# Day-006: Bài Tập - Normalization (2NF)

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về 2NF (Second Normal Form) và Partial Dependency. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 2NF là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- 2NF (Second Normal Form) là gì?
- Yêu cầu của 2NF (2 yêu cầu chính)?
- Tại sao cần 2NF?

---

### Câu 1.2: Partial Dependency

**Câu hỏi:**

a) Partial Dependency là gì?

b) Khi nào xảy ra Partial Dependency?

c) Cho ví dụ cụ thể về Partial Dependency.

d) Table với Single Primary Key có thể vi phạm 2NF không? Tại sao?

---

### Câu 1.3: Normalization vs Denormalization

**Câu hỏi:**

a) Trade-offs giữa Normalized (tuân thủ 2NF) và Denormalized data?

b) Khi nào nên normalize? Khi nào có thể denormalize?

c) OLTP system nên normalize hay denormalize? Tại sao?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 2NF - Order Items

**Tình huống:**

Table `order_items` vi phạm 2NF:

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),
  product_price DECIMAL(10, 2),
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này (Partial Dependency, redundancy, etc.).

b) Viết lại schema tuân thủ 2NF.

c) Giải thích tại sao schema mới tuân thủ 2NF.

---

### Câu 2.2: Vi phạm 2NF - Enrollments

**Tình huống:**

Table `enrollments` vi phạm 2NF:

```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  student_name VARCHAR(100),
  course_name VARCHAR(100),
  enrollment_date DATE,
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id)
);
```

**Câu hỏi:**

a) Xác định Partial Dependencies trong table này.

b) Viết lại schema tuân thủ 2NF.

c) Nếu muốn query "tất cả students học course 'Math'", viết query với schema mới.

---

### Câu 2.3: Single Primary Key

**Tình huống:**

Table `orders` có Single Primary Key:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,  -- Single Key
  user_id INT,
  user_name VARCHAR(100),
  total_amount DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Table này có vi phạm 2NF không? Tại sao?

b) Có vấn đề gì với table này không? (Hint: Có thể vi phạm 3NF)

c) Nếu muốn tuân thủ best practices, làm thế nào?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Order Items

**Yêu cầu:** Thiết kế schema cho order_items tuân thủ 2NF:

- Mỗi order có nhiều products
- Mỗi product có: name, category, price
- Mỗi order_item có: quantity

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 2NF.

b) Giải thích tại sao thiết kế này tuân thủ 2NF.

c) Nếu muốn lưu "price tại thời điểm order" (có thể khác với product.price), làm thế nào?

---

### Câu 3.2: Project Members

**Yêu cầu:** Thiết kế schema cho project members tuân thủ 2NF:

- Mỗi project có nhiều members
- Mỗi member có: name, email, role
- Mỗi project_member có: joined_date, role_in_project

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 2NF.

b) Phân tích: `role_in_project` có phải Partial Dependency không? Tại sao?

c) Nếu `role_in_project` khác với `member.role`, làm thế nào?

---

### Câu 3.3: Library Loans

**Yêu cầu:** Thiết kế schema cho library loans tuân thủ 2NF:

- Mỗi loan có một member và nhiều books
- Mỗi member có: name, email
- Mỗi book có: title, author, isbn
- Mỗi loan_book có: due_date

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 2NF.

b) Giải thích tại sao thiết kế này tuân thủ 2NF.

c) Nếu muốn query "tất cả books của author 'John Doe'", viết query.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: 2NF và Performance

**Tình huống:**

Bạn có 2 options:

**Option A: Denormalized (vi phạm 2NF)**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),  -- Duplicate
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Option B: Normalized (tuân thủ 2NF)**
```sql
CREATE TABLE products (...);
CREATE TABLE order_items (...);
```

**Câu hỏi:**

a) So sánh 2 options về:
   - Query performance (SELECT)
   - Update performance (UPDATE)
   - Storage
   - Data integrity

b) Trong trường hợp nào Option A tốt hơn? Option B tốt hơn?

