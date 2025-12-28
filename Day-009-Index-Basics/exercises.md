# Day-009: Bài Tập - Index (Cơ bản)

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Index. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Index là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Index là gì?
- Tại sao Index làm query nhanh hơn?
- Index tốn gì? (Storage, performance)

---

### Câu 1.2: B-Tree Index

**Câu hỏi:**

a) B-Tree index là gì? (High-level concept)

b) Tại sao database dùng B-Tree cho index?

c) Time complexity của B-Tree search là gì?

d) B-Tree khác Full Table Scan như thế nào?

---

### Câu 1.3: Index Scan vs Full Table Scan

**Câu hỏi:**

a) Index Scan là gì? Khi nào xảy ra?

b) Full Table Scan là gì? Khi nào xảy ra?

c) So sánh performance của Index Scan vs Full Table Scan.

d) Database tự động chọn Index Scan hay Full Table Scan - đúng không?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Query chậm do thiếu Index

**Tình huống:**

Table `orders` có 1 triệu rows, query chậm:

```sql
SELECT * FROM orders WHERE user_id = 12345;
-- Mất 5 giây
```

**Câu hỏi:**

a) Tại sao query chậm? (Phân tích execution)

b) Làm thế nào để query nhanh hơn?

c) Viết command tạo index.

d) Sau khi tạo index, query sẽ nhanh như thế nào? (Ước tính)

---

### Câu 2.2: Index không được dùng

**Tình huống:**

Table `users` có index trên `email`, nhưng query vẫn chậm:

```sql
SELECT * FROM users WHERE UPPER(email) = 'JOHN@EXAMPLE.COM';
-- Mất 3 giây, không dùng index
```

**Câu hỏi:**

a) Tại sao index không được dùng?

b) Làm thế nào để query dùng index?

c) Có cách nào tạo index để hỗ trợ UPPER() không?

---

### Câu 2.3: Quá nhiều Indexes

**Tình huống:**

Table `products` có 20 indexes trên các columns khác nhau.

**Câu hỏi:**

a) Vấn đề gì có thể xảy ra với quá nhiều indexes?

b) Làm thế nào biết index nào được dùng? Index nào không?

c) Có nên xóa indexes không dùng không? Tại sao?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ INDEX

### Câu 3.1: E-commerce Orders

**Yêu cầu:** Thiết kế indexes cho table `orders`:

- Queries thường dùng:
  - `WHERE user_id = X`
  - `WHERE status = 'pending'`
  - `WHERE created_at > '2024-01-01'`
  - `ORDER BY created_at DESC`

**Câu hỏi:**

a) Viết CREATE INDEX statements.

b) Giải thích tại sao cần mỗi index.

c) Nếu có query `WHERE user_id = X AND status = 'pending'`, cần index gì?

---

### Câu 3.2: Users Table

**Yêu cầu:** Thiết kế indexes cho table `users`:

- Queries thường dùng:
  - `WHERE email = X` (login)
  - `WHERE name LIKE 'John%'` (search)
  - `ORDER BY created_at DESC` (newest users)

**Câu hỏi:**

a) Viết CREATE INDEX statements.

b) Index trên `name` có hỗ trợ `LIKE 'John%'` không? `LIKE '%John'`?

c) Nếu có query `WHERE email = X AND name = Y`, cần index gì?

---

### Câu 3.3: Products với Categories

**Yêu cầu:** Thiết kế indexes cho table `products`:

- Queries thường dùng:
  - `WHERE category_id = X`
  - `WHERE price > 100`
  - `WHERE name LIKE '%laptop%'`
  - `ORDER BY price ASC`

**Câu hỏi:**

a) Viết CREATE INDEX statements.

b) Index trên `name` có hỗ trợ `LIKE '%laptop%'` không? Tại sao?

c) Nếu có query `WHERE category_id = X AND price > 100`, cần index gì?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Index và Performance Trade-offs

**Tình huống:**

Bạn có 2 options:

**Option A: Không có index**
```sql
-- Không có index trên user_id
SELECT * FROM orders WHERE user_id = 12345;
```

**Option B: Có index**
```sql
-- Có index trên user_id
CREATE INDEX idx_orders_user_id ON orders(user_id);
SELECT * FROM orders WHERE user_id = 12345;
```

