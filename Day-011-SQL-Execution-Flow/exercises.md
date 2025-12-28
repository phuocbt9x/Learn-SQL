# Day-011: Bài Tập - SQL Execution Flow

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về SQL Execution Flow. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: SQL Execution Flow

**Câu hỏi:** Hãy giải thích ngắn gọn:
- SQL query đi qua những bước nào?
- Parser làm gì?
- Planner làm gì?
- Executor làm gì?

---

### Câu 1.2: Query Plan

**Câu hỏi:**

a) Query Plan là gì?

b) Tại sao Query Plan quan trọng?

c) Làm thế nào xem Query Plan?

---

### Câu 1.3: Cost Estimation

**Câu hỏi:**

a) Planner ước tính cost dựa trên gì?

b) Tại sao cost estimation quan trọng?

c) Cost thấp có nghĩa là query nhanh không?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Query chậm - Phân tích

**Tình huống:**

Query chậm:

```sql
SELECT * FROM orders WHERE user_id = 12345;
-- Mất 30 giây
```

**EXPLAIN output:**

```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=100)
  Filter: (user_id = 12345)
Execution Time: 30000.123 ms
```

**Câu hỏi:**

a) Phân tích vấn đề từ EXPLAIN output.

b) Tại sao query chậm?

c) Làm thế nào fix?

---

### Câu 2.2: Query không dùng index

**Tình huống:**

Có index trên `email`, nhưng query không dùng:

```sql
SELECT * FROM users WHERE email = 'john@example.com';
```

**EXPLAIN output:**

```
Seq Scan on users (cost=0.00..10000.00 rows=1 width=100)
  Filter: (email = 'john@example.com')
```

**Câu hỏi:**

a) Tại sao không dùng index?

b) Có thể force dùng index không?

c) Làm thế nào đảm bảo dùng index?

---

### Câu 2.3: Planner chọn plan sai

**Tình huống:**

Planner chọn Full Table Scan thay vì Index Scan:

```sql
SELECT * FROM products WHERE category_id = 5;
```

**EXPLAIN output:**

```
Seq Scan on products (cost=0.00..50000.00 rows=1000 width=100)
  Filter: (category_id = 5)
```

**Nhưng có index trên `category_id`!**

**Câu hỏi:**

a) Tại sao Planner chọn Full Table Scan?

b) Khi nào Planner chọn Full Table Scan thay vì Index Scan?

c) Làm thế nào fix?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Analyze Query Plan

**Yêu cầu:**

Cho query:

```sql
SELECT u.name, o.total_amount
FROM users u
JOIN orders o ON u.id = o.user_id
WHERE u.email = 'john@example.com';
```

**Câu hỏi:**

a) Dự đoán query plan (không cần chạy EXPLAIN).

b) Cần indexes gì để query nhanh?

c) Viết CREATE INDEX statements.

---

### Câu 3.2: Optimize Query

**Yêu cầu:**

Query hiện tại:

```sql
SELECT * FROM orders
WHERE user_id = 12345
ORDER BY created_at DESC
LIMIT 10;
```

**EXPLAIN output:**

```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=100)
  Filter: (user_id = 12345)
  Sort (cost=1000.00..1000.00 rows=100 width=100)
    Sort Key: created_at DESC
    Limit (cost=0.00..0.00 rows=10)
```

**Câu hỏi:**

a) Phân tích query plan.

b) Tối ưu query (indexes, query structure).

c) Viết CREATE INDEX statements.

---

### Câu 3.3: Complex Query Plan

**Yêu cầu:**

Query:

```sql
SELECT p.name, COUNT(oi.id) as order_count
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
WHERE p.category_id = 5
GROUP BY p.id, p.name
HAVING COUNT(oi.id) > 10
ORDER BY order_count DESC;
```

**Câu hỏi:**

a) Dự đoán query plan.

b) Cần indexes gì?

c) Viết CREATE INDEX statements.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Parser vs Planner vs Executor

**Tình huống:**

Query chậm, bạn cần tìm nguyên nhân.

