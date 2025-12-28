# Day-007: Solutions - Normalization (3NF)

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 3NF là gì?

**Đáp án:**

**3NF (Third Normal Form) là gì?**

3NF là dạng chuẩn hóa thứ ba, yêu cầu:
1. **Đã tuân thủ 2NF** (Second Normal Form)
2. **Không có Transitive Dependency** (Phụ thuộc bắc cầu)

**2 yêu cầu chính:**

1. **Tuân thủ 2NF**: Đã tuân thủ 1NF + không có Partial Dependency
2. **Không có Transitive Dependency**: Non-key column không phụ thuộc vào non-key column khác

**Tại sao cần 3NF?**

1. **Giảm redundancy**: Dữ liệu không trùng lặp
2. **Dễ maintain**: Update một chỗ
3. **Data integrity**: Đảm bảo dữ liệu nhất quán
4. **Tránh update anomalies**: Không phải update nhiều chỗ

---

### Câu 1.2: Transitive Dependency

**a) Transitive Dependency là gì?**

**Transitive Dependency** (Phụ thuộc bắc cầu) xảy ra khi:
- Một non-key column phụ thuộc vào **một non-key column khác** (không phải PK)
- Non-key column đó lại phụ thuộc vào Primary Key
- → Tạo ra "chuỗi phụ thuộc": PK → non-key A → non-key B

**b) Khi nào xảy ra Transitive Dependency?**

- ✅ Có non-key column phụ thuộc vào non-key column khác (không phải PK)

**c) Ví dụ cụ thể:**

```sql
-- ❌ VI PHẠM 3NF
CREATE TABLE orders (
  id INT PRIMARY KEY,           -- PK
  user_id INT,                   -- Non-key, phụ thuộc vào id
  user_name VARCHAR(100),        -- Non-key, phụ thuộc vào user_id (không phải PK)
  total_amount DECIMAL(10, 2)
);
```

**Phân tích:**
- **PK**: `id`
- **user_id**: Phụ thuộc vào `id` (PK) → OK
- **user_name**: Phụ thuộc vào `user_id` (không phải PK) → **Transitive Dependency**

**d) Transitive Dependency vs Partial Dependency:**

| Tiêu chí | Partial Dependency | Transitive Dependency |
|----------|-------------------|----------------------|
| **Xảy ra khi** | Composite Primary Key | Single Primary Key |
| **Phụ thuộc vào** | Một phần của PK | Non-key column → non-key column |
| **Giải quyết bởi** | 2NF | 3NF |

---

### Câu 1.3: 2NF vs 3NF

**a) Khi nào dừng ở 2NF? Khi nào cần 3NF?**

**Có thể dừng ở 2NF khi:**
- ✅ Không có Transitive Dependency
- ✅ Data warehouse (có thể denormalize)
- ✅ Read-only data

**Cần 3NF khi:**
- ✅ Có Transitive Dependency
- ✅ OLTP systems
- ✅ Frequent updates

**b) Table tuân thủ 3NF có tự động tuân thủ 2NF không?**

**Đáp án: CÓ**

**Lý do:**
- 3NF yêu cầu đã tuân thủ 2NF
- → Table tuân thủ 3NF tự động tuân thủ 2NF (và 1NF)

**c) OLTP system:**

**Nên tuân thủ 3NF**

**Lý do:**
- ✅ Data integrity quan trọng
- ✅ Frequent updates → 3NF dễ maintain
- ✅ Tránh update anomalies

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 3NF - Orders với User Info

**a) Phân tích vấn đề:**

1. **Transitive Dependency**:
   - `user_name` phụ thuộc vào `user_id` (không phải PK)
   - `user_email` phụ thuộc vào `user_id` (không phải PK)

2. **Data redundancy**:
   - `user_name`, `user_email` bị duplicate trong nhiều orders
   - Tốn storage không cần thiết

3. **Update anomalies**:
   - Đổi tên/email user → phải sửa nhiều rows
   - Dễ quên, dễ lỗi

4. **Inconsistency risk**:
   - Cùng user_id nhưng user_name/email khác nhau → inconsistency

**b) Schema tuân thủ 3NF:**

