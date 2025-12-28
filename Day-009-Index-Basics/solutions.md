# Day-009: Solutions - Index (Cơ bản)

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Index là gì?

**Đáp án:**

**Index là gì?**

Index (Chỉ mục) là một cấu trúc dữ liệu giúp database tìm kiếm nhanh hơn trong table.

**Tại sao Index làm query nhanh hơn?**

1. **B-Tree structure**: Index dùng B-Tree → tìm kiếm O(log n) thay vì O(n)
2. **Ít rows đọc**: Chỉ đọc một phần nhỏ rows cần thiết
3. **Sorted data**: Data được sort → tìm kiếm nhanh

**Index tốn gì?**

1. **Storage**: Index chiếm disk space (có thể 10-30% của table size)
2. **Write performance**: INSERT/UPDATE/DELETE phải update index → chậm hơn
3. **Memory**: Index được load vào memory → tốn memory

---

### Câu 1.2: B-Tree Index

**a) B-Tree index là gì?**

**B-Tree** (Balanced Tree) là cấu trúc dữ liệu phổ biến nhất cho Index.

**High-level concept:**
- Cây nhị phân cân bằng
- Mỗi node có thể có nhiều children
- Data được sort → tìm kiếm nhanh

**b) Tại sao database dùng B-Tree?**

**Lý do:**
- ✅ Balanced → tìm kiếm luôn O(log n)
- ✅ Efficient → nhanh, không cần scan toàn bộ
- ✅ Range queries → hỗ trợ tốt range queries
- ✅ Sorted → ORDER BY nhanh

**c) Time complexity:**

**O(log n)**: Chỉ cần log(n) comparisons để tìm

**Ví dụ:**
- 1,000 rows: ~10 comparisons
- 1,000,000 rows: ~20 comparisons
- 1,000,000,000 rows: ~30 comparisons

**d) B-Tree vs Full Table Scan:**

| Tiêu chí | B-Tree | Full Table Scan |
|----------|--------|-----------------|
| **Time complexity** | O(log n) | O(n) |
| **Rows đọc** | Một phần nhỏ | Tất cả |
| **Performance** | Nhanh | Chậm với table lớn |

---

### Câu 1.3: Index Scan vs Full Table Scan

**a) Index Scan:**

**Index Scan** là khi database dùng Index để tìm rows.

**Xảy ra khi:**
- ✅ Có index trên column trong WHERE
- ✅ Index có thể dùng được

**b) Full Table Scan:**

**Full Table Scan** là khi database đọc TẤT CẢ rows.

**Xảy ra khi:**
- ✅ Không có index
- ✅ Table nhỏ (database quyết định scan nhanh hơn)
- ✅ Query trả về > 50% rows (scan có thể nhanh hơn)

**c) So sánh performance:**

| Tiêu chí | Index Scan | Full Table Scan |
|----------|------------|-----------------|
| **Time complexity** | O(log n) | O(n) |
| **Performance (1M rows)** | ~0.2 ms | ~1 giây |
| **Performance (1B rows)** | ~0.3 ms | ~16 phút |

**d) Database tự động chọn:**

**Đáp án: ĐÚNG**

Database query planner tự động chọn dựa trên:
- Có index không?
- Table size
- Selectivity
- Statistics

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Query chậm do thiếu Index

**a) Tại sao query chậm?**

**Lý do:**
- Không có index trên `user_id`
- Full Table Scan → phải scan 1 triệu rows
- Time complexity: O(n) → chậm

**b) Làm thế nào để query nhanh hơn?**

**Tạo index:**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**c) Command tạo index:**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**d) Sau khi tạo index:**

**Ước tính:**
- Trước: 5 giây (Full Table Scan)
- Sau: ~0.1 ms (Index Scan)
- **Nhanh hơn ~50,000x!**

---

### Câu 2.2: Index không được dùng

**a) Tại sao index không được dùng?**

**Lý do:**
- `UPPER(email)` là function → index không thể dùng
- Database phải tính UPPER() cho mọi row → không thể dùng index

**b) Làm thế nào để query dùng index?**

**Option 1: Query không dùng function**

```sql
-- ✅ ĐÚNG: Không dùng function
SELECT * FROM users WHERE email = 'john@example.com';
-- Hoặc
SELECT * FROM users WHERE LOWER(email) = 'john@example.com';
-- Nhưng vẫn phải tính LOWER() → không dùng index
```

**Option 2: Normalize data**

