# Day-078: Solutions - DDL - DROP TABLE, TRUNCATE, DELETE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: DROP vs TRUNCATE vs DELETE

**DROP TABLE:** Xóa table hoàn toàn (structure + data). Dùng khi không còn cần table.

**TRUNCATE:** Xóa tất cả rows nhanh, giữ structure. Dùng khi cần reset table.

**DELETE:** Xóa rows theo WHERE clause, có thể rollback. Dùng khi cần xóa có điều kiện.

**So sánh:**
- DROP: Xóa structure + data, không rollback, nhanh
- TRUNCATE: Xóa data, giữ structure, không rollback, rất nhanh
- DELETE: Xóa data có điều kiện, có rollback, chậm

---

### Câu 1.2: CASCADE Options

**CASCADE:** Xóa dependencies (views, constraints, dependent tables).

**Khi nào dùng:** Khi muốn xóa cả dependencies, cleanup toàn bộ.

**Hậu quả nếu dùng sai:** Xóa nhầm dependencies → mất data, break application.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: So sánh Performance

**Solution:**

```sql
-- Tạo table với 100,000 rows
CREATE TABLE test_data AS 
SELECT generate_series(1, 100000) AS id, 
       'data' || generate_series(1, 100000) AS value;

-- Test DELETE
BEGIN;
  DELETE FROM test_data;
ROLLBACK;  -- Rollback để test tiếp

-- Test TRUNCATE
TRUNCATE TABLE test_data;

-- Test DROP + CREATE
DROP TABLE test_data;
CREATE TABLE test_data (...);
```

**Kết quả so sánh (Illustrative / approximate for educational purposes):**

| Operation | Time |
|-----------|------|
| **DELETE** | ~5-10 giây (log từng row) |
| **TRUNCATE** | ~0.1 giây (không log) |
| **DROP + CREATE** | ~0.2 giây (xóa + tạo lại) |

**Đánh giá:**
- TRUNCATE nhanh nhất (không log)
- DELETE chậm nhất (log từng row)
- DROP + CREATE nhanh nhưng mất structure

**Khi nào dùng:**
- DELETE: Khi cần xóa có điều kiện, cần rollback
- TRUNCATE: Khi cần xóa tất cả, reset table
- DROP: Khi không còn cần table

---

### Câu 2.2: Soft Delete Pattern

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
  UPDATE users SET deleted_at = CURRENT_TIMESTAMP WHERE id = user_id;
END;
$$ LANGUAGE plpgsql;

-- View để filter deleted
CREATE VIEW active_users AS
SELECT * FROM users WHERE deleted_at IS NULL;

-- Query active users
SELECT * FROM active_users;
```

**So sánh:**

| Aspect | Soft Delete | Hard Delete |
|--------|-------------|-------------|
| **Recovery** | ✅ Có thể recover | ❌ Không thể |
| **Audit** | ✅ Có audit trail | ❌ Không có |
| **Storage** | ❌ Tốn storage | ✅ Tiết kiệm |
| **Query** | ❌ Cần filter | ✅ Đơn giản |

**Trade-off:**
- Soft delete: Tốn storage nhưng có thể recover
- Hard delete: Tiết kiệm storage nhưng không thể recover

**Best practice:** Dùng soft delete cho production data quan trọng.

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Xóa Data An toàn

**Solution:**

```sql
-- Plan: Xóa từng batch để không lock table lâu

BEGIN;
  -- Xóa batch đầu tiên (10,000 rows)
  DELETE FROM logs 
  WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
  LIMIT 10000;
  
  -- Check kết quả
  SELECT COUNT(*) FROM logs WHERE created_at < CURRENT_DATE - INTERVAL '1 year';
  
  -- Nếu OK, commit, nếu không rollback
  -- COMMIT;
  -- ROLLBACK;
END;

-- Lặp lại cho đến khi xóa hết
-- Hoặc dùng script để tự động
```

**Best practices:**
- Xóa từng batch (10,000-100,000 rows)
- Monitor lock time
- Có rollback plan
- Backup trước khi xóa lớn

---

### Câu 3.2: Cleanup Schema

**Solution:**

```sql
-- 1. Xóa table không còn dùng
DROP TABLE old_users;

-- 2. Xóa test data (dùng DELETE vì có điều kiện)
BEGIN;
  DELETE FROM products WHERE status = 'test';
  -- Check trước khi commit
  SELECT COUNT(*) FROM products WHERE status = 'test';
  COMMIT;
END;

-- 3. Reset orders table (dùng TRUNCATE vì reset toàn bộ)
TRUNCATE TABLE orders RESTART IDENTITY;
```

**Giải thích:**
- DROP: Xóa table không còn dùng
- DELETE: Xóa có điều kiện, có rollback
- TRUNCATE: Reset table, nhanh

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: CASCADE Behavior

**Solution:**

```sql
-- Test DROP CASCADE
DROP TABLE users CASCADE;
-- → Xóa users, orders, order_items, views, constraints

-- Test TRUNCATE CASCADE
TRUNCATE TABLE users CASCADE;
-- → Xóa rows trong users, orders, order_items

-- Test DELETE
DELETE FROM users WHERE id = 1;
-- → Chỉ xóa user, orders và order_items vẫn còn (orphan records)
-- → Cần FOREIGN KEY với ON DELETE CASCADE để xóa cascade
```

**Cách xóa an toàn:**

```sql
-- Option 1: Xóa từng bước (an toàn nhất)
BEGIN;
  DELETE FROM order_items WHERE order_id IN (SELECT id FROM orders WHERE user_id = 1);
  DELETE FROM orders WHERE user_id = 1;
  DELETE FROM users WHERE id = 1;
  COMMIT;
END;

-- Option 2: Dùng FOREIGN KEY với ON DELETE CASCADE
ALTER TABLE orders 
  ADD CONSTRAINT fk_orders_user_id 
  FOREIGN KEY (user_id) REFERENCES users(id) 
  ON DELETE CASCADE;
  
-- Sau đó DELETE sẽ tự động xóa cascade
DELETE FROM users WHERE id = 1;
```

---

### Câu 4.2: Recovery Strategy

**Solution:**

**Backup Strategy:**
- Full backup: Hàng ngày
- Incremental backup: Hàng giờ
- Transaction log backup: Real-time (nếu có)

**Recovery Procedure:**
1. Identify point in time cần recover
2. Restore từ backup gần nhất
3. Apply transaction logs đến point in time
4. Verify data
5. Switch to recovered database

**RTO (Recovery Time Objective):** < 1 giờ
**RPO (Recovery Point Objective):** < 15 phút

**Test Recovery:**
- Test restore trên staging hàng tháng
- Document recovery procedure
- Train team

---

**Chúc mừng hoàn thành Day-078!** 🎉

**Chuẩn bị cho Day-079: DML - INSERT** 🚀

