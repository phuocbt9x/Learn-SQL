# Day-015: Solutions - Review Phase 1

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này tổng hợp lại tất cả kiến thức từ Day-001 đến Day-014, giúp bạn chuẩn bị cho Phase 2.

---

## 🎯 BÀI TẬP 1: TỔNG HỢP KIẾN THỨC

### Câu 1.1: Database Foundations

**Database là gì? RDBMS là gì?**

- **Database**: Hệ thống lưu trữ và quản lý dữ liệu có tổ chức
- **RDBMS**: Relational Database Management System - hệ thống quản lý database quan hệ

**Table, Row, Column là gì?**

- **Table**: Cấu trúc dữ liệu 2 chiều (rows và columns)
- **Row**: Một record trong table
- **Column**: Một attribute trong table

**Primary Key là gì? Foreign Key là gì?**

- **Primary Key**: Column(s) đảm bảo uniqueness của mỗi row
- **Foreign Key**: Column(s) reference đến Primary Key của table khác

**Normalization (1NF, 2NF, 3NF) là gì?**

- **1NF**: Atomic values, không có multiple values trong một cell
- **2NF**: 1NF + không có partial dependencies
- **3NF**: 2NF + không có transitive dependencies

---

### Câu 1.2: Design & Performance

**a) Logical design vs Physical design?**

- **Logical**: ERD, relationships, business rules
- **Physical**: Tables, indexes, partitions, SQL statements

**b) Data types quan trọng như thế nào?**

- **Storage**: Ảnh hưởng đến storage size
- **Performance**: Ảnh hưởng đến query performance
- **Accuracy**: Ảnh hưởng đến data accuracy (NUMERIC vs FLOAT)

**c) Index là gì? Tại sao cần?**

- **Index**: Cấu trúc dữ liệu giúp queries nhanh hơn
- **Tại sao**: Queries không cần scan toàn bộ table

**d) SQL Execution Flow?**

- **Parser**: Phân tích SQL syntax
- **Planner**: Tạo execution plan
- **Executor**: Thực thi plan

---

### Câu 1.3: Operations & Reliability

**a) Connection vs Session?**

- **Connection**: Physical link giữa application và database
- **Session**: Logical context trong connection

**b) ACID là gì? 4 properties là gì?**

- **ACID**: Atomicity, Consistency, Isolation, Durability
- **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
- **Consistency**: Database luôn ở trạng thái hợp lệ
- **Isolation**: Transactions độc lập với nhau
- **Durability**: Data đã commit không bị mất

**c) Transaction là gì? BEGIN, COMMIT, ROLLBACK?**

- **Transaction**: Nhóm các operations được thực thi như một đơn vị duy nhất
- **BEGIN**: Bắt đầu transaction
- **COMMIT**: Kết thúc transaction thành công
- **ROLLBACK**: Hủy transaction

---

## 🔍 BÀI TẬP 2: THIẾT KẾ DATABASE

### Câu 2.1: E-commerce Database Design

**a) ERD (text description):**

```
Users
- id (PK)
- email
- name
- created_at

Categories
- id (PK)
- name

Products
- id (PK)
- name
- price
- category_id (FK → Categories)

Orders
- id (PK)
- user_id (FK → Users)
- total_amount
- created_at

Order Items
- id (PK)
- order_id (FK → Orders)
- product_id (FK → Products)
- quantity
```

**b) CREATE TABLE statements:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  name VARCHAR(100) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
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
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

**d) Normalization:**

- **1NF**: ✅ Atomic values
- **2NF**: ✅ No partial dependencies
- **3NF**: ✅ No transitive dependencies

---

### Câu 2.2: Blog System Database Design

**ERD:**

```
Users
- id (PK)
- name
- email

Posts
- id (PK)
- author_id (FK → Users)
- title
- content
- created_at

Tags
- id (PK)
- name

Post Tags (many-to-many)
- post_id (FK → Posts)
- tag_id (FK → Tags)

Comments
- id (PK)
- post_id (FK → Posts)
- user_id (FK → Users)
- content
- created_at
```

**CREATE TABLE statements:**

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

---

## 🧠 BÀI TẬP 3: TRANSACTION DESIGN

### Câu 3.1: Payment Transaction

**SQL transaction:**

```sql
BEGIN;
  -- 1. Check balance (với lock)
  SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;
  
  -- 2. Trừ tiền
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  
  -- 3. Tạo payment record
  INSERT INTO payments (user_id, amount) VALUES (1, 100);
  
  -- 4. Update order status
  UPDATE orders SET status = 'paid' WHERE id = 123;
COMMIT;
```

**ACID properties:**

- **Atomicity**: Cả 4 bước cùng thành công hoặc cùng rollback
- **Consistency**: Balance, constraints được đảm bảo
- **Isolation**: Transactions khác không thấy changes
- **Durability**: Data không bị mất sau COMMIT

