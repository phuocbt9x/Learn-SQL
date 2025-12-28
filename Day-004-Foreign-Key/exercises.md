# Day-004: Bài Tập - Foreign Key

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Foreign Key và Referential Integrity. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Foreign Key là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Foreign Key là gì?
- Tại sao cần Foreign Key?
- Foreign Key reference đến gì? (Primary Key hay bất kỳ column nào?)

---

### Câu 1.2: Referential Integrity

**Câu hỏi:** 

a) Referential Integrity là gì?

b) Tại sao Referential Integrity quan trọng?

c) Làm thế nào Foreign Key đảm bảo Referential Integrity?

d) Cho ví dụ vi phạm Referential Integrity.

---

### Câu 1.3: ON DELETE Actions

**Câu hỏi:** Giải thích sự khác biệt giữa:

- **ON DELETE CASCADE**
- **ON DELETE RESTRICT**
- **ON DELETE SET NULL**

Cho ví dụ cụ thể cho mỗi loại.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Table không có Foreign Key

**Tình huống:**

Developer tạo tables như sau:

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- ❌ Không có FOREIGN KEY constraint
  total_amount DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại với Foreign Key constraint.

c) Nếu muốn xóa user → tự động xóa orders, làm thế nào?

---

### Câu 2.2: Chọn sai ON DELETE Action

**Tình huống:**

Table `orders` có Foreign Key đến `users`:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

**Vấn đề:** Khi xóa user, tất cả orders bị xóa → mất dữ liệu audit.

**Câu hỏi:**

a) Tại sao CASCADE không phù hợp trong trường hợp này?

b) Nên dùng ON DELETE action nào? Tại sao?

c) Nếu muốn giữ lại orders nhưng đánh dấu user đã bị xóa, làm thế nào?

---

### Câu 2.3: Orphan Records

**Tình huống:**

Table `orders` có một số orders với `user_id` không tồn tại trong `users`:

```sql
-- Users
id | name
1  | John
2  | Jane

-- Orders (có orphan records)
id | user_id | total_amount
1  | 1       | 100.00
2  | 999     | 200.00  -- ❌ user_id = 999 không tồn tại
3  | 2       | 150.00
```

**Câu hỏi:**

a) Viết query tìm tất cả orphan records.

b) Làm thế nào để fix orphan records? (Có 2 options: xóa hoặc assign lại)

c) Làm thế nào để ngăn chặn orphan records trong tương lai?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce System

**Yêu cầu:** Thiết kế Foreign Keys cho hệ thống e-commerce:

- `users`: Users
- `products`: Products
- `orders`: Orders (của users)
- `order_items`: Items trong orders (reference đến orders và products)

**Business rules:**
- Mỗi order phải có user
- Mỗi order_item phải có order và product
- Khi xóa user → không cho xóa nếu có orders (RESTRICT)
- Khi xóa order → tự động xóa order_items (CASCADE)

**Câu hỏi:**

a) Viết CREATE TABLE với Foreign Keys phù hợp.

b) Giải thích tại sao chọn mỗi ON DELETE action.

c) Nếu muốn xóa user → tự động xóa orders và order_items, làm thế nào?

---

### Câu 3.2: Blog System

**Yêu cầu:** Thiết kế Foreign Keys cho blog system:

- `users`: Authors
- `posts`: Blog posts (của authors)
- `comments`: Comments trên posts (của users)
- `tags`: Tags
- `post_tags`: Relationship giữa posts và tags

**Business rules:**
- Mỗi post phải có author
- Mỗi comment phải có post và user
- Khi xóa post → xóa comments (CASCADE)
- Khi xóa user (author) → set posts.author_id = NULL (SET NULL)
- Khi xóa user (commenter) → set comments.user_id = NULL (SET NULL)

**Câu hỏi:**

a) Viết CREATE TABLE với Foreign Keys phù hợp.

b) Giải thích tại sao chọn mỗi ON DELETE action.

c) Có vấn đề gì với SET NULL cho posts.author_id không? (Post phải có author?)

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Foreign Key vs Application Validation

**Tình huống:**

Có 2 cách đảm bảo Referential Integrity:

**Option A: Foreign Key constraint**
```sql
FOREIGN KEY (user_id) REFERENCES users(id)
```

**Option B: Application validation**
```python
# Check trong code
if not user_exists(user_id):
    raise ValueError("User does not exist")
```

**Câu hỏi:**

a) So sánh 2 cách trên về:
   - Data integrity
   - Performance
   - Complexity
   - Reliability

