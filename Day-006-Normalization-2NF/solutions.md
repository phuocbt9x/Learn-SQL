# Day-006: Solutions - Normalization (2NF)

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 2NF là gì?

**Đáp án:**

**2NF (Second Normal Form) là gì?**

2NF là dạng chuẩn hóa thứ hai, yêu cầu:
1. **Đã tuân thủ 1NF** (First Normal Form)
2. **Không có Partial Dependency** (Phụ thuộc một phần)

**2 yêu cầu chính:**

1. **Tuân thủ 1NF**: Mỗi cell = một giá trị atomic, không có repeating groups
2. **Không có Partial Dependency**: Non-key column không phụ thuộc vào một phần của Composite Primary Key

**Tại sao cần 2NF?**

1. **Giảm redundancy**: Dữ liệu không trùng lặp
2. **Dễ maintain**: Update một chỗ
3. **Data integrity**: Đảm bảo dữ liệu nhất quán
4. **Storage hiệu quả**: Tiết kiệm storage

---

### Câu 1.2: Partial Dependency

**a) Partial Dependency là gì?**

**Partial Dependency** (Phụ thuộc một phần) xảy ra khi:
- Primary Key là **Composite Key** (nhiều columns)
- Có **non-key column phụ thuộc vào chỉ một phần** của Composite Key

**b) Khi nào xảy ra Partial Dependency?**

- ✅ Có Composite Primary Key
- ✅ Có non-key column phụ thuộc vào chỉ một phần của PK

**c) Ví dụ cụ thể:**

```sql
-- ❌ VI PHẠM 2NF
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),  -- Phụ thuộc vào product_id (một phần của PK)
  quantity INT,
  PRIMARY KEY (order_id, product_id)  -- Composite Key
);
```

**Phân tích:**
- **PK**: `(order_id, product_id)` - Composite Key
- **product_name**: Phụ thuộc vào `product_id` (chỉ một phần của PK)
- → **Partial Dependency** → Vi phạm 2NF

**d) Table với Single Primary Key có thể vi phạm 2NF không?**

**Đáp án: KHÔNG**

**Lý do:**
- Partial Dependency chỉ xảy ra khi có Composite Primary Key
- Single Primary Key (chỉ 1 column) → không có "một phần" → không có Partial Dependency
- → **Tự động tuân thủ 2NF**

**Lưu ý:** Có thể vi phạm **3NF** (Transitive Dependency) - sẽ học ở Day-007.

---

### Câu 1.3: Normalization vs Denormalization

**a) Trade-offs:**

| Tiêu chí | Normalized (2NF) | Denormalized |
|----------|------------------|--------------|
| **Data redundancy** | ✅ Không duplicate | ❌ Có duplicate |
| **Update performance** | ✅ Update một chỗ | ❌ Update nhiều chỗ |
| **Query performance** | ❌ Có thể chậm (JOINs) | ✅ Nhanh hơn (ít JOINs) |
| **Storage** | ✅ Tiết kiệm | ❌ Tốn hơn |
| **Data integrity** | ✅ Tốt | ❌ Dễ inconsistency |

**b) Khi nào normalize? Khi nào denormalize?**

**Nên normalize khi:**
- ✅ OLTP systems (transaction systems)
- ✅ Frequent updates
- ✅ Data integrity critical
- ✅ Storage matters

**Có thể denormalize khi:**
- ✅ Data warehouse (analytics)
- ✅ Performance-critical (read-heavy)
- ✅ Read-only data
- ✅ Simple queries

**c) OLTP system:**

**Nên normalize**

**Lý do:**
- ✅ Data integrity quan trọng
- ✅ Frequent updates → normalized dễ maintain
- ✅ Complex relationships → normalized rõ ràng
- ✅ Transaction systems cần consistency

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 2NF - Order Items

**a) Phân tích vấn đề:**

1. **Partial Dependency**:
   - `product_name` phụ thuộc vào `product_id` (một phần của PK)
   - `product_price` phụ thuộc vào `product_id` (một phần của PK)

