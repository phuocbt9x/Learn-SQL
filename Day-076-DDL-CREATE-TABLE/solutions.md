# Day-076: Solutions - DDL - CREATE TABLE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: CREATE TABLE là gì?

**CREATE TABLE:** Câu lệnh DDL để tạo bảng mới với schema định nghĩa.

**Tại sao cần:** Định nghĩa cấu trúc dữ liệu, đảm bảo data integrity, performance.

**Khi nào dùng:** Tạo table mới, schema migration, partitioning.

**Hậu quả nếu thiếu constraints:**
- Không đảm bảo data integrity
- Duplicate values, orphan records
- Performance tệ (thiếu indexes)
- Khó maintain

---

### Câu 1.2: Constraints

**PRIMARY KEY:** Khóa chính, đảm bảo unique và NOT NULL, tự động tạo index. Dùng cho mỗi table.

**FOREIGN KEY:** Khóa ngoại, đảm bảo referential integrity. Dùng khi có quan hệ giữa tables.

**NOT NULL:** Đảm bảo column không thể NULL. Dùng khi column bắt buộc.

**UNIQUE:** Đảm bảo giá trị không trùng lặp. Dùng cho email, username, etc.

**CHECK:** Đảm bảo giá trị thỏa mãn điều kiện. Dùng cho business rules validation.

**Hậu quả nếu thiếu:**
- PRIMARY KEY: Không có cách định danh row duy nhất
- FOREIGN KEY: Orphan records, data inconsistency
- NOT NULL: NULL values → logic errors
- UNIQUE: Duplicate values
- CHECK: Invalid data

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Table với Constraints

**Solution:**

```sql
-- Tạo departments table trước
CREATE TABLE departments (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tạo employees table
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  department_id INTEGER NOT NULL REFERENCES departments(id),
  salary DECIMAL(10, 2) NOT NULL CHECK (salary > 0),
  status VARCHAR(20) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tạo indexes
CREATE INDEX idx_employees_email ON employees(email);
CREATE INDEX idx_employees_department_id ON employees(department_id);
CREATE INDEX idx_employees_status ON employees(status);
```

**Giải thích:**
- `id SERIAL PRIMARY KEY`: Auto-increment primary key
- `email NOT NULL UNIQUE`: Đảm bảo email unique và không NULL
- `department_id REFERENCES departments(id)`: Foreign key constraint
- `salary CHECK (salary > 0)`: Validate salary > 0
- Indexes cho columns thường query

---

### Câu 2.2: So sánh Performance

**Solution:**

```sql
-- Table không có constraints và indexes
CREATE TABLE products_v1 (
  id INTEGER,
  name VARCHAR(255),
  price DECIMAL(10, 2)
);

-- Table có đầy đủ constraints và indexes
CREATE TABLE products_v2 (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0)
);

CREATE INDEX idx_products_v2_name ON products_v2(name);
```

**Kết quả so sánh (Illustrative / approximate for educational purposes):**

| Operation | products_v1 | products_v2 |
|-----------|-------------|-------------|
| **Insert 10,000 rows** | ~500ms | ~600ms (chậm hơn do constraints) |
| **SELECT WHERE id = ?** | ~50ms (full scan) | ~1ms (index scan) |
| **SELECT WHERE name LIKE ?** | ~100ms (full scan) | ~5ms (index scan) |

**Đánh giá:**
- **Insert**: V1 nhanh hơn (không có constraint checks)
- **Query**: V2 nhanh hơn nhiều (có indexes)
- **Trade-off**: Constraints làm chậm insert nhưng cải thiện query performance đáng kể

**Kết luận:**
- Production nên dùng V2
- Query performance quan trọng hơn insert performance
- Constraints đảm bảo data integrity

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Thiết kế Schema cho Blog System

**Solution:**

