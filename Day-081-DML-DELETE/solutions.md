# Day-081: Solutions - DML - DELETE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: DELETE là gì?

**DELETE:** Câu lệnh DML để xóa rows theo điều kiện.

**Tại sao cần:** Xóa rows cụ thể, có thể rollback, trigger được fire.

**Khi nào dùng:** Xóa data không còn cần, cleanup, conditional deletes.

**Hậu quả nếu DELETE sai:**
- Xóa tất cả rows nếu thiếu WHERE → disaster
- Xóa nhầm data → mất data
- Lock table lâu nếu delete lớn → downtime

---

### Câu 1.2: DELETE Variants

**WHERE clause:** Xóa rows thỏa mãn điều kiện.

**JOIN:** Xóa dựa trên data từ table khác.

**Soft delete:** Không xóa thật, đánh dấu deleted.

**Khi nào dùng:**
- WHERE: Xóa rows cụ thể
- JOIN: Xóa dựa trên data từ table khác
- Soft delete: Cần recover, audit trail

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: DELETE với WHERE Clause

**Solution:**

```sql
-- Delete single row
DELETE FROM users WHERE id = 1;

-- Delete với điều kiện
DELETE FROM orders 
WHERE status = 'cancelled' 
  AND created_at < CURRENT_DATE - INTERVAL '1 year';

-- Delete với subquery
DELETE FROM products 
WHERE category_id IN (
  SELECT id FROM categories WHERE status = 'inactive'
);
```

---

### Câu 2.2: DELETE với JOIN

**Solution:**

**PostgreSQL:**
```sql
-- Xóa orders của users đã bị deleted
DELETE FROM orders o
USING users u
WHERE o.user_id = u.id AND u.deleted_at IS NOT NULL;

-- Xóa order_items của orders đã bị cancelled
DELETE FROM order_items oi
USING orders o
WHERE oi.order_id = o.id AND o.status = 'cancelled';

-- Xóa products không còn trong bất kỳ order nào
DELETE FROM products p
WHERE NOT EXISTS (
  SELECT 1 FROM order_items oi WHERE oi.product_id = p.id
);
```

**MySQL:**
```sql
-- Xóa orders của users đã bị deleted
DELETE o FROM orders o
JOIN users u ON o.user_id = u.id
WHERE u.deleted_at IS NOT NULL;
```

**So sánh:**
- JOIN: Rõ ràng hơn, có thể optimize tốt hơn
- Subquery: Linh hoạt hơn, nhưng có thể chậm hơn

---

### Câu 2.3: Soft Delete Pattern

**Solution:**

```sql
-- Thêm column deleted_at
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMP;

-- Tạo index cho performance
CREATE INDEX idx_users_deleted_at ON users(deleted_at) WHERE deleted_at IS NULL;

-- Function để soft delete
CREATE OR REPLACE FUNCTION soft_delete_user(user_id INTEGER)
RETURNS VOID AS $$
BEGIN
  UPDATE users 
  SET deleted_at = CURRENT_TIMESTAMP 
  WHERE id = user_id AND deleted_at IS NULL;
END;
$$ LANGUAGE plpgsql;

-- View để filter deleted
CREATE VIEW active_users AS
SELECT * FROM users WHERE deleted_at IS NULL;

-- Query active users
SELECT * FROM active_users;

-- Hard delete (nếu cần)
DELETE FROM users 
WHERE deleted_at IS NOT NULL 
  AND deleted_at < CURRENT_DATE - INTERVAL '1 year';
```

**So sánh:**

| Aspect | Soft Delete | Hard Delete |
|--------|-------------|-------------|
| **Recovery** | ✅ Có thể recover | ❌ Không thể |
| **Audit** | ✅ Có audit trail | ❌ Không có |
| **Storage** | ❌ Tốn storage | ✅ Tiết kiệm |
| **Query** | ❌ Cần filter | ✅ Đơn giản |
| **Foreign keys** | ✅ Không ảnh hưởng | ❌ Cần CASCADE |

**Trade-off:**
- Soft delete: Tốn storage nhưng có thể recover
- Hard delete: Tiết kiệm storage nhưng không thể recover

**Best practice:** Dùng soft delete cho production data quan trọng.

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Xóa Dữ liệu Lớn Không Lock Table

**Solution:**

