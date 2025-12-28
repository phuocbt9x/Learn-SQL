# Day-005: Solutions - Normalization (1NF)

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: 1NF là gì?

**Đáp án:**

**1NF (First Normal Form) là gì?**

1NF là dạng chuẩn hóa đầu tiên, yêu cầu:
- Mỗi cell chỉ chứa một giá trị atomic (không thể chia nhỏ)
- Không có repeating groups (nhóm lặp lại)
- Mỗi row là unique (có Primary Key)

**3 yêu cầu chính:**

1. **Atomic values**: Mỗi cell = một giá trị đơn
2. **No repeating groups**: Không có columns như "phone1, phone2, phone3"
3. **Unique rows**: Mỗi row có Primary Key

**Tại sao cần 1NF?**

1. **Dễ query**: Không phải parse strings, arrays
2. **Dễ maintain**: Update/delete đơn giản
3. **Database có thể index**: Index trên atomic values hiệu quả
4. **Data integrity**: Đảm bảo dữ liệu nhất quán

---

### Câu 1.2: Atomic Values

**a) `name = "John Doe"`**

**Đáp án: TÙY context**

- **Atomic nếu**: Chỉ cần full name, không cần tách first/last name
- **Không atomic nếu**: Cần query/sort theo first name hoặc last name

**Ví dụ:**
```sql
-- Atomic (nếu chỉ cần full name)
name VARCHAR(100)  -- "John Doe"

-- Không atomic (nếu cần tách)
first_name VARCHAR(50),  -- "John"
last_name VARCHAR(50)    -- "Doe"
```

---

**b) `phones = "123-456-7890, 987-654-3210"`**

**Đáp án: KHÔNG atomic**

**Lý do:**
- Nhiều giá trị trong một cell
- Khó query: Không thể query "tất cả users có phone = '123-456-7890'"
- Khó update: Update một phone → phải parse string

**Cách sửa:**
```sql
-- Tách thành bảng riêng
CREATE TABLE user_phones (
  id INT PRIMARY KEY,
  user_id INT,
  phone VARCHAR(20)
);
```

---

**c) `email = "john@example.com"`**

**Đáp án: Atomic**

**Lý do:**
- Một giá trị đơn (một email)
- Không cần tách thành username và domain (trong hầu hết cases)
- Dễ query, dễ index

**Lưu ý:** Nếu cần query theo domain (`@example.com`), có thể tách, nhưng thường không cần.

---

**d) `address = "123 Main St, New York, NY 10001"`**

**Đáp án: TÙY context**

- **Atomic nếu**: Chỉ cần full address, không cần query theo city/state
- **Không atomic nếu**: Cần query "tất cả users ở New York" → nên tách thành `street`, `city`, `state`, `zip`

**Ví dụ:**
```sql
-- Atomic (nếu chỉ cần full address)
address VARCHAR(200)  -- "123 Main St, New York, NY 10001"

-- Không atomic (nếu cần query theo city/state)
street VARCHAR(100),  -- "123 Main St"
city VARCHAR(50),      -- "New York"
state VARCHAR(2),     -- "NY"
zip VARCHAR(10)       -- "10001"
```

---

**e) `tags = "sql,database,postgresql"`**

**Đáp án: KHÔNG atomic**

**Lý do:**
- Nhiều giá trị trong một cell (comma-separated)
- Khó query: `WHERE tags LIKE '%sql%'` có thể match "mysql" (không đúng)
- Khó update: Update một tag → phải parse và rebuild string

**Cách sửa:**
```sql
-- Tách thành bảng riêng (many-to-many)
CREATE TABLE tags (...);
CREATE TABLE product_tags (
  product_id INT,
  tag_id INT,
  PRIMARY KEY (product_id, tag_id)
);
```

---

**f) `price = 99.99`**

**Đáp án: Atomic**

**Lý do:**
- Một giá trị đơn (một số)
- Không thể chia nhỏ có ý nghĩa
- Dễ query, dễ index

---

**g) `full_name = "Nguyễn Văn A"` (trong context cần tách first name và last name)**

**Đáp án: KHÔNG atomic**

