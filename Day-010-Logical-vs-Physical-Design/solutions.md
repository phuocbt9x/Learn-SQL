# Day-010: Solutions - Logical vs Physical Design

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Logical vs Physical Design

**Đáp án:**

**Logical design là gì?**

Logical Design (Thiết kế logic) là thiết kế database ở mức khái niệm:
- Entities và relationships
- Business rules
- ERD (Entity Relationship Diagram)
- Không quan tâm đến implementation

**Physical design là gì?**

Physical Design (Thiết kế vật lý) là thiết kế database ở mức implementation:
- Tables và columns với data types
- Indexes và partitions
- Storage và performance optimization
- SQL statements (CREATE TABLE, CREATE INDEX)

**Sự khác biệt:**

| Tiêu chí | Logical | Physical |
|----------|---------|----------|
| **Level** | Conceptual | Implementation |
| **Focus** | Business | Performance |
| **Output** | ERD | SQL |
| **Technology** | Agnostic | Specific |

---

### Câu 1.2: ERD và SQL

**a) ERD thuộc logical hay physical?**

**Đáp án: Logical design**

**Lý do:** ERD mô tả entities và relationships ở mức khái niệm, không có implementation details.

**b) CREATE TABLE thuộc logical hay physical?**

**Đáp án: Physical design**

**Lý do:** CREATE TABLE là SQL statement để implement database, có data types cụ thể.

**c) Indexes thuộc logical hay physical?**

**Đáp án: Physical design**

**Lý do:** Indexes là implementation detail để optimize performance, không có trong logical design.

**d) Tại sao cần cả 2?**

**Lý do:**
- **Logical**: Hiểu business requirements, communication
- **Physical**: Implement database, optimize performance
- **Cả 2 đều cần**: Logical để hiểu, physical để implement

---

### Câu 1.3: Gap giữa Logical và Physical

**a) Gap là gì?**

**Gap** là khoảng cách giữa logical design (conceptual) và physical design (implementation).

**Ví dụ:**
- Logical: "Users có email"
- Physical: `email VARCHAR(100)` - cần quyết định size, data type

**b) Ví dụ cụ thể:**

**Gap 1: Data types**
- Logical: Không có data types
- Physical: Cần chọn VARCHAR(100), INT, etc.

**Gap 2: Indexes**
- Logical: Không có indexes
- Physical: Cần tạo indexes cho performance

**Gap 3: Partitions**
- Logical: Không có partitions
- Physical: Cần partitions để scale

**c) Làm thế nào bridge gap?**

**Quy trình:**
1. **Logical design**: ERD, relationships
2. **Physical design**: Tables, indexes, partitions
3. **Review**: Review cả 2
4. **Iterate**: Update cả 2 khi cần

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: ERD đẹp nhưng Physical Design sai

**a) Phân tích vấn đề:**

1. **Sai data types**: VARCHAR(255) cho ID, VARCHAR(1000) quá lớn
2. **Không có indexes**: Queries sẽ chậm
3. **Storage bloat**: Tốn storage không cần thiết
4. **Performance**: Query chậm, không có index

**b) Physical design đúng:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  name VARCHAR(100) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
```

**c) Đảm bảo physical design tốt:**

1. **Review data types**: Chọn đúng data types
2. **Add indexes**: Tạo indexes cho performance
3. **Add Foreign Keys**: Đảm bảo integrity
4. **Test performance**: Test queries trước khi deploy

---

### Câu 2.2: Logical Design thiếu thông tin

**a) Thiếu gì:**

- Data types
- Constraints (NOT NULL, UNIQUE)
- Indexes
- Business rules chi tiết

**b) Chuyển sang physical design:**

**Cần thêm:**
- Data types cho mỗi attribute
- Primary Keys, Foreign Keys
- Indexes
- Constraints

**c) Cần thêm thông tin:**

- Data types: Email là VARCHAR bao nhiêu?
- Constraints: Email có unique không?
- Indexes: Columns nào cần index?
- Business rules: Rules chi tiết

---

### Câu 2.3: Physical Design không match Logical Design

**a) Có match không?**

**Đáp án: KHÔNG**

**Lý do:**
- Logical: One-to-many (User has many Orders)
- Physical: Lưu order_ids trong users table → vi phạm 1NF

**b) Vấn đề:**

1. **Vi phạm 1NF**: Multiple values trong một cell
2. **Khó query**: Không thể query orders dễ dàng
3. **Khó maintain**: Update orders phức tạp

**c) Physical design đúng:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Chuyển từ Logical sang Physical

**a) Physical design:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(20)
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  category VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**b) Indexes:**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

**c) Giải thích:**

- **Data types**: Chọn đúng types (INT, VARCHAR, DECIMAL, TIMESTAMPTZ)
- **Indexes**: Foreign Keys và columns thường query
- **Foreign Keys**: Đảm bảo relationships

---

### Câu 3.2: E-commerce System

**a) ERD (text description):**

```
Users
- id
- email
- name