```sql
-- Users table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  username VARCHAR(50) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);

-- Posts table
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id),
  title VARCHAR(255) NOT NULL,
  content TEXT NOT NULL,
  published_at TIMESTAMP,
  status VARCHAR(20) DEFAULT 'draft' CHECK (status IN ('draft', 'published', 'archived')),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_status ON posts(status);
CREATE INDEX idx_posts_published_at ON posts(published_at);

-- Comments table
CREATE TABLE comments (
  id SERIAL PRIMARY KEY,
  post_id INTEGER NOT NULL REFERENCES posts(id),
  user_id INTEGER NOT NULL REFERENCES users(id),
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
CREATE INDEX idx_comments_created_at ON comments(created_at);

-- Tags table
CREATE TABLE tags (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tags_name ON tags(name);

-- Post_Tags junction table
CREATE TABLE post_tags (
  post_id INTEGER NOT NULL REFERENCES posts(id),
  tag_id INTEGER NOT NULL REFERENCES tags(id),
  PRIMARY KEY (post_id, tag_id)
);

CREATE INDEX idx_post_tags_tag_id ON post_tags(tag_id);
```

**Design Decisions:**
- **Users**: Email và username unique, indexes cho login queries
- **Posts**: Status với CHECK constraint, indexes cho filtering
- **Comments**: Indexes cho queries theo post và user
- **Tags**: Unique name, index cho search
- **Post_Tags**: Composite PRIMARY KEY, index cho reverse lookup

---

### Câu 3.2: Refactor Table Design

**Solution:**

```sql
-- Refactored orders table
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id),
  product_id INTEGER NOT NULL REFERENCES products(id),
  quantity INTEGER NOT NULL CHECK (quantity > 0),
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0),
  total DECIMAL(10, 2) NOT NULL CHECK (total >= 0 AND total = price * quantity),
  status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'shipped', 'delivered', 'cancelled')),
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_product_id ON orders(product_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

**Thay đổi:**
1. **PRIMARY KEY**: Thêm SERIAL PRIMARY KEY cho id
2. **FOREIGN KEY**: Thêm REFERENCES cho user_id và product_id
3. **NOT NULL**: Thêm cho tất cả columns quan trọng
4. **CHECK constraints**: Validate quantity, price, total, status
5. **DEFAULT values**: Thêm cho status, timestamps
6. **Indexes**: Thêm cho foreign keys và columns thường query

**Lý do:**
- Đảm bảo data integrity
- Cải thiện query performance
- Dễ maintain và debug

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Composite Primary Key

**Solution:**

```sql
CREATE TABLE order_items (
  order_id INTEGER NOT NULL REFERENCES orders(id),
  product_id INTEGER NOT NULL REFERENCES products(id),
  quantity INTEGER NOT NULL CHECK (quantity > 0),
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0),
  PRIMARY KEY (order_id, product_id)
);

CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

**Khi nào dùng composite PRIMARY KEY:**
- Junction tables (many-to-many relationships)
- Khi combination của columns là unique và meaningful
- Khi không cần surrogate key

**Trade-offs:**
- **Pros**: Natural key, không cần thêm column
- **Cons**: Foreign keys phức tạp hơn, JOIN phức tạp hơn

**So với surrogate key:**
- Surrogate key đơn giản hơn cho JOINs
- Composite key tự nhiên hơn cho business logic

---

### Câu 4.2: Conditional Constraints

**Solution:**

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0),
  discount_price DECIMAL(10, 2) CHECK (discount_price IS NULL OR (discount_price > 0 AND discount_price < price)),
  status VARCHAR(20) NOT NULL DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'archived')),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Giải thích:**
- `price > 0`: Validate price luôn dương
- `discount_price`: Có thể NULL, nếu không NULL thì phải > 0 và < price
- `status`: Chỉ cho phép 3 giá trị

**Khi nào dùng CHECK vs application logic:**
- **CHECK constraint**: Simple validation, database-level
- **Application logic**: Complex validation, business rules phức tạp

**Best practice:**
- Dùng CHECK cho simple validation
- Dùng application logic cho complex business rules

---

**Chúc mừng hoàn thành Day-076!** 🎉

**Chuẩn bị cho Day-077: DDL - ALTER TABLE** 🚀