```sql
-- ✅ TUÂN THỦ 3NF: Tách thành 2 tables
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  user_name VARCHAR(100),
  user_email VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**c) Giải thích:**

- **Tuân thủ 3NF**: 
  - `users`: Single PK, không có Transitive Dependency → tự động tuân thủ 3NF
  - `orders`: Chỉ có `user_id` (reference), không có Transitive Dependency
- **Không có Transitive Dependency**: 
  - `user_name`, `user_email` đã tách ra `users` table
  - `orders` chỉ có `user_id` và `total_amount` (phụ thuộc trực tiếp vào PK)

---

### Câu 2.2: Vi phạm 3NF - Employees với Department Info

**a) Transitive Dependencies:**

1. **`department_name`**: Phụ thuộc vào `department_id` (không phải PK)
2. **`department_location`**: Phụ thuộc vào `department_id` (không phải PK)

**b) Schema tuân thủ 3NF:**

```sql
-- ✅ TUÂN THỦ 3NF: Tách thành 2 tables
CREATE TABLE departments (
  department_id INT PRIMARY KEY,
  department_name VARCHAR(100),
  department_location VARCHAR(100)
);

CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2),
  FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

**c) Query "tất cả employees trong department 'Engineering'":**

```sql
SELECT e.*
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE d.department_name = 'Engineering';
```

---

### Câu 2.3: Không vi phạm 3NF

**a) Có vi phạm 3NF không?**

**Đáp án: KHÔNG**

**Lý do:**
- Composite PK: `(order_id, product_id)`
- `quantity`: Phụ thuộc vào cả `(order_id, product_id)` → OK
- `price_at_order`: Phụ thuộc vào cả `(order_id, product_id)` → OK
- → **Không có Transitive Dependency** → Tuân thủ 3NF

**b) `price_at_order` phụ thuộc vào gì?**

**Đáp án: Phụ thuộc vào cả `(order_id, product_id)`**

**Lý do:**
- Cùng product trong orders khác nhau có thể có price khác nhau
- → `price_at_order` phụ thuộc vào cả PK, không phải chỉ một phần

**c) Có Transitive Dependency không?**

**Đáp án: KHÔNG**

**Lý do:**
- Tất cả columns phụ thuộc trực tiếp vào PK
- Không có column phụ thuộc vào non-key column khác
- → Không có Transitive Dependency

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Orders

**a) CREATE TABLE tuân thủ 3NF:**

```sql
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  user_name VARCHAR(100),
  user_email VARCHAR(100),
  user_phone VARCHAR(20)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**b) Giải thích:**

- **Tuân thủ 3NF**: 
  - `users`: Single PK, không có Transitive Dependency
  - `orders`: Chỉ có `user_id` (reference), không có Transitive Dependency
  - `user_name`, `user_email`, `user_phone` đã tách ra `users` table

**c) Query "tất cả orders của user 'John Doe'":**

```sql
SELECT o.*
FROM orders o
JOIN users u ON o.user_id = u.user_id
WHERE u.user_name = 'John Doe';
```

---

### Câu 3.2: Products với Categories

**a) CREATE TABLE tuân thủ 3NF:**

```sql
CREATE TABLE categories (
  category_id INT PRIMARY KEY,
  category_name VARCHAR(100),
  category_description TEXT
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  category_id INT,
  product_name VARCHAR(200),
  price DECIMAL(10, 2),
  FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

**b) Giải thích:**

- **Tuân thủ 3NF**: 
  - `categories`: Single PK, không có Transitive Dependency
  - `products`: Chỉ có `category_id` (reference), không có Transitive Dependency
  - `category_name`, `category_description` đã tách ra `categories` table

**c) Query "tất cả products trong category 'Electronics'":**

```sql
SELECT p.*
FROM products p
JOIN categories c ON p.category_id = c.category_id
WHERE c.category_name = 'Electronics';
```

---

### Câu 3.3: Employees với Departments và Locations

**a) CREATE TABLE tuân thủ 3NF:**