**Lý do:**
- Context yêu cầu tách first name và last name
- Cần query/sort theo first name hoặc last name
- Nên tách thành `first_name`, `last_name`

**Cách sửa:**
```sql
first_name VARCHAR(50),  -- "Nguyễn Văn"
last_name VARCHAR(50)    -- "A"
```

---

### Câu 1.3: Normalization

**a) Normalization là gì?**

**Normalization** là quá trình tổ chức dữ liệu để:
- Giảm redundancy (trùng lặp)
- Đảm bảo data integrity
- Tránh anomalies (insert/update/delete)

**Tại sao cần Normalization?**

1. **Giảm redundancy**: Không duplicate data → tiết kiệm storage, dễ maintain
2. **Data integrity**: Đảm bảo dữ liệu nhất quán
3. **Tránh anomalies**: Update một chỗ → không phải update nhiều chỗ

---

**b) Trade-offs:**

| Tiêu chí | Normalized | Denormalized |
|----------|------------|--------------|
| **Data integrity** | ✅ Tốt | ❌ Dễ inconsistency |
| **Storage** | ✅ Tiết kiệm | ❌ Tốn hơn |
| **Query performance** | ❌ Có thể chậm (nhiều JOINs) | ✅ Nhanh hơn (ít JOINs) |
| **Maintenance** | ✅ Dễ maintain | ❌ Khó maintain |
| **Flexibility** | ✅ Linh hoạt | ❌ Cứng nhắc |

---

**c) Khi nào normalize? Khi nào denormalize?**

**Nên normalize khi:**
- ✅ OLTP systems (transaction systems)
- ✅ Data integrity quan trọng
- ✅ Frequent updates
- ✅ Complex relationships

**Có thể denormalize khi:**
- ✅ Data warehouse (analytics, read-heavy)
- ✅ Performance-critical (cần query nhanh)
- ✅ Simple applications (ít relationships)
- ✅ Read-only data

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Vi phạm 1NF - Multiple values

**a) Phân tích vấn đề:**

1. **Khó query**: Không thể query "tất cả users có phone = '123-456-7890'"
2. **Khó update**: Update một phone → phải parse string
3. **Khó validate**: Khó đảm bảo format đúng
4. **Không thể index**: Index trên string không hiệu quả
5. **Data inconsistency**: Có thể có format khác nhau ("123-456-7890" vs "1234567890")

**b) Schema tuân thủ 1NF:**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE user_phones (
  id INT PRIMARY KEY,
  user_id INT,
  phone VARCHAR(20),
  phone_type VARCHAR(20),  -- "home", "work", "mobile"
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**c) Query "tất cả users có phone = '123-456-7890'":**

```sql
SELECT DISTINCT u.*
FROM users u
JOIN user_phones up ON u.id = up.user_id
WHERE up.phone = '123-456-7890';
```

**Ưu điểm:**
- Query chính xác (exact match)
- Có thể index trên `phone` → nhanh
- Dễ maintain

---

### Câu 2.2: Vi phạm 1NF - Repeating groups

**a) Phân tích vấn đề:**

1. **Giới hạn số lượng**: Chỉ có thể có tối đa 3 products
2. **Khó query**: "Tìm tất cả orders có product X" → phải check 3 columns
3. **Waste storage**: Nếu order chỉ có 1 product → 2 columns trống
4. **Khó maintain**: Thêm product thứ 4 → phải thêm columns

**b) Schema tuân thủ 1NF:**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_name VARCHAR(100),
  quantity INT,
  price DECIMAL(10, 2),
  FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

**c) Nếu order có 4 products:**

**Schema cũ:**
- ❌ Không thể lưu (chỉ có 3 columns)
- Phải thêm `product4_name`, `product4_quantity` → không scalable

**Schema mới:**
- ✅ Có thể lưu bao nhiêu products cũng được
- Chỉ cần thêm rows, không cần thêm columns
- Scalable và linh hoạt

---

### Câu 2.3: Vi phạm 1NF - Comma-separated values

**a) Phân tích vấn đề:**

