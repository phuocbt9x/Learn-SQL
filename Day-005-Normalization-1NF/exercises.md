# Day-005: Bài Tập - Normalization (1NF)

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về 1NF (First Normal Form) và Normalization. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 1NF là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- 1NF (First Normal Form) là gì?
- Yêu cầu của 1NF (3 yêu cầu chính)?
- Tại sao cần 1NF?

---

### Câu 1.2: Atomic Values

**Câu hỏi:** Trong các giá trị sau, giá trị nào là atomic? Giá trị nào không atomic? Giải thích tại sao.

a) `name = "John Doe"`

b) `phones = "123-456-7890, 987-654-3210"`

c) `email = "john@example.com"`

d) `address = "123 Main St, New York, NY 10001"`

e) `tags = "sql,database,postgresql"`

f) `price = 99.99`

g) `full_name = "Nguyễn Văn A"` (trong context cần tách first name và last name)

---

### Câu 1.3: Normalization

**Câu hỏi:**

a) Normalization là gì? Tại sao cần Normalization?

b) Trade-offs giữa Normalized và Denormalized data?

c) Khi nào nên normalize? Khi nào có thể denormalize?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 1NF - Multiple values

**Tình huống:**

Table `users` có column `phones` lưu nhiều số điện thoại:

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  phones VARCHAR(200)  -- "123-456-7890, 987-654-3210"
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại schema tuân thủ 1NF.

c) Nếu muốn query "tất cả users có phone = '123-456-7890'", làm thế nào với schema mới?

---

### Câu 2.2: Vi phạm 1NF - Repeating groups

**Tình huống:**

Table `orders` có repeating groups:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  product1_name VARCHAR(100),
  product1_quantity INT,
  product2_name VARCHAR(100),
  product2_quantity INT,
  product3_name VARCHAR(100),
  product3_quantity INT
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại schema tuân thủ 1NF.

c) Nếu order có 4 products, làm thế nào với schema cũ? Với schema mới?

---

### Câu 2.3: Vi phạm 1NF - Comma-separated values

**Tình huống:**

Table `products` có column `tags` lưu tags dạng comma-separated:

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  tags VARCHAR(500)  -- "sql,database,postgresql"
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại schema tuân thủ 1NF.

c) Nếu muốn query "tất cả products có tag 'sql'", làm thế nào với schema mới?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Orders

**Yêu cầu:** Thiết kế schema cho orders với:
- Mỗi order có một user
- Mỗi order có nhiều products (với quantity và price)
- Mỗi order có total amount

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 1NF.

b) Giải thích tại sao thiết kế này tuân thủ 1NF.

c) Nếu muốn lưu thêm "discount" cho mỗi product trong order, làm thế nào?

---

### Câu 3.2: Blog Posts với Tags

**Yêu cầu:** Thiết kế schema cho blog posts với:
- Mỗi post có một author
- Mỗi post có nhiều tags
- Mỗi post có title và content

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 1NF.

b) Giải thích tại sao thiết kế này tuân thủ 1NF.

c) Nếu muốn query "tất cả posts có tag 'sql'", viết query.

---

### Câu 3.3: Users với Multiple Addresses

**Yêu cầu:** Thiết kế schema cho users với:
- Mỗi user có nhiều addresses (home, work, shipping)
- Mỗi address có: street, city, state, zip

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 1NF.

b) Có nên tách address thành nhiều columns (street, city, state, zip) không? Tại sao?

c) Nếu muốn query "tất cả users ở New York", làm thế nào?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Atomic vs Non-atomic - Context matters

**Tình huống:**

Có 2 cách lưu name:

**Option A: Full name**
```sql
name VARCHAR(100)  -- "John Doe"
```

**Option B: Separate columns**
```sql
first_name VARCHAR(50),  -- "John"
last_name VARCHAR(50)   -- "Doe"
```

**Câu hỏi:**

a) Khi nào Option A là atomic? Khi nào Option B tốt hơn?

b) Quyết định atomic dựa trên gì? (Business context, queries, etc.)

c) Cho ví dụ cụ thể khi nào dùng Option A, khi nào dùng Option B.

---

### Câu 4.2: JSON/Array trong 1NF

**Tình huống:**

Table `products` có 2 cách thiết kế:

**Option A: JSON column**
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  attributes JSONB  -- {"color": "red", "size": "L"}
);
```

**Option B: Normalized**
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200)
);

CREATE TABLE product_attributes (
  id INT PRIMARY KEY,
  product_id INT,
  attribute_name VARCHAR(50),
  attribute_value VARCHAR(200)
);
```

**Câu hỏi:**

a) Option A có vi phạm 1NF không? Tại sao?

b) So sánh 2 options về:
   - Query performance
   - Flexibility
   - Data integrity
   - Indexing

c) Khi nào nên dùng Option A? Khi nào nên dùng Option B?

---

### Câu 4.3: Normalization vs Denormalization

**Tình huống:**

Bạn đang thiết kế database cho e-commerce system.

**Câu hỏi:**

a) OLTP system (transaction) - nên normalize hay denormalize? Tại sao?

b) Data warehouse (analytics) - nên normalize hay denormalize? Tại sao?

c) Trade-offs giữa normalized và denormalized trong mỗi trường hợp?

---

### Câu 4.4: 1NF và Performance

**Câu hỏi:**

a) Normalized data (1NF) có ảnh hưởng đến performance không? Tại sao?

b) Khi nào normalized data chậm hơn? Khi nào nhanh hơn?

c) Làm thế nào optimize performance với normalized data?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 1NF

**Câu hỏi:** Trong các table sau, table nào vi phạm 1NF? Giải thích tại sao.

a)
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

b)
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  phones VARCHAR(200)  -- "123-456-7890, 987-654-3210"
);
```

c)
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  product1 VARCHAR(100),
  product2 VARCHAR(100),
  product3 VARCHAR(100)
);
```

d)
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2),
  category VARCHAR(100)
);
```

---

### Câu 5.2: Sửa vi phạm 1NF

**Tình huống:**

Table `students` vi phạm 1NF:

```sql
CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  courses VARCHAR(500)  -- "Math, Physics, Chemistry"
);
```

**Câu hỏi:**

a) Viết lại schema tuân thủ 1NF.

b) Viết query migrate data từ schema cũ sang schema mới.

c) Viết query "tất cả students học course 'Math'" với schema mới.

---

### Câu 5.3: Design schema tuân thủ 1NF

**Yêu cầu:** Thiết kế schema cho hệ thống quản lý thư viện:

- Books (sách)
- Authors (tác giả) - một book có thể có nhiều authors
- Categories (danh mục) - một book có thể có nhiều categories
- Members (thành viên)
- Loans (mượn sách) - một loan có thể mượn nhiều books

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 1NF.

b) Giải thích tại sao thiết kế này tuân thủ 1NF.

c) Nếu muốn query "tất cả books của author 'John Doe'", viết query.

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. 1NF là gì? Yêu cầu gì?

2. Atomic value là gì? Cho ví dụ atomic và không atomic.

3. Tại sao cần 1NF?

4. Làm thế nào nhận biết vi phạm 1NF?

5. Cách sửa vi phạm 1NF?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý nhà hàng**:

- Restaurants (nhà hàng)
- Menus (thực đơn) - một restaurant có nhiều menus
- Menu Items (món ăn) - một menu có nhiều items
- Ingredients (nguyên liệu) - một item có nhiều ingredients
- Orders (đơn hàng) - một order có nhiều items

**Yêu cầu:**

a) Thiết kế schema tuân thủ 1NF.

b) Giải thích tại sao thiết kế này tuân thủ 1NF.

c) Nếu muốn query "tất cả items có ingredient 'tomato'", viết query.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: 1NF và NoSQL

**Câu hỏi:**

a) NoSQL databases (MongoDB, etc.) có tuân thủ 1NF không? Tại sao?

b) Khi nào nên dùng NoSQL thay vì normalized relational database?

c) Trade-offs giữa normalized RDBMS và NoSQL?

---

### Câu A.2: 1NF và Array Types

**Câu hỏi:**

a) PostgreSQL có ARRAY type. Dùng ARRAY có vi phạm 1NF không?

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  tags TEXT[]  -- Array of strings
);
```

b) Khi nào nên dùng ARRAY? Khi nào nên normalize?

c) So sánh ARRAY vs normalized table về performance?

---

### Câu A.3: 1NF và Full-text Search

**Câu hỏi:**

a) Nếu cần full-text search trên tags, normalized hay denormalized tốt hơn?

b) Có thể dùng cả 2 không? (Normalized cho integrity, denormalized cho search)

c) Trade-offs của mỗi approach?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào normalize, khi nào denormalize

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

