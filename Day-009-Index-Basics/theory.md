# Day-009: Index - Cơ bản về chỉ mục

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Index là gì và tại sao Index làm query nhanh hơn
- B-Tree index là gì (high-level concept)
- Index Scan vs Full Table Scan - khi nào dùng gì
- Cách tạo và sử dụng Index
- Hậu quả nếu không có Index hoặc có Index không phù hợp

---

## 1️⃣ INDEX LÀ GÌ?

### **Nó là gì?**

**Index** (Chỉ mục) là một cấu trúc dữ liệu giúp database **tìm kiếm nhanh hơn** trong table.

**Analogy:** Index giống như **mục lục** trong sách:
- Không có index: Phải đọc toàn bộ sách để tìm một từ
- Có index: Xem mục lục → biết trang nào → tìm nhanh

**Ví dụ:**

```sql
-- Table users (1 triệu rows)
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100),
  name VARCHAR(100)
);

-- Query không có index
SELECT * FROM users WHERE email = 'john@example.com';
-- Phải scan TẤT CẢ 1 triệu rows → chậm (vài giây)

-- Tạo index
CREATE INDEX idx_users_email ON users(email);

-- Query với index
SELECT * FROM users WHERE email = 'john@example.com';
-- Dùng index → tìm nhanh (vài milliseconds)
```

**Đặc điểm của Index:**

1. **Tăng tốc độ tìm kiếm**: Query nhanh hơn 100-1000x
2. **Tốn storage**: Index chiếm disk space
3. **Làm chậm INSERT/UPDATE/DELETE**: Phải update index
4. **Tự động maintain**: Database tự động update index khi data thay đổi

### **Tại sao tồn tại?**

Index tồn tại để giải quyết vấn đề **"Làm thế nào tìm kiếm nhanh trong table lớn?"**

**Vấn đề không có Index:**

1. **Full Table Scan**: Phải đọc TẤT CẢ rows để tìm
2. **Chậm**: Với 1 triệu rows → mất vài giây
3. **Không scale**: Với 1 tỷ rows → mất vài phút

**Với Index:**

✅ **Index Scan**: Chỉ đọc một phần nhỏ rows
✅ **Nhanh**: Với 1 triệu rows → vài milliseconds
✅ **Scale tốt**: Với 1 tỷ rows → vẫn vài milliseconds

### **Khi nào dùng trong production?**

Index nên dùng khi:

✅ **Columns thường được query**: WHERE, JOIN, ORDER BY
✅ **Table lớn**: > 10,000 rows (với small tables, index có thể không cần)
✅ **Read-heavy**: Nhiều SELECT, ít INSERT/UPDATE/DELETE
✅ **Performance-critical**: Cần query nhanh

**KHÔNG nên tạo Index khi:**

❌ **Columns ít được query**: Không cần index
❌ **Table nhỏ**: < 1,000 rows (Full Table Scan đủ nhanh)
❌ **Write-heavy**: Nhiều INSERT/UPDATE/DELETE (index làm chậm)
❌ **Columns thường xuyên update**: Mỗi update phải update index

**Trade-off:**
- **Read performance**: Index làm SELECT nhanh hơn
- **Write performance**: Index làm INSERT/UPDATE/DELETE chậm hơn
- **Storage**: Index tốn disk space

---

## 2️⃣ TẠI SAO INDEX LÀM QUERY NHANH HƠN?

### **2.1. Full Table Scan (Không có Index)**

**Cách hoạt động:**

1. Database đọc **TẤT CẢ rows** trong table
2. So sánh từng row với điều kiện WHERE
3. Trả về rows match

**Ví dụ:**

```sql
-- Table users: 1 triệu rows
SELECT * FROM users WHERE email = 'john@example.com';

-- Full Table Scan:
-- 1. Đọc row 1 → check email → không match
-- 2. Đọc row 2 → check email → không match
-- 3. ...
-- 1,000,000. Đọc row 1,000,000 → check email → MATCH!
-- → Phải đọc 1 triệu rows → chậm (vài giây)
```

