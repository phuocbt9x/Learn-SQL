# Day-011: Solutions - SQL Execution Flow

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: SQL Execution Flow

**Đáp án:**

**SQL query đi qua những bước nào?**

1. **Parser**: Phân tích SQL syntax, validate, tạo parse tree
2. **Planner**: Tạo execution plans, chọn plan tốt nhất
3. **Executor**: Thực thi plan, trả về kết quả

**Parser làm gì?**

- Kiểm tra SQL syntax
- Parse SQL thành parse tree
- Validate tables/columns tồn tại
- Output: Parse tree hoặc error

**Planner làm gì?**

- Phân tích query
- Tạo nhiều execution plans
- Estimate cost cho mỗi plan
- Chọn plan tốt nhất (cost thấp nhất)
- Output: Query Plan

**Executor làm gì?**

- Thực thi query plan
- Đọc data từ disk/memory
- Apply operations (filter, JOIN, aggregate, sort)
- Trả về kết quả cho client

---

### Câu 1.2: Query Plan

**a) Query Plan là gì?**

**Đáp án:** Query Plan (Execution Plan) là kế hoạch thực thi query do Planner tạo ra, mô tả cách database sẽ thực thi query.

**b) Tại sao quan trọng?**

**Lý do:**
- Hiểu execution strategy
- Debug performance issues
- Optimize queries
- Verify indexes được dùng

**c) Làm thế nào xem Query Plan?**

**PostgreSQL:**
```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';
```

**MySQL:**
```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

**SQL Server:**
```sql
SET SHOWPLAN_ALL ON;
SELECT * FROM users WHERE email = 'john@example.com';
```

---

### Câu 1.3: Cost Estimation

**a) Planner ước tính cost dựa trên gì?**

**Đáp án:**
- **Statistics**: Số rows, data distribution
- **Indexes**: Có indexes nào available
- **Table size**: Table lớn hay nhỏ
- **Selectivity**: Query trả về bao nhiêu % rows

**b) Tại sao cost estimation quan trọng?**

**Lý do:**
- Planner dùng cost để chọn plan tốt nhất
- Cost thấp → plan tốt hơn (thường)
- Cost estimation sai → Planner chọn plan sai → query chậm

**c) Cost thấp có nghĩa là query nhanh không?**

**Đáp án: THƯỜNG ĐÚNG, nhưng không phải luôn luôn**

**Lý do:**
- Cost là ước tính, không phải thời gian thực tế
- Cost có thể sai nếu statistics không đúng
- Nhưng thường cost thấp → query nhanh hơn

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Query chậm - Phân tích

**a) Phân tích vấn đề:**

**Vấn đề:**
- **Seq Scan**: Full Table Scan (không dùng index!)
- **Cost**: 250,000 (rất cao)
- **Execution Time**: 30 giây (quá chậm!)

**b) Tại sao query chậm?**

**Lý do:**
- Không có index trên `user_id`
- Planner chọn Full Table Scan (không có option khác)
- Executor phải scan tất cả rows → chậm

**c) Làm thế nào fix?**

**Fix:**
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**Sau khi tạo index:**

```
Index Scan using idx_orders_user_id on orders (cost=0.43..8.45 rows=100 width=100)
  Index Cond: (user_id = 12345)
Execution Time: 0.123 ms
```

**Nhanh hơn 240,000x!**

---

### Câu 2.2: Query không dùng index

**a) Tại sao không dùng index?**

**Có thể do:**
1. **Statistics không đúng**: Planner nghĩ Full Table Scan nhanh hơn
2. **Index không phù hợp**: Index không match query
3. **Small table**: Table nhỏ → Full Table Scan nhanh hơn Index Scan

**b) Có thể force dùng index không?**

**PostgreSQL:**
```sql
-- Không có cách force trực tiếp, nhưng có thể:
-- 1. Update statistics
ANALYZE users;

-- 2. Tạo index phù hợp hơn
CREATE INDEX idx_users_email ON users(email);
```

**MySQL:**
```sql
-- Force index (không recommended)
SELECT * FROM users FORCE INDEX (idx_users_email) WHERE email = 'john@example.com';
```

**c) Làm thế nào đảm bảo dùng index?**

**Best practices:**
1. **Tạo index phù hợp**: Index match query
2. **Update statistics**: ANALYZE table
3. **Review query plan**: Dùng EXPLAIN để verify

---

### Câu 2.3: Planner chọn plan sai

**a) Tại sao Planner chọn Full Table Scan?**

**Có thể do:**
1. **Statistics không đúng**: Planner nghĩ Full Table Scan nhanh hơn
2. **Selectivity cao**: Query trả về nhiều rows → Full Table Scan có thể nhanh hơn
3. **Index không selective**: Index không giúp filter nhiều

**b) Khi nào Planner chọn Full Table Scan?**

**Khi:**
- Table nhỏ → Full Table Scan nhanh hơn Index Scan
- Selectivity cao → Query trả về nhiều rows
- Statistics không đúng → Planner estimate sai

**c) Làm thế nào fix?**

**Fix:**
```sql
-- 1. Update statistics
ANALYZE products;