**Câu hỏi:**

a) So sánh 2 options về:
   - SELECT performance
   - INSERT performance
   - UPDATE performance
   - DELETE performance
   - Storage

b) Trong trường hợp nào Option A tốt hơn? Option B tốt hơn?

c) Trade-offs của index?

---

### Câu 4.2: Index Selectivity

**Tình huống:**

Table `users` có 1 triệu users, có 2 columns:

- `country` (10 giá trị khác nhau: US, VN, JP, ...)
- `email` (1 triệu giá trị khác nhau: unique)

**Câu hỏi:**

a) Index trên `country` vs `email` - cái nào hiệu quả hơn? Tại sao?

b) Selectivity là gì? Tại sao quan trọng?

c) Nếu query `WHERE country = 'US'` trả về 500,000 rows, index có hiệu quả không?

---

### Câu 4.3: Index và JOIN Performance

**Tình huống:**

Query JOIN 2 tables:

```sql
SELECT * FROM orders o
JOIN users u ON o.user_id = u.id;
```

**Câu hỏi:**

a) Index trên `orders.user_id` có ảnh hưởng đến JOIN performance không?

b) Index trên `users.id` có ảnh hưởng không? (Primary Key tự động có index)

c) Nếu không có index trên `orders.user_id`, JOIN sẽ như thế nào?

d) Best practices cho indexes trong JOINs?

---

### Câu 4.4: Index Maintenance

**Tình huống:**

Table `orders` có index trên `user_id`. Mỗi ngày có 100,000 orders mới.

**Câu hỏi:**

a) Index có được update tự động không khi INSERT orders mới?

b) INSERT 100,000 orders mới có ảnh hưởng đến index không?

c) Làm thế nào optimize INSERT performance với index?

d) Có nên disable index khi bulk insert không?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Index

**Yêu cầu:** Viết CREATE INDEX statements cho:

a) Index trên `users.email`

b) Index trên `orders.user_id`

c) Index trên `products.category_id`

d) Unique index trên `users.email`

---

### Câu 5.2: Analyze Query Plan

**Tình huống:**

Query chậm:

```sql
SELECT * FROM orders WHERE user_id = 12345;
```

**Câu hỏi:**

a) Viết EXPLAIN statement để analyze query plan.

b) Làm thế nào biết query có dùng index không?

c) Nếu query không dùng index, làm thế nào?

---

### Câu 5.3: Monitor Index Usage

**Yêu cầu:** Viết queries để:

a) List tất cả indexes trên table `orders`

b) Check index có được dùng không (PostgreSQL)

c) Tìm indexes không được dùng (có thể xóa)

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Index là gì? Tại sao Index làm query nhanh hơn?

2. B-Tree index là gì? (High-level)

3. Index Scan vs Full Table Scan - khi nào dùng gì?

4. Khi nào nên tạo Index? Khi nào không nên?

5. Làm thế nào kiểm tra index có được dùng không?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế indexes cho **hệ thống e-commerce**:

- `users`: 1 triệu users
- `products`: 100,000 products
- `orders`: 10 triệu orders
- `order_items`: 50 triệu order items

**Queries thường dùng:**
- Tìm user theo email (login)
- Tìm orders của user
- Tìm products theo category
- Tìm orders theo status và date range

**Yêu cầu:**

a) Thiết kế indexes cho tất cả tables.

b) Giải thích tại sao cần mỗi index.

c) Ước tính storage cho tất cả indexes.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Index và Partial Index

**Câu hỏi:**

a) Partial Index là gì? (Index chỉ trên một phần data)

b) Khi nào nên dùng Partial Index?

c) Ví dụ cụ thể về Partial Index?

---

### Câu A.2: Index và Covering Index

**Câu hỏi:**

a) Covering Index là gì?

b) Covering Index khác regular index như thế nào?

c) Khi nào nên dùng Covering Index?

---

### Câu A.3: Index và Query Optimization

**Câu hỏi:**

a) Index có thể làm query chậm hơn không? Khi nào?

b) Quá nhiều indexes có ảnh hưởng gì?

c) Best practices cho index management?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào tạo index

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