```sql
-- Batch delete strategy
DO $$
DECLARE
  batch_size INTEGER := 10000;
  total_rows INTEGER;
  deleted_rows INTEGER := 0;
  batch_count INTEGER := 0;
BEGIN
  -- Get total rows
  SELECT COUNT(*) INTO total_rows 
  FROM logs WHERE created_at < CURRENT_DATE - INTERVAL '2 years';
  
  -- Delete từng batch
  WHILE deleted_rows < total_rows LOOP
    BEGIN
      DELETE FROM logs 
      WHERE created_at < CURRENT_DATE - INTERVAL '2 years'
        AND id IN (
          SELECT id FROM logs 
          WHERE created_at < CURRENT_DATE - INTERVAL '2 years'
          ORDER BY id
          LIMIT batch_size
        );
      
      GET DIAGNOSTICS deleted_rows = ROW_COUNT;
      batch_count := batch_count + 1;
      
      COMMIT;
      
      -- Log progress
      RAISE NOTICE 'Batch %: Deleted % rows, Remaining: %', 
        batch_count, deleted_rows, total_rows - deleted_rows;
      
      -- Wait để giảm lock contention
      PERFORM pg_sleep(0.1);
    EXCEPTION
      WHEN OTHERS THEN
        ROLLBACK;
        RAISE;
    END;
  END LOOP;
END $$;
```

**Best practices:**
- Batch size: 10,000-100,000 rows
- Monitor locks: Check lock time
- Progress tracking: Log progress
- Rollback plan: Có thể rollback nếu sai

---

### Câu 3.2: Cleanup Orphan Records

**Solution:**

```sql
-- Identify orphan orders
SELECT COUNT(*) FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;

-- Delete orphan orders
DELETE FROM orders o
WHERE NOT EXISTS (
  SELECT 1 FROM users u WHERE u.id = o.user_id
);

-- Identify orphan order_items
SELECT COUNT(*) FROM order_items oi
LEFT JOIN orders o ON oi.order_id = o.id
WHERE o.id IS NULL;

-- Delete orphan order_items
DELETE FROM order_items oi
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.id = oi.order_id
);

-- Identify orphan products
SELECT COUNT(*) FROM products p
LEFT JOIN categories c ON p.category_id = c.id
WHERE c.id IS NULL;

-- Delete orphan products (hoặc set category_id = NULL)
UPDATE products 
SET category_id = NULL 
WHERE category_id NOT IN (SELECT id FROM categories);
```

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: DELETE với CTE

**Solution:**

```sql
-- Xóa duplicate users (giữ lại user đầu tiên)
WITH duplicates AS (
  SELECT id, 
         ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
  FROM users
)
DELETE FROM users
WHERE id IN (
  SELECT id FROM duplicates WHERE rn > 1
);

-- Xóa old logs nhưng giữ lại 1000 logs mới nhất mỗi user
WITH logs_to_keep AS (
  SELECT id
  FROM (
    SELECT id, 
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at DESC) AS rn
    FROM logs
  ) ranked
  WHERE rn <= 1000
)
DELETE FROM logs
WHERE id NOT IN (SELECT id FROM logs_to_keep);

-- Xóa products không có sales trong 1 năm
WITH products_no_sales AS (
  SELECT p.id
  FROM products p
  WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi
    JOIN orders o ON oi.order_id = o.id
    WHERE oi.product_id = p.id
      AND o.created_at >= CURRENT_DATE - INTERVAL '1 year'
  )
)
DELETE FROM products
WHERE id IN (SELECT id FROM products_no_sales);
```

**Khi nào dùng CTE:**
- Complex logic
- Multiple steps
- Readability

**Performance:** CTE có thể optimize tốt, nhưng cần test với EXPLAIN ANALYZE.

---

### Câu 4.2: CASCADE DELETE

**Solution:**

```sql
-- Thiết kế schema với CASCADE DELETE
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE
);

CREATE TABLE order_items (
  id SERIAL PRIMARY KEY,
  order_id INTEGER NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id INTEGER NOT NULL REFERENCES products(id)
);

-- Khi xóa user, tự động xóa orders và order_items
DELETE FROM users WHERE id = 1;
-- → Tự động xóa orders và order_items của user đó
```

**So sánh:**

| Aspect | CASCADE DELETE | Manual Delete |
|--------|----------------|---------------|
| **Convenience** | ✅ Tự động | ❌ Phải manual |
| **Safety** | ❌ Dễ xóa nhầm | ✅ Control tốt hơn |
| **Performance** | ✅ Nhanh | ❌ Chậm hơn |
| **Flexibility** | ❌ Không linh hoạt | ✅ Linh hoạt |

**Khi nào dùng:**
- CASCADE DELETE: Khi chắc chắn muốn xóa cascade
- Manual Delete: Khi cần control tốt hơn, có business logic

**Best practice:** Dùng CASCADE DELETE cho dependent data, manual delete cho critical data.

---

**Chúc mừng hoàn thành Day-081!** 🎉

**Chuẩn bị cho Day-082: Stored Procedures - Giới thiệu** 🚀

