# Day-079: DML - INSERT

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- INSERT single row, multiple rows
- INSERT ... ON CONFLICT (UPSERT)
- INSERT ... RETURNING
- Bulk insert optimization
- Khi nào dùng cách nào?

---

## 1️⃣ INSERT LÀ GÌ?

**INSERT** là câu lệnh DML (Data Manipulation Language) để **thêm rows mới** vào table:

```sql
-- Insert single row
INSERT INTO users (email, name) VALUES ('user@example.com', 'User Name');

-- Insert multiple rows
INSERT INTO users (email, name) VALUES 
  ('user1@example.com', 'User 1'),
  ('user2@example.com', 'User 2'),
  ('user3@example.com', 'User 3');
```

**Đặc điểm:**
- Thêm data mới vào table
- Có thể rollback (DML trong transaction)
- Trigger được fire
- Constraints được check

---

## 2️⃣ TẠI SAO TỒN TẠI INSERT?

**INSERT tồn tại để:**
- **Thêm data mới**: Tạo records mới
- **Data entry**: Nhập liệu vào database
- **Bulk loading**: Import data từ external sources
- **Application logic**: Tạo records từ application

**Nếu không có INSERT:**
- Không thể thêm data mới
- Database chỉ có structure, không có data

---

## 3️⃣ INSERT SINGLE ROW

**INSERT single row** thêm một row:

```sql
-- Specify columns
INSERT INTO users (email, name, created_at) 
VALUES ('user@example.com', 'User Name', CURRENT_TIMESTAMP);

-- Omit columns (dùng DEFAULT)
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name');
-- created_at sẽ dùng DEFAULT

-- Omit column list (phải specify tất cả columns)
INSERT INTO users 
VALUES (DEFAULT, 'user@example.com', 'User Name', CURRENT_TIMESTAMP);
```

**Khi nào dùng:**
- Insert từng row
- Application inserts
- Real-time data entry

---

## 4️⃣ INSERT MULTIPLE ROWS

**INSERT multiple rows** thêm nhiều rows cùng lúc:

```sql
-- Insert multiple rows
INSERT INTO users (email, name) VALUES 
  ('user1@example.com', 'User 1'),
  ('user2@example.com', 'User 2'),
  ('user3@example.com', 'User 3');
```

**Lợi ích:**
- Nhanh hơn insert từng row
- Một transaction thay vì nhiều transactions
- Ít round-trips hơn

**Khi nào dùng:**
- Bulk inserts
- Data migration
- Batch processing

---

## 5️⃣ INSERT ... ON CONFLICT (UPSERT)

**INSERT ... ON CONFLICT** (UPSERT) là **insert hoặc update** nếu conflict:

```sql
-- PostgreSQL syntax
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name')
ON CONFLICT (email) 
DO UPDATE SET name = EXCLUDED.name, updated_at = CURRENT_TIMESTAMP;

-- Hoặc DO NOTHING (ignore nếu conflict)
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name')
ON CONFLICT (email) 
DO NOTHING;
```

**Khi nào dùng:**
- Update nếu exists, insert nếu không
- Idempotent operations
- Sync data từ external sources

**Hậu quả nếu không dùng:**
- Phải check exists trước → race condition
- 2 queries thay vì 1 → performance tệ hơn

---

## 6️⃣ INSERT ... RETURNING

**INSERT ... RETURNING** trả về **rows vừa insert**:

```sql
-- Return inserted row
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name')
RETURNING id, email, name, created_at;

-- Return chỉ id
INSERT INTO users (email, name) 
VALUES ('user@example.com', 'User Name')
RETURNING id;
```

**Lợi ích:**
- Không cần query lại để lấy id
- Giảm round-trips
- Atomic operation

**Khi nào dùng:**
- Cần lấy auto-generated id
- Cần verify inserted data
- Application logic cần inserted values

---

## 7️⃣ BULK INSERT OPTIMIZATION

**Bulk insert** là insert **nhiều rows cùng lúc**:

```sql
-- Insert 10,000 rows
INSERT INTO users (email, name) 
SELECT 'user' || generate_series(1, 10000) || '@example.com', 
       'User ' || generate_series(1, 10000);
```

**Optimization techniques:**
1. **Batch size**: Insert từng batch (1000-10000 rows)
2. **Disable indexes**: Tạm thời disable indexes, rebuild sau
3. **Disable constraints**: Tạm thời disable constraints, enable sau
4. **Use COPY**: PostgreSQL COPY command nhanh hơn INSERT

**PostgreSQL COPY:**
```sql
COPY users (email, name) FROM '/path/to/file.csv' WITH CSV;
```

---

## 8️⃣ PRODUCTION STORY: BULK INSERT OPTIMIZATION

**Context:**
Cần import 10 triệu rows vào table `products` từ CSV file.

**Problem:**
- INSERT từng row → mất 10 giờ
- Application timeout
- Database load cao

**Investigation:**
- INSERT từng row → 1 transaction per row → overhead lớn
- Indexes được update mỗi insert → chậm
- Constraints được check mỗi insert → chậm

**Root Cause:**
- Không optimize bulk insert
- Insert từng row thay vì batch

**Fix:**

**Option 1: Batch INSERT**
```sql
-- Insert từng batch 10,000 rows
BEGIN;
  INSERT INTO products (name, price) 
  SELECT 'Product ' || generate_series(1, 10000), 
         random() * 100;
COMMIT;
-- Lặp lại cho đến hết
```

**Option 2: Disable indexes tạm thời**
```sql
-- Disable indexes
ALTER INDEX idx_products_name DISABLE;

-- Bulk insert
INSERT INTO products (name, price) 
SELECT 'Product ' || generate_series(1, 10000000), 
       random() * 100;

-- Rebuild indexes
ALTER INDEX idx_products_name REBUILD;
```

**Option 3: COPY command (PostgreSQL)**
```sql
-- COPY từ file
COPY products (name, price) FROM '/path/to/products.csv' WITH CSV;
```

**Result:**
- Option 1: Giảm từ 10 giờ xuống 2 giờ
- Option 2: Giảm từ 10 giờ xuống 30 phút
- Option 3: Giảm từ 10 giờ xuống 5 phút

**Lesson Learned:**
- Bulk insert cần optimization
- COPY command nhanh nhất cho large data
- Disable indexes tạm thời nếu cần

---

## 9️⃣ SO SÁNH: INSERT TỪNG ROW vs BATCH INSERT

**Query A: INSERT từng row**
```sql
INSERT INTO users (email, name) VALUES ('user1@example.com', 'User 1');
INSERT INTO users (email, name) VALUES ('user2@example.com', 'User 2');
-- 1000 rows...
```

**Query B: Batch INSERT**
```sql
INSERT INTO users (email, name) VALUES 
  ('user1@example.com', 'User 1'),
  ('user2@example.com', 'User 2'),
  -- ... 1000 rows
  ('user1000@example.com', 'User 1000');
```

**So sánh (1000 rows):**

| Aspect | Query A | Query B |
|--------|---------|---------|
| **Transactions** | 1000 | 1 |
| **Round-trips** | 1000 | 1 |
| **Time** | ~10 giây | ~0.5 giây |
| **Lock overhead** | Cao | Thấp |

**Kết luận:**
- Query B tốt hơn cho bulk insert
- Batch insert giảm overhead đáng kể
- Luôn dùng batch insert khi có thể

---

## 🔟 TÓM TẮT

**Key Takeaways:**
1. **INSERT**: Thêm rows mới vào table
2. **Single vs Multiple**: Multiple rows nhanh hơn
3. **ON CONFLICT**: UPSERT pattern
4. **RETURNING**: Lấy inserted values
5. **Bulk optimization**: Batch, disable indexes, COPY command

---




**Chuẩn bị cho [Day-080: DML-UPDATE](../Day-080-DML-UPDATE/theory.md)** 🚀