Categories
- id
- name

Products
- id
- name
- price
- category_id (relationship to Categories)

Orders
- id
- user_id (relationship to Users)
- total_amount
- created_at

Order Items
- id
- order_id (relationship to Orders)
- product_id (relationship to Products)
- quantity
```

**b) Physical design:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE categories (
  id INT PRIMARY KEY,
  name VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  category_id INT,
  FOREIGN KEY (category_id) REFERENCES categories(id)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**c) Indexes:**

```sql
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

---

### Câu 3.3: Blog System

**a) ERD:**

```
Users
- id
- name
- email

Posts
- id
- author_id (relationship to Users)
- title
- content
- created_at

Tags
- id
- name

Post Tags (many-to-many)
- post_id (relationship to Posts)
- tag_id (relationship to Tags)

Comments
- id
- post_id (relationship to Posts)
- user_id (relationship to Users)
- content
- created_at
```

**b) Physical design:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE posts (
  id INT PRIMARY KEY,
  author_id INT NOT NULL,
  title VARCHAR(300) NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES users(id)
);

CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  PRIMARY KEY (post_id, tag_id),
  FOREIGN KEY (post_id) REFERENCES posts(id),
  FOREIGN KEY (tag_id) REFERENCES tags(id)
);

CREATE TABLE comments (
  id INT PRIMARY KEY,
  post_id INT NOT NULL,
  user_id INT NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (post_id) REFERENCES posts(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**c) Indexes:**

```sql
CREATE INDEX idx_posts_author ON posts(author_id);
CREATE INDEX idx_posts_created_at ON posts(created_at);
CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Logical Design vs Physical Design

**a) So sánh:**

| Tiêu chí | Chỉ Logical | Cả 2 |
|----------|-------------|------|
| **Implementation** | ❌ Không thể implement | ✅ Có thể implement |
| **Performance** | ❌ Không optimize | ✅ Optimize được |
| **Maintainability** | ❌ Khó maintain | ✅ Dễ maintain |
| **Production ready** | ❌ Không sẵn sàng | ✅ Sẵn sàng |

**b) Option nào tốt hơn?**

**Đáp án: Option B (Cả 2)**

**Lý do:**
- Logical để hiểu requirements
- Physical để implement và optimize
- Cả 2 đều cần cho production

**c) Có thể chỉ có physical không?**

**Đáp án: CÓ, nhưng không tốt**

**Vấn đề:**
- Khó hiểu business requirements
- Khó communicate với stakeholders
- Khó maintain

**Best practice:** Có cả 2.

---

### Câu 4.2: Physical Design và Performance

**a) Performance khác nhau:**

**Design A (không có index):**
- Query: Full Table Scan → chậm

**Design B (có index):**
- Query: Index Scan → nhanh

**b) Tại sao physical design quan trọng?**

**Lý do:**
- Data types ảnh hưởng storage và performance
- Indexes ảnh hưởng query performance
- Partitions ảnh hưởng scale

**c) Logical design có ảnh hưởng không?**

**Đáp án: CÓ, nhưng gián tiếp**

**Lý do:**
- Logical design quyết định entities và relationships
- Physical design implement → ảnh hưởng performance
- Logical design tốt → physical design tốt hơn

---

### Câu 4.3: Iterative Design

**a) Có thể bắt đầu với physical không?**

**Đáp án: CÓ, nhưng không recommended**

**Vấn đề:**
- Dễ miss business requirements
- Khó communicate

**Best practice:** Bắt đầu với logical, rồi physical.

**b) Khi nào update?**

**Update logical khi:**
- Business requirements thay đổi
- Thêm entities/relationships mới

**Update physical khi:**
- Performance issues
- Cần optimize
- Thêm indexes/partitions

**c) Maintain cả 2:**

1. **Keep in sync**: Update cả 2 khi có thay đổi
2. **Document**: Document cả 2
3. **Review**: Review cả 2 định kỳ

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Vẽ ERD

**ERD (text description):**

```
Books
- id
- title
- isbn

Authors
- id
- name

Book Authors (many-to-many)
- book_id (relationship to Books)
- author_id (relationship to Authors)

Members
- id
- name
- email

Loans
- id
- member_id (relationship to Members)
- loan_date
- return_date

Loan Books (many-to-many)
- loan_id (relationship to Loans)
- book_id (relationship to Books)
```

---

### Câu 5.2: Chuyển ERD sang SQL

**a) CREATE TABLE:**

```sql
CREATE TABLE projects (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL
);

CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role VARCHAR(50),
  PRIMARY KEY (project_id, user_id),
  FOREIGN KEY (project_id) REFERENCES projects(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**b) Indexes:**

```sql
CREATE INDEX idx_project_members_project_id ON project_members(project_id);
CREATE INDEX idx_project_members_user_id ON project_members(user_id);
```

**c) Foreign Keys:**