**Time complexity:** O(n) - phải check tất cả n rows

**Performance:**
- 1,000 rows: ~1 ms
- 100,000 rows: ~100 ms
- 1,000,000 rows: ~1,000 ms (1 giây)
- 1,000,000,000 rows: ~1,000,000 ms (16 phút!)

---

### **2.2. Index Scan (Có Index)**

**Cách hoạt động:**

1. Database dùng **Index** để tìm rows match
2. Chỉ đọc **một phần nhỏ rows** cần thiết
3. Trả về kết quả

**Ví dụ:**

```sql
-- Table users: 1 triệu rows
-- Index trên email
SELECT * FROM users WHERE email = 'john@example.com';

-- Index Scan:
-- 1. Dùng index → tìm 'john@example.com'
-- 2. Index trả về row number (ví dụ: row 500,000)
-- 3. Đọc row 500,000 → MATCH!
-- → Chỉ đọc 1 row → nhanh (vài milliseconds)
```

**Time complexity:** O(log n) - binary search trong index

**Performance:**
- 1,000 rows: ~0.01 ms
- 100,000 rows: ~0.1 ms
- 1,000,000 rows: ~0.2 ms
- 1,000,000,000 rows: ~0.3 ms (vẫn nhanh!)

---

### **2.3. So sánh**

| Tiêu chí | Full Table Scan | Index Scan |
|----------|------------------|------------|
| **Time complexity** | O(n) | O(log n) |
| **Rows đọc** | Tất cả rows | Một phần nhỏ |
| **Performance (1M rows)** | ~1 giây | ~0.2 ms |
| **Performance (1B rows)** | ~16 phút | ~0.3 ms |
| **Khi nào dùng** | Table nhỏ, không có index | Table lớn, có index |

**Kết luận:** Index làm query nhanh hơn **100-1000x** với table lớn!

---

## 3️⃣ B-TREE INDEX LÀ GÌ? (HIGH-LEVEL)

### **Nó là gì?**

**B-Tree** (Balanced Tree) là cấu trúc dữ liệu phổ biến nhất cho Index trong databases.

**High-level concept:**

B-Tree là một **cây nhị phân cân bằng** (balanced binary tree), nhưng mỗi node có thể có **nhiều children** (không chỉ 2).

**Cấu trúc:**

```
                    [50]
                   /    \
              [25]      [75]
             /   \      /   \
          [10] [30]  [60] [90]
```

**Cách hoạt động:**

1. **Root node**: Node gốc chứa giá trị trung tâm
2. **Branch nodes**: Nodes trung gian chứa ranges
3. **Leaf nodes**: Nodes lá chứa actual data hoặc pointers đến data

**Ví dụ tìm kiếm:**

```
Tìm giá trị 30:
1. Bắt đầu từ root [50]
2. 30 < 50 → đi sang trái [25]
3. 30 > 25 → đi sang phải [30]
4. Tìm thấy [30] → trả về data
```

**Time complexity:** O(log n) - chỉ cần log(n) comparisons

---

### **Tại sao dùng B-Tree?**

**Ưu điểm:**

✅ **Balanced**: Cây cân bằng → tìm kiếm luôn O(log n)
✅ **Efficient**: Tìm kiếm nhanh, không cần scan toàn bộ
✅ **Range queries**: Hỗ trợ tốt range queries (WHERE x > 10)
✅ **Sorted**: Data được sort → ORDER BY nhanh

**So sánh với các cấu trúc khác:**

| Cấu trúc | Time complexity | Khi nào dùng |
|----------|----------------|--------------|
| **B-Tree** | O(log n) | Default choice, range queries |
| **Hash Index** | O(1) | Exact match only, không hỗ trợ range |
| **Full Table Scan** | O(n) | Table nhỏ, không có index |

**Lưu ý:** B-Tree là high-level concept. Database implementations có thể optimize thêm (B+ Tree, etc.), nhưng concept cơ bản là B-Tree.

---

## 4️⃣ INDEX SCAN VS FULL TABLE SCAN