b) Bạn sẽ chọn cách nào? Tại sao?

c) Có thể dùng cả 2 không? (Defense in depth)

---

### Câu 4.2: Foreign Key và Performance

**Tình huống:**

Table `orders` có Foreign Key đến `users`:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Câu hỏi:**

a) Foreign Key có ảnh hưởng đến performance không? Tại sao?

b) Khi nào Foreign Key làm chậm queries?

c) Làm thế nào để optimize performance với Foreign Key?

d) Có nên disable Foreign Key trong production để tăng performance không?

---

### Câu 4.3: Foreign Key và Data Warehouse

**Tình huống:**

Bạn đang thiết kế data warehouse (analytics, không phải OLTP).

**Câu hỏi:**

a) Có nên dùng Foreign Key trong data warehouse không? Tại sao?

b) Nếu không dùng Foreign Key, làm thế nào đảm bảo data integrity?

c) Trade-offs của việc không dùng Foreign Key trong data warehouse?

---

### Câu 4.4: Multiple Foreign Keys

**Tình huống:**

Table `order_items` có 2 Foreign Keys:

```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT,
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT
);
```

**Câu hỏi:**

a) Tại sao có thể có ON DELETE actions khác nhau cho các Foreign Keys?

b) Nếu xóa order → điều gì xảy ra với order_items?

c) Nếu xóa product → điều gì xảy ra với order_items?

d) Có conflict không nếu có 2 Foreign Keys với actions khác nhau?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Tables với Foreign Keys

**Yêu cầu:** Viết CREATE TABLE cho:

a) `users` và `orders` với Foreign Key (ON DELETE RESTRICT)

b) `categories` và `products` với Foreign Key (ON DELETE SET NULL)

c) `posts` và `comments` với Foreign Key (ON DELETE CASCADE)

---

### Câu 5.2: Xử lý Orphan Records

**Tình huống:**

Table `orders` có orphan records (user_id không tồn tại):

```sql
-- Tìm orphan records
SELECT o.*
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;
```

**Câu hỏi:**

a) Viết query xóa tất cả orphan orders.

b) Viết query assign orphan orders đến một default user.

c) Viết query tạo default user nếu chưa có, rồi assign orphan orders.

---

### Câu 5.3: Migrate từ không có Foreign Key sang có Foreign Key

**Tình huống:**

Table `orders` hiện tại không có Foreign Key constraint. Muốn thêm Foreign Key.

**Câu hỏi:**

a) Các bước cần làm trước khi thêm Foreign Key?

b) Viết script (pseudo-code) migrate:

1. Tìm orphan records
2. Fix orphan records (xóa hoặc assign)
3. Thêm Foreign Key constraint
4. Verify constraint hoạt động

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Foreign Key là gì? Tại sao cần Foreign Key?

2. Referential Integrity là gì? Tại sao quan trọng?

3. ON DELETE CASCADE vs RESTRICT vs SET NULL - khi nào dùng gì?

4. Khi nào nên dùng Foreign Key? Khi nào không nên?

5. Orphan records là gì? Làm thế nào để tránh?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý dự án**:

- `projects`: Dự án
- `users`: Users
- `project_members`: Members của dự án
- `tasks`: Tasks trong dự án
- `task_assignments`: Assignment của tasks cho users

**Yêu cầu:**

a) Thiết kế Foreign Keys cho tất cả relationships.

b) Chọn ON DELETE action phù hợp cho mỗi Foreign Key.

c) Giải thích tại sao chọn mỗi action.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Self-referencing Foreign Key

**Câu hỏi:**

a) Self-referencing Foreign Key là gì? Cho ví dụ.

b) Khi nào nên dùng self-referencing Foreign Key?

c) Có vấn đề gì với self-referencing Foreign Key? (Ví dụ: circular reference)

---

### Câu A.2: Foreign Key và Soft Delete

**Tình huống:**

Table `users` dùng soft delete (`deleted_at` thay vì DELETE):

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  deleted_at TIMESTAMP  -- NULL = chưa xóa
);
```

**Câu hỏi:**

a) Foreign Key có hoạt động với soft delete không?

b) Làm thế nào để đảm bảo Referential Integrity với soft delete?

c) Có cần Foreign Key constraint không nếu dùng soft delete?

---

### Câu A.3: Cross-database Foreign Key

**Câu hỏi:**

a) Foreign Key có thể reference đến table trong database khác không?

b) Nếu không thể, làm thế nào xử lý relationships giữa databases?

c) Trade-offs của việc không có Foreign Key giữa databases?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào dùng gì

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