-- 2. Tạo index phù hợp hơn
CREATE INDEX idx_products_category_id ON products(category_id);

-- 3. Check query plan lại
EXPLAIN ANALYZE SELECT * FROM products WHERE category_id = 5;
```

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Analyze Query Plan

**a) Dự đoán query plan:**

**Plan:**
1. **Index Scan** trên `users.email` (nếu có index)
2. **Index Scan** hoặc **Seq Scan** trên `orders.user_id` (tùy có index)
3. **Nested Loop** hoặc **Hash Join** để JOIN

**b) Cần indexes gì?**

**Indexes:**
- `idx_users_email` trên `users(email)`
- `idx_orders_user_id` trên `orders(user_id)`

**c) CREATE INDEX statements:**

```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

---

### Câu 3.2: Optimize Query

**a) Phân tích query plan:**

**Vấn đề:**
- **Seq Scan**: Full Table Scan (không có index trên `user_id`)
- **Sort**: Sort sau khi filter (không tận dụng index)
- **Limit**: Limit sau sort (không tối ưu)

**b) Tối ưu:**

**Option 1: Composite Index**
```sql
CREATE INDEX idx_orders_user_id_created_at ON orders(user_id, created_at DESC);
```

**Option 2: Separate Indexes**
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);
```

**c) CREATE INDEX statements:**

```sql
-- Composite index (tốt hơn cho query này)
CREATE INDEX idx_orders_user_id_created_at ON orders(user_id, created_at DESC);
```

**Query plan sau khi tối ưu:**

```
Index Scan using idx_orders_user_id_created_at on orders (cost=0.43..8.45 rows=10 width=100)
  Index Cond: (user_id = 12345)
  Limit (cost=0.00..0.00 rows=10)
```

---

### Câu 3.3: Complex Query Plan

**a) Dự đoán query plan:**

**Plan:**
1. **Seq Scan** hoặc **Index Scan** trên `products.category_id`
2. **Left Join** với `order_items`
3. **Group By** và **Aggregate** (COUNT)
4. **Having** filter
5. **Sort** (ORDER BY)

**b) Cần indexes gì?**

**Indexes:**
- `idx_products_category_id` trên `products(category_id)`
- `idx_order_items_product_id` trên `order_items(product_id)`

**c) CREATE INDEX statements:**

```sql
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Parser vs Planner vs Executor

**a) Làm thế nào xác định vấn đề?**

**Parser:**
- **Triệu chứng**: Syntax error
- **Debug**: Check SQL syntax

**Planner:**
- **Triệu chứng**: Query plan không tối ưu
- **Debug**: Dùng EXPLAIN để xem plan

**Executor:**
- **Triệu chứng**: Query chậm dù plan tốt
- **Debug**: Dùng EXPLAIN ANALYZE để xem execution time

**b) Vấn đề ở mỗi bước:**

**Parser:**
- Syntax error
- Table/column không tồn tại

**Planner:**
- Chọn plan sai
- Cost estimation sai
- Không dùng index

**Executor:**
- Disk I/O chậm
- Lock contention
- Resource contention

**c) Cách debug:**

**Parser:**
- Check SQL syntax
- Check tables/columns tồn tại

**Planner:**
- Dùng EXPLAIN để xem plan
- Check indexes
- Update statistics

**Executor:**
- Dùng EXPLAIN ANALYZE để xem execution time
- Check disk I/O
- Check locks

---

### Câu 4.2: Cost Estimation và Performance

**a) Tại sao cost estimate có thể sai?**

**Lý do:**
- **Statistics không đúng**: Statistics cũ hoặc không đúng
- **Data distribution**: Data distribution không đều
- **Correlated columns**: Columns có correlation → estimate sai

**b) Khi nào cost estimate không chính xác?**

**Khi:**
- Statistics không được update
- Data distribution thay đổi
- Correlated columns
- Small sample size

**c) Làm thế nào đảm bảo cost estimate đúng?**

**Best practices:**
1. **Update statistics**: ANALYZE table định kỳ
2. **Large sample**: Dùng sample size lớn
3. **Review plans**: Review query plans định kỳ

---

### Câu 4.3: Query Plan Caching

**a) Lợi ích và rủi ro?**

**Lợi ích:**
- Giảm Planner overhead
- Query nhanh hơn (không cần plan lại)

**Rủi ro:**
- Plan cache có thể stale
- Plan không tối ưu cho data mới
- Memory overhead