```sql
CREATE TABLE locations (
  location_id INT PRIMARY KEY,
  city VARCHAR(100),
  country VARCHAR(100)
);

CREATE TABLE departments (
  department_id INT PRIMARY KEY,
  department_name VARCHAR(100),
  location_id INT,
  FOREIGN KEY (location_id) REFERENCES locations(location_id)
);

CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2),
  FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

**b) Phân tích dependencies:**

**Levels of dependencies:**
1. **Level 1**: `employee` → `department` (employee phụ thuộc vào department)
2. **Level 2**: `department` → `location` (department phụ thuộc vào location)

**Có 2 levels of Transitive Dependencies** nếu không normalize:
- `employee` → `department_id` → `department_name`, `location_city`
- `department` → `location_id` → `city`, `country`

**c) Query "tất cả employees ở 'New York'":**

```sql
SELECT e.*
FROM employees e
JOIN departments d ON e.department_id = d.department_id
JOIN locations l ON d.location_id = l.location_id
WHERE l.city = 'New York';
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: 3NF và Performance

**a) So sánh:**

| Tiêu chí | Denormalized (A) | Normalized (B) |
|----------|------------------|----------------|
| **SELECT performance** | ✅ Nhanh (không cần JOIN) | ❌ Chậm hơn (cần JOIN) |
| **UPDATE performance** | ❌ Chậm (phải update nhiều rows) | ✅ Nhanh (chỉ update 1 row) |
| **Storage** | ❌ Tốn (duplicate data) | ✅ Tiết kiệm |
| **Data integrity** | ❌ Dễ inconsistency | ✅ Tốt |

**b) Khi nào Option A tốt hơn? Option B tốt hơn?**

**Option A (Denormalized) tốt hơn khi:**
- ✅ Read-heavy workloads
- ✅ Data warehouse
- ✅ Performance-critical
- ✅ Read-only data

**Option B (Normalized) tốt hơn khi:**
- ✅ OLTP systems
- ✅ Frequent updates
- ✅ Data integrity critical
- ✅ Storage matters

**c) Có thể dùng cả 2 không?**

**Đáp án: CÓ - Hybrid approach**

```sql
-- Normalized base tables
CREATE TABLE users (...);
CREATE TABLE orders (...);

-- Denormalized materialized view
CREATE MATERIALIZED VIEW orders_denormalized AS
SELECT 
  o.id,
  o.user_id,
  u.user_name,
  u.user_email,
  o.total_amount
FROM orders o
JOIN users u ON o.user_id = u.user_id;
```

---

### Câu 4.2: 3NF và Data Warehouse

**a) Có nên tuân thủ 3NF trong data warehouse không?**

**Đáp án: KHÔNG (thường)**

**Lý do:**
- Data warehouse: Read-heavy, analytics
- Performance quan trọng hơn storage
- Data đã clean → không cần real-time integrity
- Có thể denormalize để tăng performance

**b) Trade-offs:**

**Denormalize:**
- ✅ Query nhanh (ít JOINs)
- ❌ Storage tốn hơn
- ❌ Khó maintain

**c) Khi nào normalize? Khi nào denormalize?**

**Normalize khi:**
- ✅ Data integrity quan trọng
- ✅ Storage matters

**Denormalize khi:**
- ✅ Performance-critical
- ✅ Read-only data
- ✅ Analytics queries

---

### Câu 4.3: Multiple Levels of Dependencies

**a) Phân tích dependencies:**

**Dependencies:**
- `employee` → `department_id` → `department_name`, `location_id`
- `department` → `location_id` → `location_city`

**b) Có bao nhiêu levels?**

**Đáp án: 2 levels**

1. **Level 1**: employee → department
2. **Level 2**: department → location

**c) Làm thế nào normalize?**

**Tách thành 3 tables:**

```sql
CREATE TABLE locations (...);
CREATE TABLE departments (
  ...,
  location_id INT,
  FOREIGN KEY (location_id) REFERENCES locations(location_id)
);
CREATE TABLE employees (
  ...,
  department_id INT,
  FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

---

### Câu 4.4: 3NF và Foreign Keys

**a) Foreign Key có đảm bảo tuân thủ 3NF không?**

**Đáp án: KHÔNG trực tiếp**

**Lý do:**
- Foreign Key chỉ đảm bảo Referential Integrity
- 3NF là về dependencies, không phải về relationships
- → Foreign Key không đảm bảo tuân thủ 3NF

**b) Foreign Key có giải quyết Transitive Dependency không?**

**Đáp án: KHÔNG, nhưng giúp maintain relationships sau khi normalize**

**Lý do:**
- Foreign Key không giải quyết Transitive Dependency
- Nhưng sau khi normalize (tách tables), Foreign Key giúp maintain relationships

**c) Tại sao cần Foreign Key sau khi normalize?**

**Lý do:**
- Đảm bảo Referential Integrity
- Đảm bảo không có orphan records
- Self-documenting relationships

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 3NF

**a)**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2)
);
```