```sql
-- Lưu email đã lowercase
ALTER TABLE users ADD COLUMN email_lower VARCHAR(100);
UPDATE users SET email_lower = LOWER(email);
CREATE INDEX idx_users_email_lower ON users(email_lower);

-- Query
SELECT * FROM users WHERE email_lower = 'john@example.com';
```

**c) Có cách nào tạo index để hỗ trợ UPPER() không?**

**Đáp án: CÓ - Expression Index**

```sql
-- Tạo index trên expression
CREATE INDEX idx_users_email_upper ON users(UPPER(email));

-- Query
SELECT * FROM users WHERE UPPER(email) = 'JOHN@EXAMPLE.COM';
-- Index có thể dùng được!
```

---

### Câu 2.3: Quá nhiều Indexes

**a) Vấn đề:**

1. **Storage**: Tốn nhiều disk space
2. **Write performance**: INSERT/UPDATE/DELETE chậm (phải update nhiều indexes)
3. **Maintenance**: Khó maintain, khó optimize

**b) Làm thế nào biết index nào được dùng?**

**PostgreSQL:**

```sql
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan as index_scans
FROM pg_stat_user_indexes
WHERE tablename = 'products'
ORDER BY idx_scan;
```

**c) Có nên xóa indexes không dùng không?**

**Đáp án: CÓ, nhưng cẩn thận**

**Nên xóa khi:**
- ✅ Index không được dùng (idx_scan = 0)
- ✅ Index làm chậm writes đáng kể
- ✅ Storage quan trọng

**KHÔNG nên xóa khi:**
- ❌ Index được dùng cho queries hiếm (nhưng quan trọng)
- ❌ Index cho future queries
- ❌ Không chắc chắn

**Best practice:**
- Monitor index usage trong vài tuần
- Xóa indexes không dùng, nhưng giữ backup
- Test queries sau khi xóa index

---

## 🧠 BÀI TẬP 3: THIẾT KẾ INDEX

### Câu 3.1: E-commerce Orders

**a) CREATE INDEX statements:**

```sql
-- Index cho WHERE user_id = X
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Index cho WHERE status = 'pending'
CREATE INDEX idx_orders_status ON orders(status);

-- Index cho WHERE created_at > '2024-01-01'
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Index cho ORDER BY created_at DESC (có thể dùng idx_orders_created_at)
-- Không cần index riêng, idx_orders_created_at đã hỗ trợ ORDER BY
```

**b) Giải thích:**

- **`idx_orders_user_id`**: Foreign Key → cần index để JOIN nhanh
- **`idx_orders_status`**: Thường query theo status
- **`idx_orders_created_at`**: Date range queries, ORDER BY

**c) Query `WHERE user_id = X AND status = 'pending'`:**

**Option 1: Composite Index**

```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

**Option 2: Dùng indexes riêng**

```sql
-- Database có thể dùng cả 2 indexes
-- idx_orders_user_id và idx_orders_status
```

**Recommendation:** Composite index tốt hơn cho queries với nhiều conditions.

---

### Câu 3.2: Users Table

**a) CREATE INDEX statements:**

```sql
-- Index cho WHERE email = X (login)
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Index cho WHERE name LIKE 'John%'
CREATE INDEX idx_users_name ON users(name);

-- Index cho ORDER BY created_at DESC
CREATE INDEX idx_users_created_at ON users(created_at DESC);
```

**b) Index trên `name` có hỗ trợ `LIKE 'John%'` không?**

**Đáp án:**

- **`LIKE 'John%'`**: ✅ CÓ (prefix match)
- **`LIKE '%John'`**: ❌ KHÔNG (suffix match, phải scan toàn bộ)

**Lý do:**
- Index được sort → prefix match nhanh
- Suffix match không thể dùng index → phải scan

**c) Query `WHERE email = X AND name = Y`:**

**Option 1: Composite Index**

```sql
CREATE INDEX idx_users_email_name ON users(email, name);
```

**Option 2: Dùng indexes riêng**

```sql
-- Database có thể dùng idx_users_email (selective hơn)
-- Sau đó filter theo name
```

**Recommendation:** Composite index nếu cả 2 conditions thường dùng cùng lúc.

---

### Câu 3.3: Products với Categories

**a) CREATE INDEX statements:**

```sql
-- Index cho WHERE category_id = X
CREATE INDEX idx_products_category ON products(category_id);

-- Index cho WHERE price > 100
CREATE INDEX idx_products_price ON products(price);

