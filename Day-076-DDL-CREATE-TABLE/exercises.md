# Day-076: Bài Tập - DDL - CREATE TABLE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: CREATE TABLE là gì?

**Câu hỏi:**
- CREATE TABLE là gì?
- Tại sao cần CREATE TABLE?
- Khi nào dùng CREATE TABLE trong production?
- Hậu quả nếu thiếu constraints?

---

### Câu 1.2: Constraints

**Câu hỏi:**
- PRIMARY KEY là gì? Khi nào dùng?
- FOREIGN KEY là gì? Khi nào dùng?
- NOT NULL, UNIQUE, CHECK là gì?
- Hậu quả nếu thiếu mỗi constraint?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Table với Constraints

**Yêu cầu:**
Tạo table `employees` với:
- `id`: PRIMARY KEY, auto-increment
- `email`: NOT NULL, UNIQUE
- `name`: NOT NULL
- `department_id`: FOREIGN KEY đến table `departments`
- `salary`: CHECK (salary > 0)
- `status`: DEFAULT 'active'
- `created_at`: DEFAULT CURRENT_TIMESTAMP

**Tạo thêm table `departments` trước:**
- `id`: PRIMARY KEY
- `name`: NOT NULL, UNIQUE

---

### Câu 2.2: So sánh Performance

**Yêu cầu:**
1. Tạo table `products_v1` không có constraints và indexes
2. Tạo table `products_v2` có đầy đủ constraints và indexes
3. Insert 10,000 rows vào mỗi table
4. So sánh:
   - Thời gian insert
   - Thời gian query (SELECT WHERE id = ?)
   - Thời gian query (SELECT WHERE name LIKE ?)

**Đánh giá:**
- Trade-offs giữa constraints và performance
- Khi nào nên trade-off?

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Thiết kế Schema cho Blog System

**Yêu cầu:**
Thiết kế schema cho blog system với:
- Users (id, email, username, password_hash, created_at)
- Posts (id, user_id, title, content, published_at, status)
- Comments (id, post_id, user_id, content, created_at)
- Tags (id, name)
- Post_Tags (post_id, tag_id)

**Yêu cầu:**
- Đầy đủ constraints
- Indexes phù hợp
- Giải thích design decisions

---

### Câu 3.2: Refactor Table Design

**Yêu cầu:**
Có table `orders` hiện tại:
```sql
CREATE TABLE orders (
  id INTEGER,
  user_id INTEGER,
  product_id INTEGER,
  quantity INTEGER,
  price DECIMAL(10, 2),
  total DECIMAL(10, 2),
  status VARCHAR(20),
  created_at TIMESTAMP
);
```

**Vấn đề:**
- Thiếu constraints
- Thiếu indexes
- Thiếu data validation

**Yêu cầu:**
- Refactor với đầy đủ constraints
- Thêm indexes phù hợp
- Giải thích từng thay đổi

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Composite Primary Key

**Yêu cầu:**
Tạo table `order_items` với composite PRIMARY KEY:
- `order_id` + `product_id` là PRIMARY KEY
- Các constraints khác phù hợp

**Câu hỏi:**
- Khi nào dùng composite PRIMARY KEY?
- Trade-offs so với surrogate key?

---

### Câu 4.2: Conditional Constraints

**Yêu cầu:**
Tạo table `products` với:
- `price`: CHECK (price > 0)
- `discount_price`: CHECK (discount_price > 0 AND discount_price < price)
- `status`: CHECK (status IN ('active', 'inactive', 'archived'))

**Câu hỏi:**
- Làm thế nào validate complex business rules?
- Khi nào nên dùng CHECK constraint vs application logic?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

