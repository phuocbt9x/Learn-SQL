# Day-011: SQL Execution Flow - High-level

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- SQL query đi qua những bước nào từ khi gửi đến khi trả về kết quả
- Parser → Planner → Executor - vai trò của từng bước
- Query Plan là gì và tại sao quan trọng
- Cách database quyết định execution strategy
- Hậu quả nếu không hiểu execution flow

---

## 1️⃣ SQL QUERY ĐI QUA NHỮNG BƯỚC NÀO?

### **High-level Flow**

Khi bạn gửi một SQL query, nó đi qua các bước sau:

```
1. Client gửi SQL query
   ↓
2. Parser (Phân tích cú pháp)
   ↓
3. Planner (Lập kế hoạch thực thi)
   ↓
4. Executor (Thực thi)
   ↓
5. Trả về kết quả cho Client
```

**Ví dụ:**

```sql
-- Client gửi query
SELECT * FROM users WHERE email = 'john@example.com';
```

**Flow:**
1. **Parser**: Phân tích SQL syntax → tạo parse tree
2. **Planner**: Tạo execution plan (dùng index nào, JOIN như thế nào)
3. **Executor**: Thực thi plan → đọc data từ disk/memory
4. **Return**: Trả về kết quả cho client

### **Tại sao quan trọng?**

Hiểu execution flow giúp:

1. **Debug queries**: Biết query chậm ở bước nào
2. **Optimize queries**: Hiểu cách database xử lý query
3. **Read query plans**: Hiểu EXPLAIN output
4. **Troubleshoot**: Tìm nguyên nhân performance issues

---

## 2️⃣ PARSER (PHÂN TÍCH CÚ PHÁP)

### **Nó là gì?**

**Parser** là bước đầu tiên, phân tích SQL query để:
- **Kiểm tra syntax**: SQL có đúng cú pháp không?
- **Parse thành cấu trúc**: Chuyển SQL text thành parse tree
- **Validate**: Kiểm tra tables, columns có tồn tại không?

**Ví dụ:**

```sql
-- SQL query
SELECT * FROM users WHERE email = 'john@example.com';

-- Parser phân tích:
-- - SELECT: Lấy columns
-- - FROM users: Từ table users
-- - WHERE email = '...': Điều kiện filter
```

**Nếu syntax sai:**

```sql
-- ❌ Syntax error
SELECT * FORM users WHERE email = 'john@example.com';
-- Parser phát hiện: "FORM" không phải keyword → ERROR
```

**Output của Parser:**

- **Parse tree**: Cấu trúc dữ liệu biểu diễn query
- **Hoặc error**: Nếu syntax sai

### **Khi nào xảy ra?**

Parser chạy **MỖI LẦN** gửi query:
- ✅ Validate syntax
- ✅ Parse query
- ✅ Check tables/columns tồn tại

**Performance:**
- Parser rất nhanh (< 1ms)
- Không ảnh hưởng nhiều đến performance

---

## 3️⃣ PLANNER (LẬP KẾ HOẠCH THỰC THI)

### **Nó là gì?**

**Planner** (hay Query Optimizer) là bước quan trọng nhất, tạo **execution plan**:
- **Phân tích query**: Hiểu query cần làm gì
- **Tạo plans**: Tạo nhiều execution plans khác nhau
- **Chọn plan tốt nhất**: Dựa trên cost estimation
- **Output**: Query Plan (execution plan)

**Ví dụ:**

```sql
SELECT * FROM users WHERE email = 'john@example.com';
```

**Planner tạo plans:**

**Plan A: Full Table Scan**
- Scan tất cả rows → check email
- Cost: 1000 (ước tính)

**Plan B: Index Scan**
- Dùng index trên email → tìm nhanh
- Cost: 10 (ước tính)

**Planner chọn Plan B** (cost thấp hơn)

### **Cost Estimation**

Planner ước tính **cost** (chi phí) của mỗi plan dựa trên:
- **Statistics**: Số rows, data distribution
- **Indexes**: Có indexes nào available
- **Table size**: Table lớn hay nhỏ
- **Selectivity**: Query trả về bao nhiêu % rows

**Ví dụ:**

```sql
-- Table: 1 triệu rows
-- Index trên email: Có
-- Query: WHERE email = 'john@example.com' (trả về 1 row)

-- Plan A: Full Table Scan
-- Cost: 1,000,000 (scan 1M rows)

-- Plan B: Index Scan
-- Cost: 10 (index lookup + 1 row read)

-- Planner chọn Plan B (cost thấp hơn)
```

### **Khi nào xảy ra?**

Planner chạy **MỖI LẦN** gửi query:
- ✅ Tạo execution plan
- ✅ Chọn plan tốt nhất
- ✅ Cache plan (một số databases)

**Performance:**
- Planner nhanh (< 10ms thường)
- Nhưng ảnh hưởng lớn đến query performance (chọn plan đúng/sai)

---

## 4️⃣ EXECUTOR (THỰC THI)

### **Nó là gì?**

**Executor** là bước cuối cùng, **thực thi** execution plan:
- **Đọc data**: Đọc data từ disk/memory
- **Apply operations**: Filter, JOIN, aggregate, sort
- **Return results**: Trả về kết quả cho client

**Ví dụ:**

```sql
SELECT * FROM users WHERE email = 'john@example.com';
```

**Executor thực thi:**

1. **Index Scan**: Dùng index để tìm 'john@example.com'
2. **Read row**: Đọc row từ table
3. **Return**: Trả về row cho client

### **Operations**