**Xử lý lỗi:**

```sql
BEGIN;
  -- Operations
EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END;
```

---

### Câu 3.2: Order Transaction

**SQL transaction:**

```sql
BEGIN;
  -- 1. Tạo order
  INSERT INTO orders (user_id, total) VALUES (1, 100);
  
  -- 2. Trừ inventory (với lock)
  UPDATE products SET stock = stock - 1 WHERE id = 5 FOR UPDATE;
  
  -- 3. Tạo payment
  INSERT INTO payments (order_id, amount) VALUES (123, 100);
  
  -- 4. Send notification (async - không trong transaction)
COMMIT;
```

**Tối ưu:**

- Keep transaction short
- External operations (notifications) ngoài transaction
- Use appropriate isolation level

---

## 🎓 BÀI TẬP 4: PERFORMANCE OPTIMIZATION

### Câu 4.1: Query Optimization

**a) Query plan (dự đoán):**

```
Nested Loop (cost=0.43..25.00 rows=10 width=100)
  -> Index Scan using idx_users_email on users (cost=0.43..8.45 rows=1 width=50)
        Index Cond: (email = 'john@example.com')
  -> Index Scan using idx_orders_user_id on orders (cost=0.00..16.55 rows=10 width=50)
        Index Cond: (user_id = users.id)
  Sort (cost=1000.00..1000.00 rows=10 width=100)
    Sort Key: orders.created_at DESC
    Limit (cost=0.00..0.00 rows=10)
```

**b) Indexes cần thiết:**

```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);
```

**c) Tối ưu:**

- Composite index: `idx_orders_user_id_created_at` trên `orders(user_id, created_at DESC)`

**d) Performance improvements:**

- Index Scan thay vì Full Table Scan
- Nested Loop hiệu quả cho small result set
- Sort nhanh hơn với index

---

### Câu 4.2: Index Design

**Indexes:**

```sql
-- 1. Users email lookup
CREATE INDEX idx_users_email ON users(email);

-- 2. Orders by user and date
CREATE INDEX idx_orders_user_id_created_at ON orders(user_id, created_at DESC);

-- 3. Products by category and price
CREATE INDEX idx_products_category_price ON products(category_id, price);
```

**Lý do:**

- **idx_users_email**: Email lookup thường dùng
- **idx_orders_user_id_created_at**: Query orders by user và sort by date
- **idx_products_category_price**: Query products by category và filter by price

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Database**: Hệ thống lưu trữ và quản lý dữ liệu
2. **Primary Key**: Column(s) đảm bảo uniqueness
3. **Normalization**: 1NF, 2NF, 3NF - tránh data duplication và anomalies
4. **ACID**: Atomicity, Consistency, Isolation, Durability
5. **Transaction**: Nhóm operations được thực thi như một đơn vị duy nhất
6. **Index**: Cấu trúc dữ liệu giúp queries nhanh hơn
7. **Logical vs Physical**: ERD vs SQL statements
8. **SQL Execution Flow**: Parser → Planner → Executor
9. **Connection vs Session**: Physical link vs Logical context
10. **Data types**: Ảnh hưởng đến storage và performance

---

### Câu 5.2: Áp dụng thực tế

**ERD:**

```
Projects
- id (PK)
- name

Users
- id (PK)
- name
- email

Project Members
- project_id (FK → Projects)
- user_id (FK → Users)
- role

Tasks
- id (PK)
- project_id (FK → Projects)
- title
- status
```

**CREATE TABLE statements:**

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

**Indexes:**

```sql
CREATE INDEX idx_project_members_project_id ON project_members(project_id);
CREATE INDEX idx_project_members_user_id ON project_members(user_id);
CREATE INDEX idx_tasks_project_id ON tasks(project_id);
CREATE INDEX idx_tasks_status ON tasks(status);
```

---

## 📝 TÓM TẮT PHASE 1

### Key Learnings

1. **Database Foundations**: Database, RDBMS, Tables, Rows, Columns
2. **Data Integrity**: Primary Keys, Foreign Keys, Constraints, Normalization
3. **Performance**: Data Types, Indexes, Query Optimization
4. **Design**: Logical Design, Physical Design
5. **Operations**: Connections, Sessions, Transactions, ACID

### Best Practices

✅ **Design properly**: Logical → Physical design
✅ **Normalize**: 1NF, 2NF, 3NF
✅ **Choose right data types**: Storage và performance
✅ **Add indexes**: Cho performance
✅ **Use transactions**: Cho multi-step operations
✅ **Monitor**: Monitor connections, transactions, performance

---

**Chúc mừng hoàn thành Phase 1: Database Foundations!** 🎉

**Chuẩn bị cho Phase 2: Core SQL Query Language** 🚀