2. **Data redundancy**:
   - `product_name` và `product_price` bị duplicate trong nhiều orders
   - Tốn storage không cần thiết

3. **Update anomalies**:
   - Đổi tên/giá product → phải sửa nhiều rows
   - Dễ quên, dễ lỗi

4. **Inconsistency risk**:
   - Cùng product_id nhưng product_name khác nhau → inconsistency

**b) Schema tuân thủ 2NF:**

```sql
-- ✅ TUÂN THỦ 2NF: Tách thành 2 tables
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100),
  product_price DECIMAL(10, 2)
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**c) Giải thích:**

- **Tuân thủ 2NF**: 
  - `products`: Single Primary Key → tự động tuân thủ 2NF
  - `order_items`: Composite Key, nhưng không có Partial Dependency (quantity phụ thuộc vào cả PK)
- **Không có Partial Dependency**: 
  - `product_name`, `product_price` đã tách ra `products` table
  - `order_items` chỉ có `quantity` phụ thuộc vào cả `(order_id, product_id)`

---

### Câu 2.2: Vi phạm 2NF - Enrollments

**a) Partial Dependencies:**

1. **`student_name`**: Phụ thuộc vào `student_id` (một phần của PK)
2. **`course_name`**: Phụ thuộc vào `course_id` (một phần của PK)

**b) Schema tuân thủ 2NF:**

```sql
-- ✅ TUÂN THỦ 2NF: Tách thành 3 tables
CREATE TABLE students (
  student_id INT PRIMARY KEY,
  student_name VARCHAR(100)
);

CREATE TABLE courses (
  course_id INT PRIMARY KEY,
  course_name VARCHAR(100)
);

CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  enrollment_date DATE,
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id),
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

**c) Query "tất cả students học course 'Math'":**

```sql
SELECT s.*
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN courses c ON e.course_id = c.course_id
WHERE c.course_name = 'Math';
```

---

### Câu 2.3: Single Primary Key

**a) Có vi phạm 2NF không?**

**Đáp án: KHÔNG**

**Lý do:**
- Primary Key là Single Key (`id`) → không có Composite Key
- Không có Partial Dependency (vì không có "một phần")
- → **Tự động tuân thủ 2NF**

**b) Có vấn đề gì không?**

**Đáp án: CÓ - Có thể vi phạm 3NF**

**Lý do:**
- `user_name` phụ thuộc vào `user_id` (không phải PK)
- `user_id` phụ thuộc vào `id` (PK)
- → **Transitive Dependency** → Có thể vi phạm 3NF (sẽ học ở Day-007)

**c) Best practices:**

```sql
-- ✅ TỐT HƠN: Tách thành 2 tables
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Ưu điểm:**
- Tuân thủ 2NF và 3NF
- Không duplicate user info
- Dễ maintain

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Order Items

**a) CREATE TABLE tuân thủ 2NF:**

```sql
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100),
  product_category VARCHAR(50),
  product_price DECIMAL(10, 2)
);

CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (order_id) REFERENCES orders(order_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**b) Giải thích:**

- **Tuân thủ 2NF**: 
  - `products`: Single PK → tự động tuân thủ 2NF
  - `order_items`: Composite PK, nhưng không có Partial Dependency
  - `quantity` phụ thuộc vào cả `(order_id, product_id)` → OK

**c) Nếu muốn lưu "price tại thời điểm order":**

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  price_at_order DECIMAL(10, 2),  -- Price tại thời điểm order
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (order_id) REFERENCES orders(order_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**Lý do:**
- `price_at_order` phụ thuộc vào cả `(order_id, product_id)` → không phải Partial Dependency
- Cùng product trong orders khác nhau có thể có price khác nhau → hợp lý

---

### Câu 3.2: Project Members

**a) CREATE TABLE tuân thủ 2NF:**