c) Có thể dùng cả 2 không? (Hybrid approach)

---

### Câu 4.2: 2NF và Data Warehouse

**Tình huống:**

Bạn đang thiết kế data warehouse (analytics, không phải OLTP).

**Câu hỏi:**

a) Có nên tuân thủ 2NF trong data warehouse không? Tại sao?

b) Trade-offs của việc denormalize trong data warehouse?

c) Khi nào nên normalize? Khi nào nên denormalize?

---

### Câu 4.3: Partial Dependency vs Transitive Dependency

**Câu hỏi:**

a) Partial Dependency và Transitive Dependency khác nhau như thế nào?

b) Cho ví dụ cụ thể về mỗi loại.

c) 2NF giải quyết Partial Dependency, 3NF giải quyết Transitive Dependency - đúng không?

---

### Câu 4.4: Composite Key và 2NF

**Tình huống:**

Table có Composite Primary Key:

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Câu hỏi:**

a) Table này có tuân thủ 2NF không? Tại sao?

b) Nếu thêm column `product_name`, có vi phạm 2NF không?

c) Làm thế nào để đảm bảo tuân thủ 2NF với Composite Key?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 2NF

**Câu hỏi:** Trong các table sau, table nào vi phạm 2NF? Giải thích tại sao.

a)
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

b)
```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  course_name VARCHAR(100),
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id)
);
```

c)
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2)
);
```

---

### Câu 5.2: Sửa vi phạm 2NF

**Tình huống:**

Table `project_tasks` vi phạm 2NF:

```sql
CREATE TABLE project_tasks (
  project_id INT,
  task_id INT,
  project_name VARCHAR(100),
  task_name VARCHAR(200),
  assigned_to INT,
  PRIMARY KEY (project_id, task_id)
);
```

**Câu hỏi:**

a) Xác định Partial Dependencies.

b) Viết lại schema tuân thủ 2NF.

c) Viết query migrate data từ schema cũ sang schema mới.

---

### Câu 5.3: Design schema tuân thủ 2NF

**Yêu cầu:** Thiết kế schema cho hệ thống quản lý lớp học:

- Classes (lớp học)
- Students (học sinh)
- Enrollments (đăng ký) - một student có thể enroll nhiều classes
- Mỗi enrollment có: enrollment_date, grade

**Câu hỏi:**

a) Viết CREATE TABLE tuân thủ 2NF.

b) Giải thích tại sao thiết kế này tuân thủ 2NF.

c) Nếu muốn query "tất cả students trong class 'Math 101'", viết query.

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. 2NF là gì? Yêu cầu gì?

2. Partial Dependency là gì? Cách nhận biết?

3. Tại sao cần 2NF?

4. Khi nào table tự động tuân thủ 2NF?

5. Cách sửa vi phạm 2NF?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý dự án**:

- Projects (dự án)
- Users (users)
- Project Members (thành viên dự án) - một project có nhiều members
- Mỗi project_member có: role, joined_date

**Yêu cầu:**

a) Thiết kế schema tuân thủ 2NF.

b) Giải thích tại sao thiết kế này tuân thủ 2NF.

c) Nếu muốn query "tất cả members của project 'Website Redesign'", viết query.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: 2NF và Materialized Views

**Câu hỏi:**

a) Materialized Views là gì?

b) Có thể dùng Materialized Views để denormalize data mà vẫn giữ normalized base tables không?

c) Trade-offs của approach này?

---

### Câu A.2: 2NF và Indexing

**Câu hỏi:**

a) Normalized data (2NF) có ảnh hưởng đến indexing như thế nào?

b) Index trên normalized table vs denormalized table - cái nào hiệu quả hơn?

c) Làm thế nào optimize indexes với normalized data?

---

### Câu A.3: 2NF và Caching

**Câu hỏi:**

a) Normalized data có ảnh hưởng đến caching như thế nào?

b) Cache normalized data vs denormalized data - cái nào hiệu quả hơn?

c) Best practices cho caching với normalized data?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào normalize, khi nào denormalize

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

