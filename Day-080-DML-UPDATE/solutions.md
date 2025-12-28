# Day-080: Solutions - DML - UPDATE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: UPDATE là gì?

**UPDATE:** Câu lệnh DML để sửa đổi data trong table.

**Tại sao cần:** Sửa đổi data, bulk updates, data correction, status changes.

**Khi nào dùng:** Update thông tin, sửa lỗi, thay đổi trạng thái.

**Hậu quả nếu UPDATE sai:**
- Update tất cả rows nếu thiếu WHERE → disaster
- Update sai data → data corruption
- Lock table lâu nếu update lớn → downtime

---

### Câu 1.2: UPDATE Variants

**WHERE clause:** Update rows thỏa mãn điều kiện.

**JOIN:** Update dựa trên data từ table khác.

**RETURNING:** Trả về updated values, giảm round-trips.

**Khi nào dùng:**
- WHERE: Update rows cụ thể
- JOIN: Update dựa trên data từ table khác
- RETURNING: Cần verify updated data

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: UPDATE với WHERE Clause

**Solution:**

```sql
-- Update single row
UPDATE users 
SET name = 'New Name', updated_at = CURRENT_TIMESTAMP 
WHERE id = 1;

-- Update với điều kiện
UPDATE orders 
SET status = 'expired' 
WHERE status = 'pending' 
  AND created_at < CURRENT_DATE - INTERVAL '30 days';

-- Update với subquery
UPDATE products 
SET price = price * 0.9 
WHERE category_id IN (
  SELECT id FROM categories WHERE status = 'inactive'
);
```

---

### Câu 2.2: UPDATE với JOIN

**Solution:**

**PostgreSQL:**
```sql
-- Update orders với total từ products
UPDATE orders o
SET total = o.quantity * p.price
FROM products p
WHERE o.product_id = p.id;

-- Update users với last_order_date
UPDATE users u
SET last_order_date = (
  SELECT MAX(created_at) FROM orders WHERE user_id = u.id
);

-- Update products với category_name
UPDATE products p
SET category_name = c.name
FROM categories c
WHERE p.category_id = c.id;
```

**MySQL:**
```sql
-- Update orders với total
UPDATE orders o
JOIN products p ON o.product_id = p.id
SET o.total = o.quantity * p.price;
```

**So sánh:**
- JOIN: Rõ ràng hơn, có thể optimize tốt hơn
- Subquery: Linh hoạt hơn, nhưng có thể chậm hơn

---

### Câu 2.3: UPDATE ... RETURNING

**Solution:**

```sql
-- Update và return updated row
UPDATE users 
SET status = 'active', updated_at = CURRENT_TIMESTAMP 
WHERE id = 1
RETURNING id, email, status, updated_at;

-- Update và return chỉ ids
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1
RETURNING id;

-- Update và return count (PostgreSQL)
WITH updated AS (
  UPDATE orders 
  SET status = 'processed' 
  WHERE status = 'pending'
  RETURNING id
)
SELECT COUNT(*) FROM updated;
```

**So sánh:**
- UPDATE ... RETURNING: 1 query, atomic
- UPDATE + SELECT: 2 queries, có thể race condition

**Performance:** RETURNING nhanh hơn và an toàn hơn.

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Update 1 Triệu Rows An toàn

**Solution:**

```sql
-- Batch update strategy
DO $$
DECLARE
  batch_size INTEGER := 10000;
  total_rows INTEGER;
  updated_rows INTEGER := 0;
  batch_count INTEGER := 0;
BEGIN
  -- Get total rows
  SELECT COUNT(*) INTO total_rows 
  FROM orders WHERE status = 'pending';
  
  -- Update từng batch
  WHILE updated_rows < total_rows LOOP
    BEGIN
      UPDATE orders 
      SET status = 'processed',
          processed_at = CURRENT_TIMESTAMP
      WHERE status = 'pending'
        AND id IN (
          SELECT id FROM orders 
          WHERE status = 'pending' 
          ORDER BY id 
          LIMIT batch_size
        );
      
      GET DIAGNOSTICS updated_rows = ROW_COUNT;
      batch_count := batch_count + 1;
      
      COMMIT;
      
      -- Log progress
      RAISE NOTICE 'Batch %: Updated % rows', batch_count, updated_rows;
      
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

### Câu 3.2: Conditional Update

**Solution:**

```sql
-- Update với CASE expression
UPDATE products 
SET price = CASE
  WHEN price < 100 THEN price * 1.10
  WHEN price >= 100 AND price <= 1000 THEN price * 1.05
  ELSE price  -- Không update nếu price > 1000
END,
updated_at = CURRENT_TIMESTAMP
WHERE price < 1000;  -- Chỉ update nếu price < 1000
```

**Test:**
```sql
-- Verify results
SELECT 
  CASE
    WHEN price < 100 THEN 'Increased 10%'
    WHEN price >= 100 AND price <= 1000 THEN 'Increased 5%'
    ELSE 'No change'
  END AS update_type,
  COUNT(*) AS count
FROM products
WHERE updated_at >= CURRENT_DATE
GROUP BY update_type;
```

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Update với CTE

**Solution:**

```sql
-- Update products với price từ pricing (chỉ nếu khác)
WITH new_prices AS (
  SELECT p.id, pr.price
  FROM products p
  JOIN pricing pr ON p.id = pr.product_id
  WHERE p.price != pr.price
)
UPDATE products p
SET price = np.price,
    updated_at = CURRENT_TIMESTAMP
FROM new_prices np
WHERE p.id = np.id;

-- Update users với last_login (chỉ nếu mới hơn)
WITH latest_logins AS (
  SELECT user_id, MAX(login_at) AS last_login
  FROM login_logs
  GROUP BY user_id
)
UPDATE users u
SET last_login = ll.last_login
FROM latest_logins ll
WHERE u.id = ll.user_id
  AND (u.last_login IS NULL OR u.last_login < ll.last_login);

-- Update orders với calculated_total
WITH order_totals AS (
  SELECT 
    o.id,
    SUM(oi.quantity * oi.price) AS calculated_total
  FROM orders o
  JOIN order_items oi ON o.id = oi.order_id
  GROUP BY o.id
)
UPDATE orders o
SET total = ot.calculated_total
FROM order_totals ot
WHERE o.id = ot.id;
```

**Khi nào dùng CTE:**
- Complex calculations
- Multiple steps
- Readability

**Performance:** CTE có thể optimize tốt, nhưng cần test với EXPLAIN ANALYZE.

---

### Câu 4.2: Update với Lock

**Solution:**

```sql
-- Update order với SELECT FOR UPDATE
BEGIN;
  SELECT * FROM orders WHERE id = 1 FOR UPDATE;
  UPDATE orders 
  SET status = 'processing' 
  WHERE id = 1;
COMMIT;

-- Update user balance với lock
BEGIN;
  SELECT balance FROM users WHERE id = 1 FOR UPDATE;
  UPDATE users 
  SET balance = balance - 100 
  WHERE id = 1 AND balance >= 100;
COMMIT;

-- Update product stock với lock
BEGIN;
  SELECT stock FROM products WHERE id = 1 FOR UPDATE;
  UPDATE products 
  SET stock = stock - 1 
  WHERE id = 1 AND stock > 0;
COMMIT;
```

**Khi nào cần lock:**
- Race conditions
- Concurrent updates
- Critical operations

**Trade-offs:**
- Lock đảm bảo consistency
- Nhưng có thể block other transactions
- Cần giữ lock ngắn nhất có thể

---

**Chúc mừng hoàn thành Day-080!** 🎉

**Chuẩn bị cho Phase 5.2!** 🚀