```sql
CREATE TABLE projects (
  project_id INT PRIMARY KEY,
  project_name VARCHAR(200)
);

CREATE TABLE users (
  user_id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role_in_project VARCHAR(50),
  joined_date DATE,
  PRIMARY KEY (project_id, user_id),
  FOREIGN KEY (project_id) REFERENCES projects(project_id),
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**b) `role_in_project` có phải Partial Dependency không?**

**Đáp án: KHÔNG**

**Lý do:**
- `role_in_project` phụ thuộc vào cả `(project_id, user_id)`
- Một user có thể có role khác nhau trong các projects khác nhau
- → **KHÔNG phải Partial Dependency** → Tuân thủ 2NF

**c) Nếu `role_in_project` khác với `user.role`:**

**Đáp án: Giữ nguyên schema**

**Lý do:**
- `role_in_project` là role trong project cụ thể (có thể khác với default role)
- Phụ thuộc vào cả `(project_id, user_id)` → không phải Partial Dependency
- Schema hiện tại đã đúng

---

### Câu 3.3: Library Loans

**a) CREATE TABLE tuân thủ 2NF:**

```sql
CREATE TABLE members (
  member_id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE books (
  book_id INT PRIMARY KEY,
  title VARCHAR(300),
  author VARCHAR(200),
  isbn VARCHAR(20) UNIQUE
);

CREATE TABLE loans (
  loan_id INT PRIMARY KEY,
  member_id INT,
  loan_date DATE,
  return_date DATE,
  FOREIGN KEY (member_id) REFERENCES members(member_id)
);

CREATE TABLE loan_books (
  loan_id INT,
  book_id INT,
  due_date DATE,
  PRIMARY KEY (loan_id, book_id),
  FOREIGN KEY (loan_id) REFERENCES loans(loan_id),
  FOREIGN KEY (book_id) REFERENCES books(book_id)
);
```

**b) Giải thích:**

- **Tuân thủ 2NF**: 
  - `members`, `books`, `loans`: Single PK → tự động tuân thủ 2NF
  - `loan_books`: Composite PK, nhưng không có Partial Dependency
  - `due_date` phụ thuộc vào cả `(loan_id, book_id)` → OK

**c) Query "tất cả books của author 'John Doe'":**

```sql
SELECT b.*
FROM books b
WHERE b.author = 'John Doe';
```

**Hoặc nếu muốn lấy books đã được loan:**

```sql
SELECT DISTINCT b.*
FROM books b
JOIN loan_books lb ON b.book_id = lb.book_id
WHERE b.author = 'John Doe';
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: 2NF và Performance

**a) So sánh:**

| Tiêu chí | Denormalized (A) | Normalized (B) |
|----------|------------------|----------------|
| **SELECT performance** | ✅ Nhanh (không cần JOIN) | ❌ Chậm hơn (cần JOIN) |
| **UPDATE performance** | ❌ Chậm (phải update nhiều rows) | ✅ Nhanh (chỉ update 1 row) |
| **Storage** | ❌ Tốn (duplicate data) | ✅ Tiết kiệm |
| **Data integrity** | ❌ Dễ inconsistency | ✅ Tốt |

**b) Khi nào Option A tốt hơn? Option B tốt hơn?**

**Option A (Denormalized) tốt hơn khi:**
- ✅ Read-heavy workloads (nhiều SELECT, ít UPDATE)
- ✅ Data warehouse (analytics)
- ✅ Performance-critical (cần query nhanh)
- ✅ Read-only data

**Option B (Normalized) tốt hơn khi:**
- ✅ OLTP systems (transaction systems)
- ✅ Frequent updates
- ✅ Data integrity critical
- ✅ Storage matters

**c) Có thể dùng cả 2 không?**

**Đáp án: CÓ - Hybrid approach**

```sql
-- Normalized base tables (cho integrity)
CREATE TABLE products (...);
CREATE TABLE order_items (...);

-- Denormalized view/materialized view (cho performance)
CREATE MATERIALIZED VIEW order_items_denormalized AS
SELECT 
  oi.order_id,
  oi.product_id,
  p.product_name,
  p.product_category,
  p.product_price,
  oi.quantity
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id;
```