### **4.1. Index Scan**

**Nó là gì?**

**Index Scan** là khi database dùng Index để tìm rows, sau đó đọc rows từ table.

**Cách hoạt động:**

1. **Index lookup**: Dùng index để tìm rows match
2. **Row lookup**: Đọc rows từ table (dựa trên pointers từ index)
3. **Return results**: Trả về kết quả

**Ví dụ:**

```sql
-- Có index trên email
SELECT * FROM users WHERE email = 'john@example.com';

-- Execution:
-- 1. Index Scan: Tìm 'john@example.com' trong index → row 500,000
-- 2. Table lookup: Đọc row 500,000 từ table
-- 3. Return: Trả về row 500,000
```

**Khi nào xảy ra:**

✅ **Có index trên column trong WHERE**
✅ **Index có thể dùng được** (không bị disable bởi function, etc.)

**Performance:** Rất nhanh (O(log n))

---

### **4.2. Full Table Scan**

**Nó là gì?**

**Full Table Scan** (hay Sequential Scan) là khi database đọc **TẤT CẢ rows** trong table.

**Cách hoạt động:**

1. **Read all rows**: Đọc từng row một
2. **Check condition**: So sánh với điều kiện WHERE
3. **Return matches**: Trả về rows match

**Ví dụ:**

```sql
-- Không có index trên email
SELECT * FROM users WHERE email = 'john@example.com';

-- Execution:
-- 1. Full Table Scan: Đọc row 1 → check → không match
-- 2. Đọc row 2 → check → không match
-- 3. ...
-- 1,000,000. Đọc row 1,000,000 → check → MATCH!
```

**Khi nào xảy ra:**

✅ **Không có index** trên column trong WHERE
✅ **Index không thể dùng** (ví dụ: function trong WHERE)
✅ **Table nhỏ** (database quyết định Full Table Scan nhanh hơn)

**Performance:** Chậm với table lớn (O(n))

---

### **4.3. Khi nào dùng gì?**

**Database tự động quyết định:**

Database query planner tự động chọn Index Scan hay Full Table Scan dựa trên:

1. **Có index không?**: Nếu không có → Full Table Scan
2. **Table size**: Table nhỏ → có thể Full Table Scan nhanh hơn
3. **Selectivity**: Nếu query trả về > 50% rows → Full Table Scan có thể nhanh hơn
4. **Statistics**: Database dùng statistics để estimate cost

**Ví dụ:**

```sql
-- Table nhỏ (100 rows)
SELECT * FROM users WHERE email = 'john@example.com';
-- Database có thể chọn Full Table Scan (nhanh hơn index overhead)

-- Table lớn (1 triệu rows)
SELECT * FROM users WHERE email = 'john@example.com';
-- Database sẽ chọn Index Scan (nhanh hơn nhiều)
```

**Best practice:**

- **Tạo index** trên columns thường query
- **Để database quyết định**: Database thường chọn đúng
- **Monitor query plans**: Dùng EXPLAIN để xem database chọn gì

---

## 5️⃣ CÁCH TẠO VÀ SỬ DỤNG INDEX

### **5.1. Tạo Index**

**Cú pháp:**

```sql
CREATE INDEX index_name ON table_name(column_name);
```

**Ví dụ:**

```sql
-- Tạo index trên email
CREATE INDEX idx_users_email ON users(email);

-- Tạo index trên nhiều columns (composite index)
CREATE INDEX idx_users_name_email ON users(name, email);

-- Tạo unique index
CREATE UNIQUE INDEX idx_users_email_unique ON users(email);
```

**Lưu ý:**

- **Index name**: Nên đặt tên rõ ràng (ví dụ: `idx_users_email`)
- **Composite index**: Index trên nhiều columns (sẽ học chi tiết ở Day sau)
- **Unique index**: Đảm bảo values unique (tương tự UNIQUE constraint)

---

### **5.2. Sử dụng Index**

**Index được dùng tự động:**

Database tự động dùng index khi:

✅ **WHERE clause**: `WHERE email = 'john@example.com'`
✅ **JOIN**: `JOIN users ON users.id = orders.user_id` (nếu có index trên id)
✅ **ORDER BY**: `ORDER BY email` (nếu có index trên email)

**Ví dụ:**

```sql
-- Index được dùng tự động
SELECT * FROM users WHERE email = 'john@example.com';
-- Database tự động dùng idx_users_email

-- Index được dùng trong JOIN
SELECT * FROM orders o
JOIN users u ON o.user_id = u.id;
-- Database tự động dùng index trên users.id (Primary Key)
```

**Kiểm tra index có được dùng không:**

```sql
-- Dùng EXPLAIN để xem query plan
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';

-- Kết quả sẽ show:
-- Index Scan using idx_users_email on users
-- hoặc
-- Seq Scan on users (nếu không dùng index)
```

---

### **5.3. Xóa Index**

**Cú pháp:**

```sql
DROP INDEX index_name;
```

**Ví dụ:**

```sql
DROP INDEX idx_users_email;
```

**Khi nào xóa Index:**

✅ **Index không được dùng**: Monitor và thấy index không được dùng
✅ **Index làm chậm writes**: Nếu INSERT/UPDATE/DELETE quá chậm
✅ **Storage optimization**: Nếu cần tiết kiệm storage

**Lưu ý:** Xóa index có thể làm queries chậm hơn → cẩn thận!

---

## 6️⃣ PRODUCTION STORY: QUERY TỪ 30S → 0.1S NHỜ ĐÚNG INDEX

### **Context**

Startup e-commerce có table `orders` với 10 triệu orders:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  product_id INT,
  total_amount DECIMAL(10, 2),
  status VARCHAR(20),
  created_at TIMESTAMP
);
```

**Business requirement:** Query tìm orders của user cụ thể.

### **Vấn đề xuất hiện**

**Tháng 1: Query chậm**

Query tìm orders của user:

```sql
SELECT * FROM orders WHERE user_id = 12345;
-- Mất 30 giây!
```

**Vấn đề:**
- Không có index trên `user_id`
- Full Table Scan → phải scan 10 triệu rows
- Query timeout trong production

**Tháng 2: Users complaints**

Users phàn nàn:
- Dashboard load chậm
- "My Orders" page timeout
- Support nhận nhiều tickets

**Tháng 3: Database load cao**

Database server:
- CPU: 90% (do scan nhiều rows)
- I/O: 95% (do đọc nhiều data từ disk)
- Queries queue: 100+ queries đang chờ

### **Investigation**

**Bước 1: Analyze query**

```sql
-- Check query plan
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

Kết quả:
```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=100)
  Filter: (user_id = 12345)
Execution Time: 30000.123 ms
```

**Phân tích:**
- **Seq Scan**: Full Table Scan (không có index)
- **Execution Time**: 30 giây (quá chậm!)

**Bước 2: Check indexes**

```sql
-- Check indexes trên orders table
SELECT indexname, indexdef 
FROM pg_indexes 
WHERE tablename = 'orders';
```

Kết quả: Chỉ có index trên `id` (Primary Key), **KHÔNG có index trên `user_id`**!

**Root cause:**
1. Không có index trên `user_id`
2. Full Table Scan → phải scan 10 triệu rows
3. Query chậm → timeout

### **Fix**

**Fix 1: Tạo index**

```sql
-- Tạo index trên user_id
CREATE INDEX idx_orders_user_id ON orders(user_id);
-- Mất ~5 phút để build index (một lần duy nhất)
```

**Fix 2: Verify index được dùng**

```sql
-- Check query plan sau khi có index
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

Kết quả:
```
Index Scan using idx_orders_user_id on orders (cost=0.43..8.45 rows=100 width=100)
  Index Cond: (user_id = 12345)
Execution Time: 0.123 ms
```

**Phân tích:**
- **Index Scan**: Dùng index (không còn Full Table Scan)
- **Execution Time**: 0.123 ms (từ 30 giây → 0.1 ms!)
- **Nhanh hơn 240,000x!**

**Fix 3: Tạo thêm indexes cho queries khác**

```sql
-- Index cho status queries
CREATE INDEX idx_orders_status ON orders(status);