-- Index cho ORDER BY price ASC
-- Không cần index riêng, idx_products_price đã hỗ trợ ORDER BY
```

**b) Index trên `name` có hỗ trợ `LIKE '%laptop%'` không?**

**Đáp án: KHÔNG**

**Lý do:**
- `LIKE '%laptop%'` là substring match (có ký tự ở đầu và cuối)
- Index không thể dùng cho substring match → phải Full Table Scan

**Giải pháp:**
- Dùng Full-text search (sẽ học ở Day sau)
- Hoặc normalize data (tách thành words, tạo index)

**c) Query `WHERE category_id = X AND price > 100`:**

**Option 1: Composite Index**

```sql
CREATE INDEX idx_products_category_price ON products(category_id, price);
```

**Option 2: Dùng indexes riêng**

```sql
-- Database có thể dùng idx_products_category
-- Sau đó filter theo price
```

**Recommendation:** Composite index tốt hơn nếu cả 2 conditions thường dùng cùng lúc.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Index và Performance Trade-offs

**a) So sánh:**

| Tiêu chí | Không có Index | Có Index |
|----------|----------------|----------|
| **SELECT** | ❌ Chậm (O(n)) | ✅ Nhanh (O(log n)) |
| **INSERT** | ✅ Nhanh | ❌ Chậm hơn (phải update index) |
| **UPDATE** | ✅ Nhanh | ❌ Chậm hơn (phải update index) |
| **DELETE** | ✅ Nhanh | ❌ Chậm hơn (phải update index) |
| **Storage** | ✅ Tiết kiệm | ❌ Tốn (10-30% table size) |

**b) Khi nào Option A tốt hơn? Option B tốt hơn?**

**Option A (Không có index) tốt hơn khi:**
- ✅ Write-heavy (nhiều INSERT/UPDATE/DELETE)
- ✅ Table nhỏ (< 1,000 rows)
- ✅ Columns ít được query

**Option B (Có index) tốt hơn khi:**
- ✅ Read-heavy (nhiều SELECT)
- ✅ Table lớn (> 10,000 rows)
- ✅ Columns thường được query

**c) Trade-offs:**

**Index làm:**
- ✅ SELECT nhanh hơn
- ❌ INSERT/UPDATE/DELETE chậm hơn
- ❌ Tốn storage

**Best practice:**
- Tạo index trên columns thường query
- Monitor write performance
- Balance giữa read và write performance

---

### Câu 4.2: Index Selectivity

**a) Index nào hiệu quả hơn?**

**Đáp án: Index trên `email` hiệu quả hơn**

**Lý do:**
- **Selectivity**: `email` có 1 triệu giá trị unique → selectivity cao
- **`country`**: Chỉ có 10 giá trị → selectivity thấp
- **Index selectivity cao** → hiệu quả hơn (ít rows match hơn)

**b) Selectivity là gì?**

**Selectivity** là tỷ lệ unique values / total rows.

**Ví dụ:**
- `email`: 1M unique / 1M rows = 100% selectivity (rất cao)
- `country`: 10 unique / 1M rows = 0.001% selectivity (rất thấp)

**Tại sao quan trọng:**
- Selectivity cao → index hiệu quả hơn
- Selectivity thấp → index ít hiệu quả (nhiều rows match)

**c) Nếu query trả về 500,000 rows:**

**Đáp án: Index KHÔNG hiệu quả**

**Lý do:**
- Trả về 50% rows → Full Table Scan có thể nhanh hơn
- Index phải đọc 500,000 rows → không hiệu quả
- Database có thể chọn Full Table Scan thay vì Index Scan

**Rule of thumb:**
- Index hiệu quả khi query trả về < 5-10% rows
- Index ít hiệu quả khi query trả về > 50% rows

---

### Câu 4.3: Index và JOIN Performance

**a) Index trên `orders.user_id` có ảnh hưởng không?**

**Đáp án: CÓ, rất quan trọng**

**Lý do:**
- JOIN cần tìm matching rows trong `orders` table
- Index trên `user_id` → tìm nhanh hơn
- Không có index → phải scan toàn bộ `orders` table

**b) Index trên `users.id` có ảnh hưởng không?**

**Đáp án: CÓ (nhưng tự động có)**

**Lý do:**
- `users.id` là Primary Key → tự động có index
- Index trên Primary Key → JOIN nhanh

**c) Nếu không có index trên `orders.user_id`:**

**Hậu quả:**
- JOIN phải scan toàn bộ `orders` table
- Với 10 triệu orders → rất chậm
- Time complexity: O(n × m) → có thể timeout

**d) Best practices:**

1. **Index trên Foreign Keys**: Luôn tạo index trên Foreign Key columns
2. **Index trên JOIN columns**: Columns thường dùng trong JOIN
3. **Composite indexes**: Nếu JOIN với nhiều conditions

---

### Câu 4.4: Index Maintenance

**a) Index có được update tự động không?**

**Đáp án: CÓ**

**Lý do:**
- Database tự động update index khi INSERT/UPDATE/DELETE
- Không cần manual update

**b) INSERT 100,000 orders có ảnh hưởng không?**

**Đáp án: CÓ**

**Hậu quả:**
- Mỗi INSERT phải update index → chậm hơn
- 100,000 INSERTs → phải update index 100,000 lần
- Có thể mất vài phút thay vì vài giây

**c) Làm thế nào optimize INSERT performance?**

**Options:**

1. **Disable index tạm thời** (nếu có thể):
   ```sql
   -- Disable index
   ALTER INDEX idx_orders_user_id DISABLE;
   -- Bulk insert
   INSERT INTO orders ...;
   -- Re-enable index
   ALTER INDEX idx_orders_user_id REBUILD;
   ```

2. **Batch inserts**: Insert nhiều rows cùng lúc (faster)

3. **Consider index later**: Tạo index sau khi bulk insert

**d) Có nên disable index khi bulk insert không?**

**Đáp án: CÓ, nếu có thể**

**Lý do:**
- Bulk insert → disable index → insert nhanh hơn
- Sau đó rebuild index → nhanh hơn update từng row

**Trade-off:**
- Queries trong lúc disable index → không dùng index được
- Phải rebuild index sau → tốn thời gian

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Index

**a) Index trên `users.email`:**

```sql
CREATE INDEX idx_users_email ON users(email);
```

**b) Index trên `orders.user_id`:**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**c) Index trên `products.category_id`:**

```sql
CREATE INDEX idx_products_category ON products(category_id);
```

**d) Unique index trên `users.email`:**

```sql
CREATE UNIQUE INDEX idx_users_email_unique ON users(email);
```

---

### Câu 5.2: Analyze Query Plan

**a) EXPLAIN statement:**

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

**b) Làm thế nào biết query có dùng index không?**

**Check output:**
- **Index Scan**: Có dùng index
- **Seq Scan** hoặc **Full Table Scan**: Không dùng index

**Ví dụ output:**
```
Index Scan using idx_orders_user_id on orders
  Index Cond: (user_id = 12345)