Executor thực hiện các operations:
- **Scan**: Seq Scan, Index Scan
- **Filter**: WHERE conditions
- **Join**: JOIN operations
- **Aggregate**: SUM, COUNT, AVG
- **Sort**: ORDER BY
- **Limit**: LIMIT

### **Khi nào xảy ra?**

Executor chạy **MỖI LẦN** query được execute:
- ✅ Thực thi plan
- ✅ Đọc data
- ✅ Trả về kết quả

**Performance:**
- Executor là bước tốn thời gian nhất
- Phụ thuộc vào plan (tốt/xấu)

---

## 5️⃣ QUERY PLAN LÀ GÌ?

### **Nó là gì?**

**Query Plan** (Execution Plan) là kế hoạch thực thi query do Planner tạo ra.

**Ví dụ:**

```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

**Output (PostgreSQL):**

```
Index Scan using idx_users_email on users
  Index Cond: (email = 'john@example.com')
  Rows: 1
  Cost: 0.43..8.45
```

**Phân tích:**
- **Index Scan**: Dùng index để scan
- **Index Cond**: Điều kiện trên index
- **Rows**: Ước tính số rows trả về
- **Cost**: Chi phí thực thi (0.43 start, 8.45 total)

### **Tại sao quan trọng?**

Query Plan giúp:

1. **Hiểu execution**: Biết database làm gì
2. **Debug performance**: Tìm nguyên nhân query chậm
3. **Optimize**: Biết cách optimize query
4. **Verify indexes**: Kiểm tra index có được dùng không

### **Cách xem Query Plan**

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

## 6️⃣ PRODUCTION STORY: QUERY "ĐƠN GIẢN" NHƯNG CHẬM - TẠI SAO?

### **Context**

Startup có query "đơn giản":

```sql
SELECT * FROM orders WHERE user_id = 12345;
-- Mất 30 giây!
```

**Query trông đơn giản**, nhưng tại sao chậm?

### **Investigation**

**Bước 1: Check query plan**

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

**Kết quả:**

```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=100)
  Filter: (user_id = 12345)
Execution Time: 30000.123 ms
```

**Phân tích:**
- **Seq Scan**: Full Table Scan (không dùng index!)
- **Cost**: 250,000 (rất cao)
- **Execution Time**: 30 giây (quá chậm!)

**Bước 2: Check indexes**

```sql
SELECT indexname FROM pg_indexes WHERE tablename = 'orders';
```

Kết quả: Chỉ có index trên `id` (Primary Key), **KHÔNG có index trên `user_id`**!

**Root cause:**
1. Không có index trên `user_id`
2. Planner chọn Full Table Scan (không có option khác)
3. Executor phải scan 10 triệu rows → chậm

### **Fix**

**Fix 1: Tạo index**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**Fix 2: Check query plan lại**

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

**Kết quả:**

```
Index Scan using idx_orders_user_id on orders (cost=0.43..8.45 rows=100 width=100)
  Index Cond: (user_id = 12345)
Execution Time: 0.123 ms
```

**Phân tích:**
- **Index Scan**: Dùng index (không còn Full Table Scan!)
- **Cost**: 8.45 (giảm từ 250,000!)
- **Execution Time**: 0.123 ms (giảm từ 30 giây!)

**Nhanh hơn 240,000x!**

### **Lesson Learned**

1. **Query "đơn giản" không có nghĩa là nhanh**: Phụ thuộc vào execution plan
2. **Check query plan**: Dùng EXPLAIN để xem plan
3. **Indexes quan trọng**: Không có index → Full Table Scan → chậm
4. **Planner chọn plan**: Planner chọn plan tốt nhất dựa trên available indexes
5. **Understand execution flow**: Hiểu flow giúp debug và optimize

---

## 7️⃣ BEST PRACTICES

### **7.1. Hiểu Execution Flow**

✅ **Biết các bước**: Parser → Planner → Executor
✅ **Check query plan**: Dùng EXPLAIN để xem plan
✅ **Verify indexes**: Đảm bảo indexes được dùng
✅ **Monitor performance**: Monitor query performance

### **7.2. Optimize Queries**

✅ **Tạo indexes**: Indexes giúp Planner chọn plan tốt
✅ **Update statistics**: Statistics giúp Planner estimate đúng
✅ **Review plans**: Review query plans định kỳ

### **7.3. Debug Performance**

✅ **Use EXPLAIN**: Dùng EXPLAIN để debug
✅ **Check indexes**: Đảm bảo có indexes cần thiết
✅ **Understand plans**: Hiểu query plans

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **SQL execution flow**: Parser → Planner → Executor
2. **Parser**: Phân tích syntax, validate
3. **Planner**: Tạo execution plan, chọn plan tốt nhất
4. **Executor**: Thực thi plan, trả về kết quả
5. **Query Plan**: Kế hoạch thực thi, quan trọng để debug và optimize

### **Best Practices**

✅ **Hiểu execution flow**: Biết các bước
✅ **Check query plan**: Dùng EXPLAIN
✅ **Tạo indexes**: Giúp Planner chọn plan tốt
✅ **Monitor performance**: Monitor queries
✅ **Debug với EXPLAIN**: Dùng EXPLAIN để debug

### **Câu hỏi tự kiểm tra**

1. SQL query đi qua những bước nào?
2. Parser làm gì? Planner làm gì? Executor làm gì?
3. Query Plan là gì? Tại sao quan trọng?
4. Làm thế nào xem Query Plan?
5. Tại sao query "đơn giản" có thể chậm?

---







**Chuẩn bị cho [Day-012: Database-Connection-Session](../Day-012-Database-Connection-Session/theory.md)** 🚀