1. **Khó query**: 
   ```sql
   -- ❌ Khó query chính xác
   SELECT * FROM products WHERE tags LIKE '%sql%';
   -- Có thể match "mysql" (không đúng)
   ```

2. **Khó update**: Update một tag → phải parse và rebuild string
3. **Khó validate**: Khó đảm bảo format đúng
4. **Không thể index**: Index trên string không hiệu quả

**b) Schema tuân thủ 1NF:**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng (many-to-many)
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200)
);

CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE
);

CREATE TABLE product_tags (
  product_id INT,
  tag_id INT,
  PRIMARY KEY (product_id, tag_id),
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (tag_id) REFERENCES tags(id)
);
```

**c) Query "tất cả products có tag 'sql'":**

```sql
SELECT p.*
FROM products p
JOIN product_tags pt ON p.id = pt.product_id
JOIN tags t ON pt.tag_id = t.id
WHERE t.name = 'sql';
```

**Ưu điểm:**
- Query chính xác (exact match)
- Có thể index trên `tags.name` → nhanh
- Dễ maintain
- Không duplicate tags (normalize)

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Orders

**a) CREATE TABLE tuân thủ 1NF:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2)
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT,
  quantity INT,
  price DECIMAL(10, 2),  -- Price tại thời điểm order (có thể khác với products.price)
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**b) Giải thích:**

- **Tuân thủ 1NF**: Mỗi cell = một giá trị atomic
- **Không có repeating groups**: Mỗi order_item là một row riêng
- **Không có multiple values**: Mỗi column = một giá trị

**c) Nếu muốn lưu thêm "discount":**

```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT,
  quantity INT,
  price DECIMAL(10, 2),
  discount DECIMAL(10, 2),  -- Thêm column discount
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**Lưu ý:** Discount là một giá trị atomic → chỉ cần thêm column, không cần tách thành bảng riêng.

---

### Câu 3.2: Blog Posts với Tags

**a) CREATE TABLE tuân thủ 1NF:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE posts (
  id INT PRIMARY KEY,
  author_id INT,
  title VARCHAR(300),
  content TEXT,
  created_at TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES users(id)
);

CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE
);

CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  PRIMARY KEY (post_id, tag_id),
  FOREIGN KEY (post_id) REFERENCES posts(id),
  FOREIGN KEY (tag_id) REFERENCES tags(id)
);
```

**b) Giải thích:**

- **Tuân thủ 1NF**: Mỗi cell = một giá trị atomic
- **Tags tách thành bảng riêng**: Many-to-many relationship
- **Không có comma-separated values**: Mỗi tag là một row riêng

**c) Query "tất cả posts có tag 'sql'":**

```sql
SELECT p.*
FROM posts p
JOIN post_tags pt ON p.id = pt.post_id
JOIN tags t ON pt.tag_id = t.id
WHERE t.name = 'sql';
```

---

### Câu 3.3: Users với Multiple Addresses

**a) CREATE TABLE tuân thủ 1NF:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE user_addresses (
  id INT PRIMARY KEY,
  user_id INT,
  address_type VARCHAR(20),  -- "home", "work", "shipping"
  street VARCHAR(100),
  city VARCHAR(50),
  state VARCHAR(2),
  zip VARCHAR(10),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**b) Có nên tách address thành nhiều columns?**

**Đáp án: CÓ**

**Lý do:**
- Cần query theo city, state → phải tách
- Dễ index trên `city`, `state` → query nhanh
- Dễ validate (state phải là 2 ký tự, zip phải là số)

**Nếu không tách:**
```sql
-- ❌ KHÔNG TỐT
address VARCHAR(200)  -- "123 Main St, New York, NY 10001"
-- Khó query "tất cả users ở New York"
```

---

**c) Query "tất cả users ở New York":**

```sql
SELECT DISTINCT u.*
FROM users u
JOIN user_addresses ua ON u.id = ua.user_id
WHERE ua.city = 'New York';
```

**Ưu điểm:**
- Query chính xác (exact match trên `city`)
- Có thể index trên `city` → nhanh
- Dễ maintain

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Atomic vs Non-atomic - Context matters

**a) Khi nào Option A atomic? Khi nào Option B tốt hơn?**

**Option A (full name) atomic khi:**
- ✅ Chỉ cần full name, không cần tách
- ✅ Không cần query/sort theo first/last name
- ✅ Simple applications

**Option B (separate columns) tốt hơn khi:**
- ✅ Cần query/sort theo first name hoặc last name
- ✅ Cần hiển thị "Hello, John!" (chỉ first name)
- ✅ Cần validate first name và last name riêng

**b) Quyết định atomic dựa trên gì?**

1. **Business requirements**: Cần query/sort theo gì?
2. **Use cases**: Ứng dụng dùng dữ liệu như thế nào?
3. **Queries**: Cần query theo phần nào của giá trị?
4. **Indexing**: Cần index trên phần nào?

**c) Ví dụ cụ thể:**

**Dùng Option A (full name):**
- Simple contact list: Chỉ cần hiển thị full name
- Không cần sort theo first/last name

**Dùng Option B (separate columns):**
- User profiles: Cần hiển thị "Hello, John!"
- Email templates: "Dear John, ..."
- Sort theo last name: "Doe, John" vs "Smith, Jane"

---

### Câu 4.2: JSON/Array trong 1NF

**a) Option A có vi phạm 1NF không?**

**Đáp án: TÙY interpretation**

**Strict 1NF:**
- ❌ Vi phạm: JSON là non-atomic (có thể parse thành nhiều values)

**Practical 1NF:**
- ✅ Có thể chấp nhận: Nếu attributes ít được query, schema linh hoạt

**b) So sánh:**

| Tiêu chí | JSON (Option A) | Normalized (Option B) |
|----------|----------------|----------------------|
| **Query performance** | ❌ Chậm (phải parse JSON) | ✅ Nhanh (index trên columns) |
| **Flexibility** | ✅ Linh hoạt (mỗi product có attributes khác) | ❌ Cứng nhắc (tất cả products có cùng structure) |
| **Data integrity** | ❌ Không có schema enforcement | ✅ Có constraints, validation |
| **Indexing** | ❌ Khó index (phải dùng GIN index) | ✅ Dễ index (index trên columns) |

**c) Khi nào dùng Option A? Option B?**

**Dùng JSON (Option A) khi:**
- ✅ Schema linh hoạt: Mỗi product có attributes khác nhau
- ✅ Rarely queried: Attributes ít được query
- ✅ Document database pattern: Phù hợp với document model

**Dùng Normalized (Option B) khi:**
- ✅ Fixed schema: Tất cả products có cùng attributes
- ✅ Frequently queried: Attributes thường được query
- ✅ Need indexing: Cần index trên attributes
- ✅ Data integrity critical: Cần đảm bảo data integrity

---

### Câu 4.3: Normalization vs Denormalization

**a) OLTP system:**

**Nên normalize**

**Lý do:**
- ✅ Data integrity quan trọng
- ✅ Frequent updates → normalized dễ maintain
- ✅ Complex relationships → normalized rõ ràng
- ✅ Transaction systems cần consistency

**b) Data warehouse:**

**Có thể denormalize**

**Lý do:**
- ✅ Read-heavy → denormalized query nhanh hơn (ít JOINs)
- ✅ Data đã clean → không cần real-time integrity
- ✅ Analytics queries → cần query nhanh
- ✅ Performance quan trọng hơn storage

**c) Trade-offs:**

**OLTP (Normalized):**
- ✅ Data integrity tốt
- ❌ Query có thể chậm (nhiều JOINs)
- ✅ Dễ maintain

**Data Warehouse (Denormalized):**
- ✅ Query nhanh (ít JOINs)
- ❌ Storage tốn hơn
- ❌ Khó maintain (có thể có inconsistency)

---

### Câu 4.4: 1NF và Performance

**a) Normalized data có ảnh hưởng đến performance không?**

**Đáp án: CÓ, nhưng thường không đáng kể**

**Ảnh hưởng:**
- **JOINs**: Normalized data cần nhiều JOINs → có thể chậm hơn
- **Indexes**: Có thể index trên normalized columns → nhanh
- **Storage**: Normalized tiết kiệm storage → cache tốt hơn

**b) Khi nào normalized chậm? Khi nào nhanh?**

**Chậm hơn khi:**
- ❌ Nhiều JOINs (5+ tables)
- ❌ Không có indexes phù hợp
- ❌ Complex queries

**Nhanh hơn khi:**
- ✅ Có indexes phù hợp
- ✅ Simple queries (1-2 JOINs)
- ✅ Cache hiệu quả (storage nhỏ hơn)

**c) Làm thế nào optimize?**

1. **Indexes**: Đảm bảo có indexes trên Foreign Keys và columns thường query
2. **Query optimization**: Viết queries hiệu quả, tránh unnecessary JOINs
3. **Caching**: Cache kết quả queries thường dùng
4. **Consider denormalization**: Nếu thực sự cần performance, có thể denormalize một số tables

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Nhận biết vi phạm 1NF

**a)**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**Đáp án: ✅ Tuân thủ 1NF**

**Lý do:**
- Mỗi cell = một giá trị atomic
- Không có repeating groups
- Có Primary Key

---

**b)**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  phones VARCHAR(200)  -- "123-456-7890, 987-654-3210"
);
```