Execution Time: 0.123 ms
```

**c) Nếu query không dùng index:**

**Làm thế nào:**
1. **Tạo index**: `CREATE INDEX idx_orders_user_id ON orders(user_id);`
2. **Check statistics**: Database có thể cần update statistics
3. **Force index** (nếu database hỗ trợ): `SELECT ... FROM orders USE INDEX (idx_orders_user_id) WHERE ...`

---

### Câu 5.3: Monitor Index Usage

**a) List tất cả indexes:**

**PostgreSQL:**
```sql
SELECT indexname, indexdef 
FROM pg_indexes 
WHERE tablename = 'orders';
```

**MySQL:**
```sql
SHOW INDEXES FROM orders;
```

**b) Check index usage:**

**PostgreSQL:**
```sql
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan as index_scans,
  idx_tup_read as tuples_read,
  idx_tup_fetch as tuples_fetched
FROM pg_stat_user_indexes
WHERE tablename = 'orders';
```

**c) Tìm indexes không được dùng:**

```sql
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan
FROM pg_stat_user_indexes
WHERE tablename = 'orders'
  AND idx_scan = 0;  -- Không được dùng
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Index là gì?**
   - Cấu trúc dữ liệu giúp tìm kiếm nhanh hơn
   - B-Tree structure → O(log n)

2. **B-Tree index:**
   - Balanced tree structure
   - Time complexity: O(log n)

3. **Index Scan vs Full Table Scan:**
   - Index Scan: O(log n), nhanh
   - Full Table Scan: O(n), chậm

4. **Khi nào tạo index:**
   - Columns thường query, table lớn, read-heavy

5. **Kiểm tra index usage:**
   - Dùng EXPLAIN để xem query plan
   - Monitor index statistics

---

