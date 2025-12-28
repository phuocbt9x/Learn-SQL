# Day-007: Bài Tập - Normalization (3NF)

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về 3NF (Third Normal Form) và Transitive Dependency. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 3NF là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- 3NF (Third Normal Form) là gì?
- Yêu cầu của 3NF (2 yêu cầu chính)?
- Tại sao cần 3NF?

---

### Câu 1.2: Transitive Dependency

**Câu hỏi:**

a) Transitive Dependency là gì?

b) Khi nào xảy ra Transitive Dependency?

c) Cho ví dụ cụ thể về Transitive Dependency.

d) Transitive Dependency khác Partial Dependency như thế nào?

---

### Câu 1.3: 2NF vs 3NF

**Câu hỏi:**

a) Khi nào có thể dừng ở 2NF? Khi nào cần 3NF?

b) Table tuân thủ 3NF có tự động tuân thủ 2NF không? Tại sao?

c) OLTP system nên tuân thủ 2NF hay 3NF? Tại sao?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 3NF - Orders với User Info

**Tình huống:**

Table `orders` vi phạm 3NF:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),
  user_email VARCHAR(100),
  total_amount DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này (Transitive Dependency, redundancy, etc.).

b) Viết lại schema tuân thủ 3NF.

c) Giải thích tại sao schema mới tuân thủ 3NF.

---

### Câu 2.2: Vi phạm 3NF - Employees với Department Info

**Tình huống:**

Table `employees` vi phạm 3NF:

```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  department_name VARCHAR(100),
  department_location VARCHAR(100),
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Xác định Transitive Dependencies trong table này.

b) Viết lại schema tuân thủ 3NF.

c) Nếu muốn query "tất cả employees trong department 'Engineering'", viết query với schema mới.

---

### Câu 2.3: Không vi phạm 3NF

**Tình huống:**

Table `order_items`:

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  price_at_order DECIMAL(10, 2),
  PRIMARY KEY (order_id, product_id)
);
```

**Câu hỏi:**

a) Table này có vi phạm 3NF không? Tại sao?

b) `price_at_order` phụ thuộc vào gì?

c) Có Transitive Dependency không?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Orders

**Yêu cầu:** Thiết kế schema cho orders tuân thủ 3NF:

- Mỗi order có một user
- Mỗi user có: name, email, phone
- Mỗi order có: total_amount, created_at

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 3NF.

b) Giải thích tại sao thiết kế này tuân thủ 3NF.

c) Nếu muốn query "tất cả orders của user 'John Doe'", viết query.

---

### Câu 3.2: Products với Categories

**Yêu cầu:** Thiết kế schema cho products tuân thủ 3NF:

- Mỗi product có một category
- Mỗi category có: name, description
- Mỗi product có: name, price

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 3NF.

b) Giải thích tại sao thiết kế này tuân thủ 3NF.

c) Nếu muốn query "tất cả products trong category 'Electronics'", viết query.

---

### Câu 3.3: Employees với Departments và Locations

**Yêu cầu:** Thiết kế schema cho employees tuân thủ 3NF:

- Mỗi employee có một department
- Mỗi department có một location
- Mỗi location có: city, country
- Mỗi department có: name
- Mỗi employee có: name, salary

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 3NF.

b) Phân tích: Có bao nhiêu levels of dependencies? (employee → department → location)

c) Nếu muốn query "tất cả employees ở 'New York'", viết query.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: 3NF và Performance

**Tình huống:**

Bạn có 2 options:

**Option A: Denormalized (vi phạm 3NF)**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),  -- Duplicate
  total_amount DECIMAL(10, 2)
);
```

**Option B: Normalized (tuân thủ 3NF)**
```sql
CREATE TABLE users (...);
CREATE TABLE orders (...);
```

**Câu hỏi:**

a) So sánh 2 options về:
   - Query performance (SELECT với JOIN)
   - Update performance (UPDATE)
   - Storage
   - Data integrity

b) Trong trường hợp nào Option A tốt hơn? Option B tốt hơn?

c) Có thể dùng cả 2 không? (Hybrid approach)

---

### Câu 4.2: 3NF và Data Warehouse

**Tình huống:**

Bạn đang thiết kế data warehouse (analytics, không phải OLTP).

**Câu hỏi:**

a) Có nên tuân thủ 3NF trong data warehouse không? Tại sao?

b) Trade-offs của việc denormalize trong data warehouse?

c) Khi nào nên normalize? Khi nào nên denormalize?

---

### Câu 4.3: Multiple Levels of Dependencies

**Tình huống:**

Table có nhiều levels of dependencies:

```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  department_name VARCHAR(100),
  location_id INT,
  location_city VARCHAR(100),
  employee_name VARCHAR(100)
);
```

**Câu hỏi:**

a) Phân tích dependencies: employee → department → location

b) Có bao nhiêu levels of Transitive Dependencies?

c) Làm thế nào normalize table này?

---

### Câu 4.4: 3NF và Foreign Keys

**Tình huống:**

Sau khi normalize, bạn có:

```sql
CREATE TABLE users (...);
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**Câu hỏi:**

a) Foreign Key có đảm bảo tuân thủ 3NF không?

b) Foreign Key có giải quyết Transitive Dependency không?

c) Tại sao cần Foreign Key sau khi normalize?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 3NF

**Câu hỏi:** Trong các table sau, table nào vi phạm 3NF? Giải thích tại sao.

a)
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2)
);
```

b)
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),
  total_amount DECIMAL(10, 2)
);
```

c)
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

---

### Câu 5.2: Sửa vi phạm 3NF

**Tình huống:**

Table `products` vi phạm 3NF:

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  category_id INT,
  category_name VARCHAR(100),
  category_description TEXT,
  product_name VARCHAR(200),
  price DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Xác định Transitive Dependencies.

b) Viết lại schema tuân thủ 3NF.

c) Viết query migrate data từ schema cũ sang schema mới.

---

### Câu 5.3: Design schema tuân thủ 3NF

**Yêu cầu:** Thiết kế schema cho hệ thống quản lý trường học:

- Students (học sinh)
- Classes (lớp học)
- Teachers (giáo viên)
- Mỗi class có một teacher
- Mỗi teacher có: name, email, department
- Mỗi student enroll nhiều classes

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 3NF.

b) Giải thích tại sao thiết kế này tuân thủ 3NF.

c) Nếu muốn query "tất cả students học class của teacher 'John Doe'", viết query.

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. 3NF là gì? Yêu cầu gì?

2. Transitive Dependency là gì? Cách nhận biết?

3. Tại sao cần 3NF?

4. Khi nào có thể dừng ở 2NF?

5. Cách sửa vi phạm 3NF?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý nhân sự**:

- Employees (nhân viên)
- Departments (phòng ban)
- Locations (địa điểm)
- Mỗi employee có một department
- Mỗi department có một location
- Mỗi location có: city, country

**Yêu cầu:**

a) Thiết kế schema tuân thủ 3NF.

b) Giải thích tại sao thiết kế này tuân thủ 3NF.

c) Nếu muốn query "tất cả employees ở 'New York'", viết query.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: 3NF và BCNF

**Câu hỏi:**

a) BCNF (Boyce-Codd Normal Form) là gì? Khác với 3NF như thế nào?

b) Khi nào cần BCNF? Khi nào 3NF đủ?

c) Trade-offs giữa 3NF và BCNF?

---

### Câu A.2: 3NF và Over-normalization

**Câu hỏi:**

a) Over-normalization là gì?

b) Có thể normalize quá mức không? Hậu quả?

c) Làm thế nào biết khi nào dừng normalize?

---

### Câu A.3: 3NF và Query Complexity

**Câu hỏi:**

a) Normalized data (3NF) có làm queries phức tạp hơn không?

b) Làm thế nào optimize queries với normalized data?

c) Best practices cho viết queries với normalized schema?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào normalize, khi nào denormalize

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