**Ưu điểm:**
- Base tables normalized → data integrity
- Materialized view denormalized → query nhanh
- Refresh view định kỳ → đảm bảo consistency

---

### Câu 4.2: 2NF và Data Warehouse

**a) Có nên tuân thủ 2NF trong data warehouse không?**

**Đáp án: KHÔNG (thường)**

**Lý do:**
- Data warehouse: Read-heavy, analytics
- Performance quan trọng hơn storage
- Data đã clean → không cần real-time integrity
- Có thể denormalize để tăng performance

**b) Trade-offs của denormalization:**

**Ưu điểm:**
- ✅ Query nhanh (ít JOINs)
- ✅ Đơn giản (ít tables)

**Nhược điểm:**
- ❌ Storage tốn hơn
- ❌ Khó maintain (nếu cần update)

**c) Khi nào normalize? Khi nào denormalize?**

**Normalize khi:**
- ✅ Data integrity quan trọng
- ✅ Storage matters
- ✅ Cần maintain dễ dàng

**Denormalize khi:**
- ✅ Performance-critical
- ✅ Read-only data
- ✅ Analytics queries

---

### Câu 4.3: Partial Dependency vs Transitive Dependency

**a) Sự khác biệt:**

| Tiêu chí | Partial Dependency | Transitive Dependency |
|----------|-------------------|----------------------|
| **Xảy ra khi** | Composite Primary Key | Single Primary Key |
| **Phụ thuộc vào** | Một phần của PK | Non-key column → non-key column |
| **Giải quyết bởi** | 2NF | 3NF |

**b) Ví dụ:**

**Partial Dependency (2NF):**
```sql
-- PK: (order_id, product_id)
-- product_name phụ thuộc vào product_id (một phần của PK)
```

**Transitive Dependency (3NF):**
```sql
-- PK: id
-- user_name phụ thuộc vào user_id
-- user_id phụ thuộc vào id (PK)
-- → user_name phụ thuộc vào id qua user_id (transitive)
```

**c) Đúng không?**

**Đáp án: ĐÚNG**

- **2NF**: Giải quyết Partial Dependency
- **3NF**: Giải quyết Transitive Dependency

---

### Câu 4.4: Composite Key và 2NF

**a) Table này có tuân thủ 2NF không?**

**Đáp án: CÓ**

**Lý do:**
- Composite PK: `(order_id, product_id)`
- `quantity` phụ thuộc vào cả `(order_id, product_id)` → không phải Partial Dependency
- → **Tuân thủ 2NF**

**b) Nếu thêm `product_name`:**

**Đáp án: CÓ vi phạm 2NF**

**Lý do:**
- `product_name` phụ thuộc vào `product_id` (một phần của PK)
- → **Partial Dependency** → Vi phạm 2NF

**c) Làm thế nào đảm bảo tuân thủ 2NF?**

**Tách thành 2 tables:**

```sql
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100)
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 2NF

**a)**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Đáp án: ✅ Tuân thủ 2NF**

**Lý do:**
- Composite PK, nhưng không có Partial Dependency
- `quantity` phụ thuộc vào cả PK

---

**b)**
```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  course_name VARCHAR(100),
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id)
);
```

**Đáp án: ❌ Vi phạm 2NF**

**Lý do:**
- `course_name` phụ thuộc vào `course_id` (một phần của PK)
- → Partial Dependency

---

**c)**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2)
);
```

**Đáp án: ✅ Tuân thủ 2NF**

**Lý do:**
- Single Primary Key → tự động tuân thủ 2NF

---

### Câu 5.2: Sửa vi phạm 2NF

**a) Partial Dependencies:**

1. **`project_name`**: Phụ thuộc vào `project_id` (một phần của PK)
2. **`task_name`**: Phụ thuộc vào `task_id` (một phần của PK)

**b) Schema tuân thủ 2NF:**