-- Index cho date range queries
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

### **Kết quả**

✅ **Index created**: Index trên `user_id` → query nhanh hơn 240,000x
✅ **Query performance**: Từ 30 giây → 0.1 ms
✅ **Database load**: CPU giảm từ 90% → 20%, I/O giảm từ 95% → 30%
✅ **User experience**: Dashboard load nhanh, không còn timeout

**Performance metrics:**

| Metric | Trước | Sau | Cải thiện |
|--------|-------|-----|-----------|
| **Query time** | 30 giây | 0.1 ms | 240,000x |
| **CPU usage** | 90% | 20% | 4.5x |
| **I/O usage** | 95% | 30% | 3.2x |
| **Queries queue** | 100+ | 0 | ∞ |

### **Lesson Learned**

1. **LUÔN tạo index** trên Foreign Keys và columns thường query
2. **Monitor query performance**: Dùng EXPLAIN để analyze queries
3. **Index trên WHERE columns**: Columns trong WHERE clause cần index
4. **Trade-off**: Index tốn storage và làm chậm writes, nhưng làm nhanh reads
5. **Test indexes**: Tạo index và test performance trước khi deploy

---

## 7️⃣ BEST PRACTICES

### **7.1. Khi nào tạo Index?**

**Nên tạo index khi:**

✅ **Foreign Keys**: Index trên Foreign Key columns (để JOIN nhanh)
✅ **WHERE columns**: Columns thường dùng trong WHERE clause
✅ **JOIN columns**: Columns thường dùng trong JOIN
✅ **ORDER BY columns**: Columns thường dùng trong ORDER BY
✅ **Table lớn**: > 10,000 rows

**KHÔNG nên tạo index khi:**

❌ **Table nhỏ**: < 1,000 rows (Full Table Scan đủ nhanh)
❌ **Write-heavy**: Nhiều INSERT/UPDATE/DELETE (index làm chậm)
❌ **Columns ít được query**: Không cần index
❌ **Columns thường xuyên update**: Mỗi update phải update index

---

### **7.2. Index Naming Convention**

**Best practice:**

```sql
-- Format: idx_<table>_<column>
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_products_category ON products(category);
```

**Lưu ý:**
- Tên rõ ràng, dễ hiểu
- Consistent naming convention
- Dễ maintain và debug

---

### **7.3. Monitor Index Usage**

**Check index có được dùng không:**

```sql
-- PostgreSQL: Check index usage
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan as index_scans
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan;
```

**Nếu index không được dùng:**
- Có thể xóa để tiết kiệm storage
- Nhưng cẩn thận - có thể cần cho queries hiếm

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **Index** giúp tìm kiếm nhanh hơn 100-1000x
2. **B-Tree** là cấu trúc phổ biến cho Index (O(log n))
3. **Index Scan** nhanh hơn Full Table Scan rất nhiều
4. **Tạo index** trên Foreign Keys và columns thường query
5. **Trade-off**: Index làm SELECT nhanh, nhưng làm INSERT/UPDATE/DELETE chậm

### **Best Practices**

✅ **Tạo index trên Foreign Keys**: Để JOIN nhanh
✅ **Tạo index trên WHERE columns**: Columns thường query
✅ **Monitor index usage**: Đảm bảo index được dùng
✅ **Test performance**: Test queries trước và sau khi tạo index
✅ **Consider trade-offs**: Index tốn storage và làm chậm writes

### **Câu hỏi tự kiểm tra**

1. Index là gì? Tại sao Index làm query nhanh hơn?
2. B-Tree index là gì? (High-level concept)
3. Index Scan vs Full Table Scan - khi nào dùng gì?
4. Khi nào nên tạo Index? Khi nào không nên?
5. Làm thế nào kiểm tra index có được dùng không?

---

**Chuẩn bị cho Day-010: Logical vs Physical Design** 🚀

