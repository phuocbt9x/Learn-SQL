# Day-079: Solutions - DML - INSERT

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: INSERT là gì?

**INSERT:** Câu lệnh DML để thêm rows mới vào table.

**Tại sao cần:** Thêm data mới, data entry, bulk loading, application logic.

**Khi nào dùng:** Tạo records mới, import data, sync data.

**Hậu quả nếu INSERT sai:**
- Duplicate data nếu thiếu constraints
- Invalid data nếu thiếu validation
- Performance tệ nếu không optimize bulk insert

---

### Câu 1.2: INSERT Variants

**Single vs Multiple:** Multiple rows nhanh hơn, ít round-trips hơn.

**ON CONFLICT:** UPSERT pattern, insert hoặc update nếu conflict.

**RETURNING:** Trả về inserted values, giảm round-trips.

**Khi nào dùng:**
- Single: Real-time inserts
- Multiple: Bulk inserts
- ON CONFLICT: Idempotent operations
- RETURNING: Cần lấy auto-generated id

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: INSERT Single và Multiple Rows

**Solution:**

```sql
-- Tạo table
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  stock INTEGER DEFAULT 0
);

-- Insert 1 row
INSERT INTO products (name, price, stock) 
VALUES ('Product 1', 10.99, 100);

-- Insert 5 rows
INSERT INTO products (name, price, stock) VALUES 
  ('Product 2', 20.99, 200),
  ('Product 3', 30.99, 300),
  ('Product 4', 40.99, 400),
  ('Product 5', 50.99, 500);

-- Insert 100 rows từ SELECT
INSERT INTO products (name, price, stock)
SELECT 'Product ' || generate_series(6, 105),
       random() * 100,
       random() * 1000;
```

---

### Câu 2.2: INSERT ... ON CONFLICT (UPSERT)

**Solution:**

```sql
-- Tạo table với email UNIQUE
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert lần đầu
INSERT INTO users (email, name) 
VALUES ('test@example.com', 'Test User');

-- Insert lại với ON CONFLICT DO UPDATE
INSERT INTO users (email, name) 
VALUES ('test@example.com', 'Updated Name')
ON CONFLICT (email) 
DO UPDATE SET 
  name = EXCLUDED.name,
  updated_at = CURRENT_TIMESTAMP;

-- Insert lại với ON CONFLICT DO NOTHING
INSERT INTO users (email, name) 
VALUES ('test@example.com', 'Another Name')
ON CONFLICT (email) 
DO NOTHING;
-- → Không update, giữ nguyên giá trị cũ
```

**So sánh:**
- **DO UPDATE**: Update nếu exists, insert nếu không
- **DO NOTHING**: Ignore nếu exists, insert nếu không

**Khi nào dùng:**
- DO UPDATE: Cần sync data, update nếu exists
- DO NOTHING: Chỉ insert nếu chưa có, không update

---

### Câu 2.3: INSERT ... RETURNING

**Solution:**

```sql
-- Insert và return id
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name')
RETURNING id;
-- → Trả về id vừa insert

-- Insert và return nhiều columns
INSERT INTO products (name, price, stock) 
VALUES ('Product X', 99.99, 500)
RETURNING id, name, price;
-- → Trả về id, name, price

-- Insert multiple và return tất cả ids
INSERT INTO users (email, name) VALUES 
  ('user1@example.com', 'User 1'),
  ('user2@example.com', 'User 2'),
  ('user3@example.com', 'User 3')
RETURNING id;
-- → Trả về tất cả ids
```

**So sánh với INSERT + SELECT:**
- **INSERT ... RETURNING**: 1 query, atomic
- **INSERT + SELECT**: 2 queries, có thể race condition

**Performance:** RETURNING nhanh hơn và an toàn hơn.

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Bulk Insert Optimization

**Solution:**

```sql
-- Option 1: INSERT từng row (CHẬM)
-- Không nên dùng cho bulk insert

-- Option 2: INSERT batch (1000 rows)
BEGIN;
  INSERT INTO logs (timestamp, level, message)
  SELECT CURRENT_TIMESTAMP, 'INFO', 'Message ' || generate_series(1, 1000);
COMMIT;
-- Lặp lại 1000 lần cho 1 triệu rows

-- Option 3: COPY command (PostgreSQL - NHANH NHẤT)
COPY logs (timestamp, level, message) FROM '/path/to/logs.csv' WITH CSV;
```