```sql
CREATE TABLE projects (
  project_id INT PRIMARY KEY,
  project_name VARCHAR(100)
);

CREATE TABLE tasks (
  task_id INT PRIMARY KEY,
  task_name VARCHAR(200)
);

CREATE TABLE project_tasks (
  project_id INT,
  task_id INT,
  assigned_to INT,
  PRIMARY KEY (project_id, task_id),
  FOREIGN KEY (project_id) REFERENCES projects(project_id),
  FOREIGN KEY (task_id) REFERENCES tasks(task_id)
);
```

**c) Migrate data:**

```sql
-- Extract unique projects
INSERT INTO projects (project_id, project_name)
SELECT DISTINCT project_id, MAX(project_name)
FROM old_project_tasks
GROUP BY project_id;

-- Extract unique tasks
INSERT INTO tasks (task_id, task_name)
SELECT DISTINCT task_id, MAX(task_name)
FROM old_project_tasks
GROUP BY task_id;

-- Migrate relationships
INSERT INTO project_tasks (project_id, task_id, assigned_to)
SELECT project_id, task_id, assigned_to
FROM old_project_tasks;
```

---

### Câu 5.3: Design schema tuân thủ 2NF

**a) CREATE TABLE:**

```sql
CREATE TABLE classes (
  class_id INT PRIMARY KEY,
  class_name VARCHAR(100),
  instructor VARCHAR(100)
);

CREATE TABLE students (
  student_id INT PRIMARY KEY,
  student_name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE enrollments (
  student_id INT,
  class_id INT,
  enrollment_date DATE,
  grade VARCHAR(2),
  PRIMARY KEY (student_id, class_id),
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (class_id) REFERENCES classes(class_id)
);
```

**b) Giải thích:**

- **Tuân thủ 2NF**: 
  - `classes`, `students`: Single PK → tự động tuân thủ 2NF
  - `enrollments`: Composite PK, nhưng không có Partial Dependency
  - `enrollment_date`, `grade` phụ thuộc vào cả `(student_id, class_id)` → OK

**c) Query "tất cả students trong class 'Math 101'":**

```sql
SELECT s.*
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN classes c ON e.class_id = c.class_id
WHERE c.class_name = 'Math 101';
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **2NF là gì?**
   - Đã tuân thủ 1NF + không có Partial Dependency

2. **Partial Dependency:**
   - Non-key column phụ thuộc vào một phần của Composite Primary Key

3. **Tại sao cần 2NF:**
   - Giảm redundancy, dễ maintain, data integrity

4. **Khi nào tự động tuân thủ:**
   - Single Primary Key → tự động tuân thủ 2NF

5. **Cách sửa:**
   - Tách columns có Partial Dependency thành bảng riêng

---

### Câu 6.2: Hệ thống quản lý dự án

**a) Schema tuân thủ 2NF:**

```sql
CREATE TABLE projects (
  project_id INT PRIMARY KEY,
  project_name VARCHAR(200)
);

CREATE TABLE users (
  user_id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role VARCHAR(50),
  joined_date DATE,
  PRIMARY KEY (project_id, user_id),
  FOREIGN KEY (project_id) REFERENCES projects(project_id),
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**b) Giải thích:**

- **Tuân thủ 2NF**: 
  - `projects`, `users`: Single PK → tự động tuân thủ 2NF
  - `project_members`: Composite PK, nhưng không có Partial Dependency
  - `role`, `joined_date` phụ thuộc vào cả `(project_id, user_id)` → OK

**c) Query "tất cả members của project 'Website Redesign'":**

```sql
SELECT u.*
FROM users u
JOIN project_members pm ON u.user_id = pm.user_id
JOIN projects p ON pm.project_id = p.project_id
WHERE p.project_name = 'Website Redesign';
```

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: 2NF và Materialized Views

**a) Materialized Views là gì?**

**Materialized View** là view được lưu trữ vật lý (có data thực tế), không phải view ảo.

**b) Có thể dùng Materialized Views để denormalize không?**