**Câu hỏi:**

a) Làm thế nào xác định vấn đề ở Parser, Planner, hay Executor?

b) Vấn đề ở mỗi bước có triệu chứng gì?

c) Cách debug từng bước?

---

### Câu 4.2: Cost Estimation và Performance

**Tình huống:**

Planner estimate cost thấp, nhưng query thực tế chậm.

**Câu hỏi:**

a) Tại sao cost estimate có thể sai?

b) Khi nào cost estimate không chính xác?

c) Làm thế nào đảm bảo cost estimate đúng?

---

### Câu 4.3: Query Plan Caching

**Câu hỏi:**

a) Một số databases cache query plans. Lợi ích và rủi ro?

b) Khi nào nên invalidate plan cache?

c) Trade-offs của plan caching?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Read EXPLAIN Output

**EXPLAIN output:**

```
Nested Loop (cost=0.43..25.00 rows=10 width=100)
  -> Index Scan using idx_users_email on users (cost=0.43..8.45 rows=1 width=50)
        Index Cond: (email = 'john@example.com')
  -> Index Scan using idx_orders_user_id on orders (cost=0.00..16.55 rows=10 width=50)
        Index Cond: (user_id = users.id)
```

**Yêu cầu:**

a) Giải thích query plan này.

b) Query này làm gì?

c) Performance tốt hay không? Tại sao?

---

### Câu 5.2: Compare Query Plans

**Query A:**

```sql
SELECT * FROM orders WHERE user_id = 12345;
```

**Plan A:**
```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=100)
  Filter: (user_id = 12345)
```

**Query B:**

```sql
SELECT * FROM orders WHERE user_id = 12345;
-- (sau khi tạo index)
```

**Plan B:**
```
Index Scan using idx_orders_user_id on orders (cost=0.43..8.45 rows=100 width=100)
  Index Cond: (user_id = 12345)
```

**Yêu cầu:**

a) So sánh 2 plans về:
   - Cost
   - Performance
   - Execution strategy

b) Plan nào tốt hơn? Tại sao?

---

### Câu 5.3: Debug Slow Query

**Tình huống:**

Query chậm:

```sql
SELECT u.name, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id, u.name
HAVING COUNT(o.id) > 5;
```

**EXPLAIN output:**

```
Hash Join (cost=100000.00..200000.00 rows=1000 width=100)
  Hash Cond: (o.user_id = u.id)
  -> Seq Scan on orders (cost=0.00..100000.00 rows=1000000 width=50)
  -> Hash (cost=50000.00..50000.00 rows=100000 width=50)
        -> Seq Scan on users (cost=0.00..50000.00 rows=100000 width=50)
              Filter: (created_at > '2024-01-01')
```

**Yêu cầu:**

a) Phân tích vấn đề.

b) Tối ưu query (indexes, query structure).

c) Viết CREATE INDEX statements.

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. SQL query đi qua những bước nào?

2. Parser làm gì? Planner làm gì? Executor làm gì?

3. Query Plan là gì? Tại sao quan trọng?

4. Làm thế nào xem Query Plan?

5. Tại sao query "đơn giản" có thể chậm?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn có query chậm:

```sql
SELECT * FROM products
WHERE category_id = 5
AND price BETWEEN 100 AND 500
ORDER BY created_at DESC
LIMIT 20;
```

**Yêu cầu:**

a) Dự đoán query plan.

b) Cần indexes gì?

c) Viết CREATE INDEX statements.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Query Optimization

**Câu hỏi:**

a) Có thể force Planner chọn plan cụ thể không?

b) Khi nào nên force plan?

c) Trade-offs của việc force plan?

---

### Câu A.2: Statistics và Planner

**Câu hỏi:**

a) Statistics ảnh hưởng đến Planner như thế nào?

b) Khi nào cần update statistics?

c) Làm thế nào update statistics?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Query Plan là công cụ quan trọng để debug và optimize
- Senior SQL Engineer hiểu execution flow và biết đọc query plans

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