**Optimization:**

```sql
-- Disable indexes tạm thời
ALTER INDEX idx_logs_timestamp DISABLE;

-- Bulk insert
INSERT INTO logs (timestamp, level, message)
SELECT CURRENT_TIMESTAMP, 'INFO', 'Message ' || generate_series(1, 1000000);

-- Rebuild indexes
ALTER INDEX idx_logs_timestamp REBUILD;
```

**Kết quả so sánh (Illustrative / approximate for educational purposes):**

| Method | Time (1M rows) |
|--------|----------------|
| **INSERT từng row** | ~10 giờ |
| **INSERT batch** | ~30 phút |
| **COPY command** | ~2 phút |
| **COPY + disable indexes** | ~1 phút |

---

### Câu 3.2: Idempotent Insert

**Solution:**

```sql
-- Implement với INSERT ... ON CONFLICT
INSERT INTO products (external_id, name, price)
VALUES 
  ('ext_1', 'Product 1', 10.99),
  ('ext_2', 'Product 2', 20.99),
  ('ext_3', 'Product 3', 30.99)
ON CONFLICT (external_id) 
DO UPDATE SET 
  name = EXCLUDED.name,
  price = EXCLUDED.price,
  updated_at = CURRENT_TIMESTAMP;
```

**Test idempotency:**
```sql
-- Chạy lần 1: Insert 3 rows
-- Chạy lần 2: Update 3 rows (không duplicate)
-- Chạy lần 3: Update 3 rows (không duplicate)
-- → Idempotent!
```

**Edge cases:**
- NULL values: Handle với COALESCE
- Missing columns: Dùng DEFAULT
- Constraint violations: Handle với DO NOTHING

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: INSERT với Subquery

**Solution:**

```sql
-- Insert users từ temp_users
INSERT INTO users (email, name)
SELECT email, name FROM temp_users
WHERE email NOT IN (SELECT email FROM users);

-- Insert products với price từ pricing
INSERT INTO products (name, price)
SELECT p.name, pr.price
FROM temp_products p
JOIN pricing pr ON p.id = pr.product_id;

-- Insert orders với user_id và product_id
INSERT INTO orders (user_id, product_id, quantity)
SELECT u.id, p.id, 1
FROM temp_orders to
JOIN users u ON to.user_email = u.email
JOIN products p ON to.product_name = p.name;
```

**Performance considerations:**
- Subquery có thể chậm nếu không có indexes
- JOIN có thể expensive
- Nên test với EXPLAIN ANALYZE

---

### Câu 4.2: Conditional INSERT

**Solution:**

```sql
-- Insert chỉ nếu email chưa tồn tại
INSERT INTO users (email, name)
SELECT 'new@example.com', 'New User'
WHERE NOT EXISTS (
  SELECT 1 FROM users WHERE email = 'new@example.com'
);

-- Insert với giá trị mặc định nếu thiếu
INSERT INTO products (name, price, stock)
VALUES (
  'Product X',
  COALESCE(NULL, 0.00),  -- Default 0.00 nếu NULL
  COALESCE(NULL, 0)       -- Default 0 nếu NULL
);

-- Insert chỉ nếu user và product tồn tại
INSERT INTO orders (user_id, product_id, quantity)
SELECT u.id, p.id, 1
FROM (VALUES ('user@example.com', 'Product Name')) AS t(email, product_name)
JOIN users u ON t.email = u.email
JOIN products p ON t.product_name = p.name;
```

**Trade-offs:**
- Database-level: Atomic, nhưng phức tạp
- Application-level: Đơn giản hơn, nhưng không atomic

**Best practice:** Dùng database-level cho critical operations, application-level cho simple cases.

---

**Chúc mừng hoàn thành Day-079!** 🎉

**Chuẩn bị cho Day-080: DML - UPDATE** 🚀