**b) Khi nào nên invalidate plan cache?**

**Khi:**
- Statistics thay đổi
- Indexes thay đổi
- Table structure thay đổi
- Performance issues

**c) Trade-offs:**

**Trade-offs:**
- **Performance vs Freshness**: Cache nhanh nhưng có thể stale
- **Memory vs Speed**: Cache tốn memory nhưng nhanh hơn

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Read EXPLAIN Output

**a) Giải thích query plan:**

**Plan:**
1. **Nested Loop**: JOIN strategy
2. **Index Scan** trên `users.email`: Tìm user với email
3. **Index Scan** trên `orders.user_id`: Tìm orders của user

**b) Query này làm gì?**

**Query:**
- Tìm user với email 'john@example.com'
- JOIN với orders của user đó
- Trả về kết quả

**c) Performance tốt hay không?**

**Đáp án: TỐT**

**Lý do:**
- Dùng Index Scan (không phải Full Table Scan)
- Nested Loop hiệu quả cho small result set
- Cost thấp (25.00)

---

### Câu 5.2: Compare Query Plans

**a) So sánh:**

| Tiêu chí | Plan A (Seq Scan) | Plan B (Index Scan) |
|----------|-------------------|---------------------|
| **Cost** | 250,000 | 8.45 |
| **Performance** | Chậm (30s) | Nhanh (0.123ms) |
| **Strategy** | Full Table Scan | Index Scan |

**b) Plan nào tốt hơn?**

**Đáp án: Plan B (Index Scan)**

**Lý do:**
- Cost thấp hơn 29,000x
- Performance nhanh hơn 240,000x
- Strategy hiệu quả hơn

---

### Câu 5.3: Debug Slow Query

**a) Phân tích vấn đề:**

**Vấn đề:**
- **Seq Scan** trên cả `orders` và `users` (không có indexes!)
- **Hash Join**: JOIN strategy không tối ưu
- **Cost**: 200,000 (rất cao)

**b) Tối ưu:**

**Indexes:**
```sql
CREATE INDEX idx_users_created_at ON users(created_at);
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**c) CREATE INDEX statements:**

```sql
CREATE INDEX idx_users_created_at ON users(created_at);
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**Query plan sau khi tối ưu:**

```
Hash Join (cost=1000.00..2000.00 rows=1000 width=100)
  Hash Cond: (o.user_id = u.id)
  -> Index Scan using idx_orders_user_id on orders (cost=0.43..500.00 rows=100000 width=50)
  -> Hash (cost=200.00..200.00 rows=10000 width=50)
        -> Index Scan using idx_users_created_at on users (cost=0.43..200.00 rows=10000 width=50)
              Index Cond: (created_at > '2024-01-01')
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **SQL execution flow**: Parser → Planner → Executor
2. **Parser**: Phân tích syntax, validate
3. **Planner**: Tạo execution plan, chọn plan tốt nhất
4. **Executor**: Thực thi plan, trả về kết quả
5. **Query Plan**: Kế hoạch thực thi, quan trọng để debug và optimize

---

### Câu 6.2: Áp dụng thực tế

**a) Dự đoán query plan:**

**Plan:**
1. **Seq Scan** hoặc **Index Scan** trên `products.category_id`
2. **Filter** trên `price BETWEEN 100 AND 500`
3. **Sort** (ORDER BY created_at DESC)
4. **Limit** (LIMIT 20)

**b) Cần indexes gì?**

**Indexes:**
- `idx_products_category_id` trên `products(category_id)`
- Composite index: `idx_products_category_price_created` trên `products(category_id, price, created_at DESC)`

**c) CREATE INDEX statements:**

```sql
-- Option 1: Separate indexes
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_products_price ON products(price);
CREATE INDEX idx_products_created_at ON products(created_at DESC);

-- Option 2: Composite index (tốt hơn cho query này)
CREATE INDEX idx_products_category_price_created ON products(category_id, price, created_at DESC);
```

---

## 📝 TÓM TẮT

### Key Learnings

1. **SQL execution flow**: Parser → Planner → Executor
2. **Parser**: Phân tích syntax, validate
3. **Planner**: Tạo execution plan, chọn plan tốt nhất
4. **Executor**: Thực thi plan, trả về kết quả
5. **Query Plan**: Quan trọng để debug và optimize

### Best Practices

✅ **Hiểu execution flow**: Biết các bước
✅ **Check query plan**: Dùng EXPLAIN
✅ **Tạo indexes**: Giúp Planner chọn plan tốt
✅ **Update statistics**: Đảm bảo cost estimate đúng
✅ **Debug với EXPLAIN ANALYZE**: Xem execution time thực tế

---

**Chúc mừng hoàn thành Day-011!** 🎉

**Chuẩn bị cho Day-012: Database Connection & Session** 🚀