### Câu 6.2: Hệ thống e-commerce

**a) Indexes:**

```sql
-- Users
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Products
CREATE INDEX idx_products_category ON products(category_id);

-- Orders
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Order Items
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

**b) Giải thích:**

- **`idx_users_email`**: Login query → cần unique index
- **`idx_products_category`**: Filter products theo category
- **`idx_orders_user_id`**: Foreign Key → cần index để JOIN nhanh
- **`idx_orders_status`**: Filter orders theo status
- **`idx_orders_created_at`**: Date range queries
- **`idx_order_items_*`**: Foreign Keys → cần indexes

**c) Storage ước tính:**

- Users indexes: ~10 MB
- Products indexes: ~5 MB
- Orders indexes: ~50 MB
- Order items indexes: ~100 MB
- **Tổng: ~165 MB** (ước tính, ~10-30% của table size)

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: Partial Index

**a) Partial Index là gì?**

**Partial Index** là index chỉ trên một phần data (với WHERE condition).

**Ví dụ:**
```sql
CREATE INDEX idx_orders_active ON orders(user_id) 
WHERE status = 'active';
-- Chỉ index orders có status = 'active'
```

**b) Khi nào nên dùng?**

**Khi:**
- ✅ Chỉ query một phần data (ví dụ: chỉ active orders)
- ✅ Tiết kiệm storage (index nhỏ hơn)
- ✅ Performance tốt hơn (index nhỏ → nhanh hơn)

**c) Ví dụ:**

```sql
-- Chỉ index active orders
CREATE INDEX idx_orders_active_user ON orders(user_id) 
WHERE status = 'active';

-- Query
SELECT * FROM orders WHERE user_id = 123 AND status = 'active';
-- Có thể dùng partial index
```

---

### Câu A.2: Covering Index

**a) Covering Index là gì?**

**Covering Index** là index chứa TẤT CẢ columns cần cho query → không cần đọc table.

**Ví dụ:**
```sql
-- Query
SELECT user_id, status FROM orders WHERE user_id = 123;

-- Covering index
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
-- Index chứa cả user_id và status → không cần đọc table
```

**b) Khác với regular index:**

**Regular index:**
- Index chỉ chứa indexed column
- Phải đọc table để lấy columns khác

**Covering index:**
- Index chứa tất cả columns cần
- Không cần đọc table → nhanh hơn

**c) Khi nào nên dùng?**

**Khi:**
- ✅ Query chỉ SELECT một vài columns
- ✅ Có thể include columns vào index
- ✅ Read-heavy workload

---

### Câu A.3: Index và Query Optimization

**a) Index có thể làm query chậm hơn không?**

**Đáp án: CÓ, trong một số cases**

**Khi:**
- ❌ Query trả về > 50% rows → Full Table Scan có thể nhanh hơn
- ❌ Index selectivity thấp → nhiều rows match
- ❌ Quá nhiều indexes → database phải chọn index nào

**b) Quá nhiều indexes:**

**Hậu quả:**
- ❌ Storage tốn
- ❌ Write performance chậm
- ❌ Index maintenance overhead
- ❌ Database phải chọn index nào → có thể chọn sai

**c) Best practices:**

1. **Tạo index cần thiết**: Chỉ tạo index trên columns thường query
2. **Monitor index usage**: Xóa indexes không dùng
3. **Balance**: Balance giữa read và write performance
4. **Test**: Test queries trước và sau khi tạo index

---

## 📝 TÓM TẮT

### Key Learnings

1. **Index** giúp tìm kiếm nhanh hơn 100-1000x (O(log n) vs O(n))
2. **B-Tree** là cấu trúc phổ biến cho Index
3. **Index Scan** nhanh hơn Full Table Scan rất nhiều
4. **Tạo index** trên Foreign Keys và columns thường query
5. **Trade-off**: Index làm SELECT nhanh, nhưng làm INSERT/UPDATE/DELETE chậm

### Best Practices

✅ **Tạo index trên Foreign Keys**: Để JOIN nhanh
✅ **Tạo index trên WHERE columns**: Columns thường query
✅ **Monitor index usage**: Đảm bảo index được dùng
✅ **Test performance**: Test queries trước và sau khi tạo index
✅ **Consider trade-offs**: Index tốn storage và làm chậm writes

---

**Chúc mừng hoàn thành Day-009!** 🎉

**Chuẩn bị cho Day-010: Logical vs Physical Design** 🚀