**Đáp án: ✅ Tuân thủ 3NF**

**Lý do:**
- Không có Transitive Dependency
- Tất cả columns phụ thuộc trực tiếp vào PK

---

**b)**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),
  total_amount DECIMAL(10, 2)
);
```

**Đáp án: ❌ Vi phạm 3NF**

**Lý do:**
- `user_name` phụ thuộc vào `user_id` (không phải PK)
- → Transitive Dependency

---

**c)**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Đáp án: ✅ Tuân thủ 3NF**

**Lý do:**
- Không có Transitive Dependency
- `quantity` phụ thuộc vào cả PK

---

### Câu 5.2: Sửa vi phạm 3NF

**a) Transitive Dependencies:**

1. **`category_name`**: Phụ thuộc vào `category_id` (không phải PK)
2. **`category_description`**: Phụ thuộc vào `category_id` (không phải PK)

**b) Schema tuân thủ 3NF:**

```sql
CREATE TABLE categories (
  category_id INT PRIMARY KEY,
  category_name VARCHAR(100),
  category_description TEXT
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  category_id INT,
  product_name VARCHAR(200),
  price DECIMAL(10, 2),
  FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

**c) Migrate data:**

```sql
-- Extract unique categories
INSERT INTO categories (category_id, category_name, category_description)
SELECT DISTINCT 
  category_id,
  MAX(category_name),
  MAX(category_description)
FROM old_products
GROUP BY category_id;

-- Migrate products
INSERT INTO products (id, category_id, product_name, price)
SELECT id, category_id, product_name, price
FROM old_products;
```

---

### Câu 5.3: Design schema tuân thủ 3NF

**a) CREATE TABLE:**

```sql
CREATE TABLE teachers (
  teacher_id INT PRIMARY KEY,
  teacher_name VARCHAR(100),
  teacher_email VARCHAR(100),
  department VARCHAR(100)
);

CREATE TABLE classes (
  class_id INT PRIMARY KEY,
  class_name VARCHAR(100),
  teacher_id INT,
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id)
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
  PRIMARY KEY (student_id, class_id),
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (class_id) REFERENCES classes(class_id)
);
```

**b) Giải thích:**

- **Tuân thủ 3NF**: 
  - Tất cả tables: Single PK, không có Transitive Dependency
  - `teacher_name`, `teacher_email`, `department` đã tách ra `teachers` table

**c) Query "tất cả students học class của teacher 'John Doe'":**

```sql
SELECT DISTINCT s.*
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN classes c ON e.class_id = c.class_id
JOIN teachers t ON c.teacher_id = t.teacher_id
WHERE t.teacher_name = 'John Doe';
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **3NF là gì?**
   - Đã tuân thủ 2NF + không có Transitive Dependency

2. **Transitive Dependency:**
   - Non-key column phụ thuộc vào non-key column khác

3. **Tại sao cần 3NF:**
   - Giảm redundancy, dễ maintain, data integrity

4. **Khi nào dừng ở 2NF:**
   - Không có Transitive Dependency, data warehouse

5. **Cách sửa:**
   - Tách columns có Transitive Dependency thành bảng riêng

---

### Câu 6.2: Hệ thống quản lý nhân sự

**a) Schema tuân thủ 3NF:**

```sql
CREATE TABLE locations (
  location_id INT PRIMARY KEY,
  city VARCHAR(100),
  country VARCHAR(100)
);

CREATE TABLE departments (
  department_id INT PRIMARY KEY,
  department_name VARCHAR(100),
  location_id INT,
  FOREIGN KEY (location_id) REFERENCES locations(location_id)
);

CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2),
  FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

**b) Giải thích:**