**Đáp án: ❌ Vi phạm 1NF**

**Lý do:**
- `phones` chứa nhiều giá trị (comma-separated)
- Không atomic

---

**c)**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  product1 VARCHAR(100),
  product2 VARCHAR(100),
  product3 VARCHAR(100)
);
```

**Đáp án: ❌ Vi phạm 1NF**

**Lý do:**
- Repeating groups (`product1`, `product2`, `product3`)
- Giới hạn số lượng products

---

**d)**
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2),
  category VARCHAR(100)
);
```

**Đáp án: ✅ Tuân thủ 1NF**

**Lý do:**
- Mỗi cell = một giá trị atomic
- Không có repeating groups
- Có Primary Key

---

### Câu 5.2: Sửa vi phạm 1NF

**a) Schema tuân thủ 1NF:**

```sql
CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE courses (
  id INT PRIMARY KEY,
  name VARCHAR(100) UNIQUE
);

CREATE TABLE student_courses (
  student_id INT,
  course_id INT,
  enrolled_at TIMESTAMP,
  PRIMARY KEY (student_id, course_id),
  FOREIGN KEY (student_id) REFERENCES students(id),
  FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

**b) Migrate data:**

```sql
-- Parse và migrate
INSERT INTO students (id, name)
SELECT DISTINCT id, name FROM old_students;

-- Parse courses
INSERT INTO courses (name)
SELECT DISTINCT TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(courses, ',', n.n), ',', -1))
FROM old_students
CROSS JOIN (SELECT 1 as n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5) n
WHERE n.n <= (LENGTH(courses) - LENGTH(REPLACE(courses, ',', '')) + 1);

-- Create relationships
INSERT INTO student_courses (student_id, course_id)
SELECT 
  s.id,
  c.id,
  NOW()
FROM old_students s
CROSS JOIN (SELECT 1 as n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5) n
JOIN courses c ON c.name = TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(s.courses, ',', n.n), ',', -1))
WHERE n.n <= (LENGTH(s.courses) - LENGTH(REPLACE(s.courses, ',', '')) + 1);
```

**c) Query "tất cả students học course 'Math'":**

```sql
SELECT s.*
FROM students s
JOIN student_courses sc ON s.id = sc.student_id
JOIN courses c ON sc.course_id = c.id
WHERE c.name = 'Math';
```

---

### Câu 5.3: Design schema tuân thủ 1NF

**a) CREATE TABLE:**

```sql
CREATE TABLE books (
  id INT PRIMARY KEY,
  title VARCHAR(300),
  isbn VARCHAR(20) UNIQUE
);