**Đáp án: CÓ**

```sql
-- Normalized base tables
CREATE TABLE products (...);
CREATE TABLE order_items (...);

-- Denormalized materialized view
CREATE MATERIALIZED VIEW order_items_denormalized AS
SELECT 
  oi.order_id,
  oi.product_id,
  p.product_name,
  p.product_category,
  oi.quantity
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id;

-- Refresh định kỳ
REFRESH MATERIALIZED VIEW order_items_denormalized;
```

**c) Trade-offs:**

**Ưu điểm:**
- ✅ Base tables normalized → data integrity
- ✅ Materialized view denormalized → query nhanh
- ✅ Có thể refresh → đảm bảo consistency

**Nhược điểm:**
- ❌ Phải refresh định kỳ → có thể stale
- ❌ Tốn storage (duplicate data)
- ❌ Phức tạp hơn

---

### Câu A.2: 2NF và Indexing

**a) Normalized data có ảnh hưởng đến indexing như thế nào?**

**Ảnh hưởng:**
- **Có thể index hiệu quả hơn**: Index trên normalized columns (ít duplicate)
- **Cần index trên Foreign Keys**: Để JOIN nhanh
- **Index trên denormalized data**: Có nhiều duplicate → index lớn hơn

**b) Index trên normalized vs denormalized:**

**Normalized:**
- ✅ Index nhỏ hơn (ít duplicate)
- ✅ Index hiệu quả hơn
- ❌ Cần nhiều indexes (trên nhiều tables)

**Denormalized:**
- ❌ Index lớn hơn (nhiều duplicate)
- ❌ Index kém hiệu quả hơn
- ✅ Chỉ cần ít indexes

**c) Optimize indexes với normalized data:**

1. **Index trên Foreign Keys**: Để JOIN nhanh
2. **Index trên columns thường query**: `product_name`, `category`
3. **Composite indexes**: Nếu query thường dùng nhiều columns
4. **Covering indexes**: Include columns thường SELECT

---

### Câu A.3: 2NF và Caching

**a) Normalized data có ảnh hưởng đến caching như thế nào?**

**Ảnh hưởng:**
- **Cache hiệu quả hơn**: Ít duplicate data → cache nhỏ hơn
- **Cache nhiều objects**: Có thể cache từng table riêng
- **Cache invalidation**: Phức tạp hơn (nhiều tables)

**b) Cache normalized vs denormalized:**

**Normalized:**
- ✅ Cache nhỏ hơn (ít duplicate)
- ✅ Có thể cache từng table
- ❌ Cache invalidation phức tạp

**Denormalized:**
- ❌ Cache lớn hơn (nhiều duplicate)
- ✅ Cache invalidation đơn giản (một object)
- ✅ Cache hit rate cao hơn (ít objects)

**c) Best practices:**

1. **Cache normalized data**: Cache từng table riêng
2. **Cache denormalized views**: Cache kết quả JOINs
3. **Cache invalidation strategy**: Invalidate khi có update
4. **TTL (Time To Live)**: Set TTL cho cache

---

## 📝 TÓM TẮT

### Key Learnings

1. **2NF yêu cầu**: Đã tuân thủ 1NF + không có Partial Dependency
2. **Partial Dependency**: Non-key column phụ thuộc vào một phần của Composite Primary Key
3. **Cách sửa**: Tách columns có Partial Dependency thành bảng riêng
4. **Single Primary Key**: Tự động tuân thủ 2NF
5. **Trade-off**: Normalize cho integrity, denormalize cho performance

### Best Practices

✅ **Luôn tuân thủ 2NF** trong OLTP systems
✅ **Tách Partial Dependencies** thành bảng riêng
✅ **Tạo Foreign Keys** để maintain relationships
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Consider denormalization** cho data warehouse nếu cần performance

---

**Chúc mừng hoàn thành Day-006!** 🎉

**Chuẩn bị cho Day-007: Normalization - 3NF (Third Normal Form)** 🚀