- **Tuân thủ 3NF**: 
  - Tất cả tables: Single PK, không có Transitive Dependency
  - `city`, `country` đã tách ra `locations` table
  - `department_name` đã tách ra `departments` table

**c) Query "tất cả employees ở 'New York'":**

```sql
SELECT e.*
FROM employees e
JOIN departments d ON e.department_id = d.department_id
JOIN locations l ON d.location_id = l.location_id
WHERE l.city = 'New York';
```

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: 3NF và BCNF

**a) BCNF là gì?**

**BCNF (Boyce-Codd Normal Form)** là dạng chuẩn hóa nâng cao của 3NF:
- Mọi determinant (column quyết định column khác) phải là candidate key

**Khác với 3NF:**
- 3NF: Cho phép non-key column là determinant
- BCNF: Yêu cầu mọi determinant phải là candidate key

**b) Khi nào cần BCNF?**

**Cần khi:**
- ✅ Có overlapping candidate keys
- ✅ Cần đảm bảo data integrity cao nhất

**3NF đủ khi:**
- ✅ Không có overlapping candidate keys
- ✅ Hầu hết cases trong production

**c) Trade-offs:**

**BCNF:**
- ✅ Data integrity cao hơn
- ❌ Phức tạp hơn
- ❌ Có thể tạo nhiều tables hơn

**3NF:**
- ✅ Đơn giản hơn
- ✅ Đủ cho hầu hết cases
- ❌ Có thể có một số edge cases

---

### Câu A.2: 3NF và Over-normalization

**a) Over-normalization là gì?**

**Over-normalization** là normalize quá mức, tạo ra quá nhiều tables nhỏ, làm queries phức tạp hơn.

**b) Có thể normalize quá mức không?**

**Đáp án: CÓ**

**Hậu quả:**
- ❌ Quá nhiều JOINs → queries chậm
- ❌ Schema phức tạp → khó maintain
- ❌ Performance giảm

**c) Làm thế nào biết khi nào dừng?**

**Dừng khi:**
- ✅ Đã tuân thủ 3NF (đủ cho hầu hết cases)
- ✅ Queries vẫn acceptable performance
- ✅ Schema không quá phức tạp

**KHÔNG nên normalize thêm khi:**
- ❌ Performance giảm đáng kể
- ❌ Queries quá phức tạp
- ❌ Schema quá phức tạp

---

### Câu A.3: 3NF và Query Complexity

**a) Normalized data có làm queries phức tạp hơn không?**

**Đáp án: CÓ, nhưng không đáng kể**

**Lý do:**
- Cần JOINs thay vì query một table
- Nhưng queries vẫn rõ ràng, dễ hiểu

**b) Làm thế nào optimize queries?**

1. **Indexes**: Đảm bảo có indexes trên Foreign Keys
2. **Query optimization**: Viết queries hiệu quả
3. **Materialized views**: Cache kết quả JOINs
4. **Consider denormalization**: Nếu thực sự cần performance

**c) Best practices:**

1. **Use aliases**: `FROM orders o JOIN users u`
2. **Index Foreign Keys**: Để JOIN nhanh
3. **Avoid unnecessary JOINs**: Chỉ JOIN tables cần thiết
4. **Use EXPLAIN**: Analyze query plans

---

## 📝 TÓM TẮT

### Key Learnings

1. **3NF yêu cầu**: Đã tuân thủ 2NF + không có Transitive Dependency
2. **Transitive Dependency**: Non-key column phụ thuộc vào non-key column khác
3. **Cách sửa**: Tách columns có Transitive Dependency thành bảng riêng
4. **Khi nào cần 3NF**: OLTP systems, có Transitive Dependency
5. **Khi nào dừng ở 2NF**: Không có Transitive Dependency, data warehouse

### Best Practices

✅ **Luôn tuân thủ 3NF** trong OLTP systems
✅ **Tách Transitive Dependencies** thành bảng riêng
✅ **Tạo Foreign Keys** để maintain relationships
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Consider denormalization** cho data warehouse nếu cần performance

---

**Chúc mừng hoàn thành Day-007!** 🎉

**Chuẩn bị cho Day-008: Data Types & Storage - Hiểu sâu về lưu trữ** 🚀