CREATE TABLE authors (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE book_authors (
  book_id INT,
  author_id INT,
  PRIMARY KEY (book_id, author_id),
  FOREIGN KEY (book_id) REFERENCES books(id),
  FOREIGN KEY (author_id) REFERENCES authors(id)
);

CREATE TABLE categories (
  id INT PRIMARY KEY,
  name VARCHAR(100) UNIQUE
);

CREATE TABLE book_categories (
  book_id INT,
  category_id INT,
  PRIMARY KEY (book_id, category_id),
  FOREIGN KEY (book_id) REFERENCES books(id),
  FOREIGN KEY (category_id) REFERENCES categories(id)
);

CREATE TABLE members (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE loans (
  id INT PRIMARY KEY,
  member_id INT,
  loan_date DATE,
  return_date DATE,
  FOREIGN KEY (member_id) REFERENCES members(id)
);

CREATE TABLE loan_books (
  loan_id INT,
  book_id INT,
  PRIMARY KEY (loan_id, book_id),
  FOREIGN KEY (loan_id) REFERENCES loans(id),
  FOREIGN KEY (book_id) REFERENCES books(id)
);
```

**b) Giải thích:**

- **Tuân thủ 1NF**: Mỗi cell = một giá trị atomic
- **Many-to-many relationships**: Tách thành junction tables (`book_authors`, `book_categories`, `loan_books`)
- **Không có repeating groups**: Mỗi relationship là một row riêng

**c) Query "tất cả books của author 'John Doe'":**

```sql
SELECT b.*
FROM books b
JOIN book_authors ba ON b.id = ba.book_id
JOIN authors a ON ba.author_id = a.id
WHERE a.name = 'John Doe';
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **1NF là gì?**
   - Mỗi cell = một giá trị atomic
   - Không có repeating groups
   - Mỗi row có Primary Key

2. **Atomic value:**
   - Giá trị không thể chia nhỏ có ý nghĩa
   - Ví dụ atomic: `email = "john@ex.com"`
   - Ví dụ không atomic: `phones = "123, 456"`

3. **Tại sao cần 1NF:**
   - Dễ query, dễ maintain
   - Database có thể index
   - Data integrity

4. **Nhận biết vi phạm:**
   - Multiple values trong một cell
   - Repeating groups
   - Comma-separated values

5. **Cách sửa:**
   - Tách thành nhiều rows (tốt hơn)
   - Hoặc tách thành nhiều columns

---

### Câu 6.2: Hệ thống quản lý nhà hàng

**a) Schema tuân thủ 1NF:**

```sql
CREATE TABLE restaurants (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  address VARCHAR(200)
);

CREATE TABLE menus (
  id INT PRIMARY KEY,
  restaurant_id INT,
  name VARCHAR(100),
  FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);

CREATE TABLE menu_items (
  id INT PRIMARY KEY,
  menu_id INT,
  name VARCHAR(200),
  price DECIMAL(10, 2),
  FOREIGN KEY (menu_id) REFERENCES menus(id)
);

CREATE TABLE ingredients (
  id INT PRIMARY KEY,
  name VARCHAR(100) UNIQUE
);

CREATE TABLE item_ingredients (
  item_id INT,
  ingredient_id INT,
  PRIMARY KEY (item_id, ingredient_id),
  FOREIGN KEY (item_id) REFERENCES menu_items(id),
  FOREIGN KEY (ingredient_id) REFERENCES ingredients(id)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  restaurant_id INT,
  order_date TIMESTAMP,
  FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  item_id INT,
  quantity INT,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (item_id) REFERENCES menu_items(id)
);
```

**b) Giải thích:**

- **Tuân thủ 1NF**: Mỗi cell = một giá trị atomic
- **Many-to-many relationships**: Tách thành junction tables
- **Không có repeating groups**: Mỗi relationship là một row riêng

**c) Query "tất cả items có ingredient 'tomato'":**

```sql
SELECT mi.*
FROM menu_items mi
JOIN item_ingredients ii ON mi.id = ii.item_id
JOIN ingredients i ON ii.ingredient_id = i.id
WHERE i.name = 'tomato';
```

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: 1NF và NoSQL

**a) NoSQL có tuân thủ 1NF không?**

**Đáp án: KHÔNG (thường)**