```sql
-- Đã có trong CREATE TABLE
FOREIGN KEY (project_id) REFERENCES projects(id),
FOREIGN KEY (user_id) REFERENCES users(id)
```

---

### Câu 5.3: Review Physical Design

**a) Vấn đề:**

1. **Sai data types**: VARCHAR(255) cho ID, VARCHAR(100) cho số
2. **Không có indexes**: Queries sẽ chậm
3. **Không có Foreign Keys**: Không đảm bảo integrity

**b) Physical design tốt hơn:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

**c) Cải thiện:**

1. **Data types**: INT cho ID, DECIMAL cho số, TIMESTAMPTZ cho date
2. **Indexes**: Index trên Foreign Keys và columns thường query
3. **Foreign Keys**: Đảm bảo integrity
4. **Constraints**: NOT NULL, DEFAULT values

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Logical design**: Conceptual level, ERD, relationships
2. **Physical design**: Implementation level, tables, indexes
3. **Gap**: Khoảng cách giữa conceptual và implementation
4. **Bridge gap**: Chuyển từ logical sang physical đúng cách
5. **Performance**: Physical design ảnh hưởng trực tiếp đến performance

---

### Câu 6.2: Hệ thống quản lý dự án

**a) ERD:**

```
Projects
- id
- name

Users
- id
- name
- email

Project Members
- project_id (relationship to Projects)
- user_id (relationship to Users)
- role

Tasks
- id
- project_id (relationship to Projects)
- title
- status
```

**b) Physical design:**

```sql
CREATE TABLE projects (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL
);

CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role VARCHAR(50),
  PRIMARY KEY (project_id, user_id),
  FOREIGN KEY (project_id) REFERENCES projects(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE tasks (
  id INT PRIMARY KEY,
  project_id INT NOT NULL,
  title VARCHAR(200) NOT NULL,
  status VARCHAR(20),
  FOREIGN KEY (project_id) REFERENCES projects(id)
);
```

**c) Indexes:**

```sql
CREATE INDEX idx_project_members_project_id ON project_members(project_id);
CREATE INDEX idx_project_members_user_id ON project_members(user_id);
CREATE INDEX idx_tasks_project_id ON tasks(project_id);
CREATE INDEX idx_tasks_status ON tasks(status);
```

---

## 📝 TÓM TẮT

### Key Learnings

1. **Logical design**: Conceptual level, ERD, relationships
2. **Physical design**: Implementation level, tables, indexes
3. **Gap**: Logical không đủ, cần physical design tốt
4. **Bridge gap**: Chuyển từ logical sang physical đúng cách
5. **Cả 2 đều cần**: Logical để hiểu, physical để implement

### Best Practices

✅ **Start with logical**: Bắt đầu với logical design
✅ **Then physical**: Chuyển sang physical design
✅ **Review both**: Review cả logical và physical
✅ **Optimize physical**: Optimize physical design cho performance
✅ **Keep in sync**: Maintain cả 2 khi có thay đổi

---

**Chúc mừng hoàn thành Day-010!** 🎉

**Chuẩn bị cho Day-011: SQL Execution Flow - High-level** 🚀