**Lý do:**
- NoSQL (document databases) thường lưu nested structures
- Một document có thể chứa arrays, objects → không atomic
- NoSQL không yêu cầu 1NF (khác với RDBMS)

**b) Khi nào nên dùng NoSQL?**

- ✅ Schema linh hoạt (mỗi document có structure khác)
- ✅ Unstructured/semi-structured data
- ✅ Scale ngang (horizontal scaling)
- ✅ Read-heavy, write-heavy workloads

**c) Trade-offs:**

| Tiêu chí | Normalized RDBMS | NoSQL |
|----------|------------------|-------|
| **Data integrity** | ✅ Tốt | ❌ Phải tự enforce |
| **Query flexibility** | ❌ Cần JOINs | ✅ Flexible queries |
| **Scalability** | ❌ Scale dọc | ✅ Scale ngang |
| **Consistency** | ✅ ACID | ❌ Eventual consistency |

---

### Câu A.2: 1NF và Array Types

**a) Dùng ARRAY có vi phạm 1NF không?**

**Đáp án: CÓ (strict 1NF), nhưng có thể chấp nhận (practical)**

**Strict 1NF:**
- ❌ Vi phạm: ARRAY là non-atomic (có thể parse thành nhiều values)

**Practical:**
- ✅ Có thể chấp nhận: Nếu ít query, schema linh hoạt

**b) Khi nào dùng ARRAY? Khi nào normalize?**

**Dùng ARRAY khi:**
- ✅ Ít query trên array elements
- ✅ Schema linh hoạt
- ✅ PostgreSQL (hỗ trợ tốt ARRAY)

**Normalize khi:**
- ✅ Thường query trên elements
- ✅ Cần index trên elements
- ✅ Cần data integrity

**c) So sánh performance:**

**ARRAY:**
- Query: Phải dùng array functions → chậm hơn
- Index: Có thể dùng GIN index → acceptable

**Normalized:**
- Query: JOIN → có thể nhanh hơn (nếu có index)
- Index: Dễ index trên columns → nhanh

**Kết luận:** Normalized thường nhanh hơn cho queries phức tạp.

---

### Câu A.3: 1NF và Full-text Search

**a) Normalized hay denormalized tốt hơn cho full-text search?**

**Đáp án: Tùy vào use case**

**Normalized:**
- ✅ Có thể index trên từng tag
- ❌ Cần JOINs → có thể chậm

**Denormalized:**
- ✅ Có thể dùng full-text search index
- ❌ Khó maintain

**b) Có thể dùng cả 2 không?**

**Đáp án: CÓ - Hybrid approach**

```sql
-- Normalized cho integrity
CREATE TABLE products (...);
CREATE TABLE tags (...);
CREATE TABLE product_tags (...);

-- Denormalized cho search
CREATE TABLE products_search (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  tags_text TEXT,  -- "sql database postgresql"
  FULLTEXT INDEX (tags_text)
);
```

**Ưu điểm:**
- Normalized: Data integrity
- Denormalized: Full-text search nhanh

**Trade-off:**
- Phải sync 2 tables
- Phức tạp hơn

---

## 📝 TÓM TẮT

### Key Learnings

1. **1NF yêu cầu**: Mỗi cell = một giá trị atomic, không có repeating groups
2. **Atomic values**: Giá trị không thể chia nhỏ có ý nghĩa (phụ thuộc context)
3. **Vi phạm 1NF**: Multiple values, repeating groups, comma-separated values
4. **Cách sửa**: Tách thành nhiều rows (tốt hơn) hoặc nhiều columns
5. **1NF là BẮT BUỘC**: Mọi table production đều phải tuân thủ

### Best Practices

✅ **Luôn tuân thủ 1NF**: Mỗi cell = một giá trị atomic
✅ **Tách non-atomic values**: Thành nhiều rows hoặc nhiều columns
✅ **Consider business context**: Quyết định atomic dựa trên use case
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Test queries**: Đảm bảo có thể query dữ liệu dễ dàng

---

**Chúc mừng hoàn thành Day-005!** 🎉

**Chuẩn bị cho Day-006: Normalization - 2NF (Second Normal Form)** 🚀

