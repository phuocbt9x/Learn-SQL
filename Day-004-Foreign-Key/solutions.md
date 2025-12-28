# Day-004: Solutions - Foreign Key

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Foreign Key là gì?

**Đáp án:**

**Foreign Key là gì?**

Foreign Key (Khóa ngoại) là một hoặc nhiều columns trong table reference đến **Primary Key** (hoặc UNIQUE column) của table khác.

**Tại sao cần Foreign Key?**

1. **Tạo mối quan hệ**: Liên kết tables với nhau
2. **Đảm bảo Referential Integrity**: Không có orphan records
3. **Database tự enforce**: Không cần check trong application code
4. **Self-documenting**: Schema tự giải thích relationships

**Foreign Key reference đến gì?**

Foreign Key phải reference đến:
- **Primary Key** của table khác (thường dùng nhất)
- **UNIQUE column** của table khác (cũng được, nhưng ít dùng)

**KHÔNG thể** reference đến column không có UNIQUE constraint.

---

### Câu 1.2: Referential Integrity

**a) Referential Integrity là gì?**

Referential Integrity (Tính toàn vẹn tham chiếu) là đảm bảo rằng **mọi Foreign Key value đều tồn tại** trong referenced table.

**Nói cách khác:** Không thể có "orphan records" - records reference đến records không tồn tại.

**b) Tại sao quan trọng?**

1. **Data consistency**: Dữ liệu luôn nhất quán giữa các tables
2. **No orphan records**: Không có records "mồ côi"
3. **Query reliability**: JOINs luôn trả về kết quả đúng
4. **Business logic correctness**: Đảm bảo business rules

**c) Làm thế nào Foreign Key đảm bảo Referential Integrity?**

Foreign Key constraint đảm bảo:

1. **INSERT**: Không thể insert Foreign Key value không tồn tại
   ```sql
   -- ❌ ERROR: user_id = 999 không tồn tại
   INSERT INTO orders (user_id, total_amount) VALUES (999, 100.00);
   ```

2. **UPDATE**: Không thể update Foreign Key value thành giá trị không tồn tại
   ```sql
   -- ❌ ERROR: user_id = 999 không tồn tại
   UPDATE orders SET user_id = 999 WHERE id = 1;
   ```

3. **DELETE**: Không thể xóa referenced record nếu có Foreign Keys reference đến (trừ khi dùng CASCADE)
   ```sql
   -- ❌ ERROR: Có orders reference đến user_id = 1
   DELETE FROM users WHERE id = 1;
   ```

**d) Ví dụ vi phạm Referential Integrity:**

```sql
-- Users
id | name
1  | John
2  | Jane

-- Orders (VI PHẠM)
id | user_id | total_amount
1  | 1       | 100.00  -- ✅ OK
2  | 999     | 200.00  -- ❌ VI PHẠM: user_id = 999 không tồn tại
3  | 2       | 150.00  -- ✅ OK
```

**Order với `user_id = 999` là orphan record** - vi phạm Referential Integrity.

---

### Câu 1.3: ON DELETE Actions

**ON DELETE CASCADE:**

Khi xóa record trong referenced table, tự động xóa tất cả records trong referencing table có Foreign Key reference đến.

**Ví dụ:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Xóa user
DELETE FROM users WHERE id = 1;
-- Tự động xóa TẤT CẢ orders có user_id = 1
```

**Khi nào dùng:** Child records không có giá trị nếu không có parent (ví dụ: orders không có giá trị nếu không có user).

---

**ON DELETE RESTRICT:**

KHÔNG cho phép xóa record trong referenced table nếu có Foreign Keys reference đến.

**Ví dụ:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);

-- Cố gắng xóa user
DELETE FROM users WHERE id = 1;
-- ❌ ERROR: Cannot delete user because there are orders referencing it

-- Phải xóa orders trước
DELETE FROM orders WHERE user_id = 1;
DELETE FROM users WHERE id = 1;  -- ✅ OK
```

**Khi nào dùng:** Child records quan trọng, cần giữ lại (audit, history). **Đây là DEFAULT**.

---

**ON DELETE SET NULL:**

Khi xóa record trong referenced table, set Foreign Key column trong referencing table thành NULL.

**Ví dụ:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- Phải cho phép NULL
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);

-- Xóa user
DELETE FROM users WHERE id = 1;
-- Tự động set user_id = NULL cho tất cả orders có user_id = 1
-- Orders vẫn còn, nhưng user_id = NULL
```

**Khi nào dùng:** Optional relationship, muốn giữ lại child records nhưng đánh dấu "không có parent".

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Table không có Foreign Key

**a) Phân tích vấn đề:**

1. **Orphan records**: Có thể insert order với `user_id` không tồn tại
2. **Data inconsistency**: Dữ liệu không nhất quán
3. **Khó maintain**: Phải tự check trong application code
4. **Dễ lỗi**: Developer quên check → insert invalid data

**b) CREATE TABLE với Foreign Key:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);
```

**c) Nếu muốn xóa user → tự động xóa orders:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

**Lưu ý:** CASCADE có thể nguy hiểm nếu không cẩn thận (một DELETE có thể xóa nhiều records).

---

### Câu 2.2: Chọn sai ON DELETE Action

**a) Tại sao CASCADE không phù hợp:**

- Orders cần giữ lại cho audit/history
- Xóa user → xóa orders → mất dữ liệu quan trọng
- Không thể recover sau khi xóa

**b) Nên dùng ON DELETE RESTRICT:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);
```

**Lý do:**
- Không cho phép xóa user nếu có orders
- Developer phải explicit xóa orders trước
- An toàn hơn, không mất dữ liệu

**c) Nếu muốn giữ lại orders nhưng đánh dấu user đã bị xóa:**

**Option 1: SET NULL**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- Phải cho phép NULL
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);
```

**Option 2: Soft delete (TỐT HƠN)**

```sql
-- Users dùng soft delete
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  deleted_at TIMESTAMP  -- NULL = chưa xóa
);

-- Orders không cần SET NULL
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);

-- Query chỉ lấy users chưa xóa
SELECT * FROM users WHERE deleted_at IS NULL;
```

**Recommendation:** Dùng soft delete thay vì SET NULL (giữ lại data integrity tốt hơn).

---

### Câu 2.3: Orphan Records

**a) Query tìm orphan records:**

```sql
SELECT o.*
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;
```

**b) Fix orphan records:**

**Option 1: Xóa orphan orders**

```sql
DELETE FROM orders
WHERE user_id NOT IN (SELECT id FROM users);
-- Hoặc
DELETE o
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;
```

**Option 2: Assign orphan orders đến default user**

```sql
-- Tạo default user nếu chưa có
INSERT INTO users (id, name) 
VALUES (0, 'Deleted User')
ON DUPLICATE KEY UPDATE name = name;

-- Assign orphan orders
UPDATE orders
SET user_id = 0
WHERE user_id NOT IN (SELECT id FROM users);
```

**c) Ngăn chặn orphan records:**

**Thêm Foreign Key constraint:**

```sql
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);
```

**Sau khi thêm Foreign Key:**
- Không thể insert order với `user_id` không tồn tại
- Database tự động enforce → không có orphan records

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce System

**a) CREATE TABLE với Foreign Keys:**

```sql
-- Users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Products
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2)
);

-- Orders
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);

-- Order Items
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT,
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT
);
```

**b) Giải thích ON DELETE actions:**

- **`orders.user_id` → `users.id` (RESTRICT)**: 
  - Không cho xóa user nếu có orders
  - Orders quan trọng cho audit → cần giữ lại

- **`order_items.order_id` → `orders.id` (CASCADE)**:
  - Xóa order → tự động xóa order_items
  - Order items không có giá trị nếu không có order

- **`order_items.product_id` → `products.id` (RESTRICT)**:
  - Không cho xóa product nếu có order_items
  - Có thể cần giữ lại product history

**c) Nếu muốn xóa user → tự động xóa orders và order_items:**

```sql
-- Orders
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE

-- Order Items (không đổi)
FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
```

**Kết quả:**
- Xóa user → tự động xóa orders (CASCADE)
- Xóa orders → tự động xóa order_items (CASCADE)
- **Cẩn thận:** Một DELETE có thể xóa nhiều records!

---

### Câu 3.2: Blog System

**a) CREATE TABLE với Foreign Keys:**

```sql
-- Users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Posts
CREATE TABLE posts (
  id INT PRIMARY KEY,
  author_id INT,  -- Có thể NULL (SET NULL)
  title VARCHAR(300),
  content TEXT,
  FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE SET NULL
);

-- Comments
CREATE TABLE comments (
  id INT PRIMARY KEY,
  post_id INT NOT NULL,
  user_id INT,  -- Có thể NULL (SET NULL)
  content TEXT,
  FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);

-- Tags
CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE
);

-- Post Tags
CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  PRIMARY KEY (post_id, tag_id),
  FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
  FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE
);
```

**b) Giải thích ON DELETE actions:**

- **`posts.author_id` → `users.id` (SET NULL)**:
  - Xóa author → set `author_id = NULL`
  - Post vẫn còn, nhưng không có author

- **`comments.post_id` → `posts.id` (CASCADE)**:
  - Xóa post → tự động xóa comments
  - Comments không có giá trị nếu không có post

- **`comments.user_id` → `users.id` (SET NULL)**:
  - Xóa user (commenter) → set `user_id = NULL`
  - Comment vẫn còn, nhưng không biết user là ai

- **`post_tags.post_id` → `posts.id` (CASCADE)**:
  - Xóa post → tự động xóa post_tags
  - Post tags không có giá trị nếu không có post

- **`post_tags.tag_id` → `tags.id` (CASCADE)**:
  - Xóa tag → tự động xóa post_tags
  - Post tags không có giá trị nếu không có tag

**c) Vấn đề với SET NULL cho posts.author_id:**

**Vấn đề:** Business rule nói "Post phải có author", nhưng SET NULL cho phép `author_id = NULL`.

**Giải pháp:**

**Option 1: Dùng RESTRICT thay vì SET NULL**

```sql
FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE RESTRICT
-- Không cho xóa author nếu có posts
```

**Option 2: Soft delete cho users**

```sql
-- Users dùng soft delete
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  deleted_at TIMESTAMP
);

-- Posts không cần SET NULL
FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE RESTRICT

-- Query chỉ lấy users chưa xóa
SELECT * FROM users WHERE deleted_at IS NULL;
```

**Recommendation:** Dùng RESTRICT hoặc soft delete thay vì SET NULL nếu business rule yêu cầu "phải có author".

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Foreign Key vs Application Validation

**a) So sánh:**

| Tiêu chí | Foreign Key | Application Validation |
|----------|-------------|----------------------|
| **Data integrity** | ✅ Database enforce | ❌ Phải tự code |
| **Performance** | ❌ Có overhead (check constraint) | ✅ Không có overhead |
| **Complexity** | ✅ Database tự enforce | ❌ Phải code logic |
| **Reliability** | ✅ Luôn enforce | ❌ Có thể quên check |

**b) Chọn cách nào?**

**Đáp án: Foreign Key (Option A)**

**Lý do:**
- **Database enforce tốt hơn**: Không thể bypass (application code có thể có bug)
- **Self-documenting**: Schema tự giải thích relationships
- **Consistency**: Mọi application đều tuân thủ (không chỉ một app)
- **Performance overhead không đáng kể**: Với modern databases, Foreign Key check rất nhanh

**c) Có thể dùng cả 2 không?**

**Đáp án: CÓ - Defense in depth**

```sql
-- Database level
FOREIGN KEY (user_id) REFERENCES users(id)
```

```python
# Application level (validation)
def create_order(user_id, total_amount):
    # Check user exists (defense in depth)
    if not user_exists(user_id):
        raise ValueError(f"User {user_id} does not exist")
    
    # Insert (database sẽ check lại)
    db.execute("INSERT INTO orders (user_id, total_amount) VALUES (%s, %s)", 
               [user_id, total_amount])
```

**Ưu điểm:**
- **Early validation**: Fail fast trong application (không cần đến database)
- **Better error messages**: Application có thể trả về error message rõ ràng hơn
- **Defense in depth**: Nếu application có bug, database vẫn enforce

**Trade-off:**
- Code phức tạp hơn (phải check 2 lần)
- Nhưng an toàn hơn

---

### Câu 4.2: Foreign Key và Performance

**a) Foreign Key có ảnh hưởng đến performance không?**

**Đáp án: CÓ, nhưng thường không đáng kể**

**Overhead:**
- **INSERT/UPDATE**: Phải check Foreign Key value có tồn tại không
- **DELETE**: Phải check có Foreign Keys reference đến không (nếu RESTRICT)

**Với modern databases:**
- Foreign Key check rất nhanh (có index)
- Overhead thường < 1ms per operation
- Không đáng kể với hầu hết applications

**b) Khi nào Foreign Key làm chậm queries?**

**Khi:**
- ❌ **High-frequency inserts**: Hàng triệu inserts/giây → overhead tích lũy
- ❌ **Complex cascades**: CASCADE sâu (xóa 1 record → xóa hàng nghìn records)
- ❌ **No index**: Foreign Key column không có index → check chậm

**c) Làm thế nào optimize performance?**

1. **Đảm bảo có index trên Foreign Key column:**
   ```sql
   CREATE INDEX idx_orders_user_id ON orders(user_id);
   ```

2. **Tránh CASCADE sâu**: Nếu có thể, dùng RESTRICT thay vì CASCADE

3. **Batch operations**: Nếu có nhiều inserts, dùng batch thay vì từng cái một

**d) Có nên disable Foreign Key trong production không?**

**Đáp án: KHÔNG**

**Lý do:**
- ❌ **Mất data integrity**: Có thể có orphan records
- ❌ **Không đáng kể performance gain**: Overhead rất nhỏ
- ❌ **Rủi ro cao**: Dễ mất dữ liệu, khó debug

**Nếu thực sự cần performance:**
- ✅ Optimize queries, indexes
- ✅ Consider read replicas
- ✅ Consider caching
- ❌ KHÔNG disable Foreign Key

---

### Câu 4.3: Foreign Key và Data Warehouse

**a) Có nên dùng Foreign Key trong data warehouse không?**

**Đáp án: KHÔNG (thường)**

**Lý do:**
- ❌ **Performance**: Data warehouse có nhiều inserts → Foreign Key check chậm
- ❌ **Data đã clean**: Data đã được validate trước khi load
- ❌ **Read-only**: Data warehouse chủ yếu read, không cần real-time integrity
- ❌ **Flexibility**: Có thể cần load data không hoàn toàn consistent

**b) Nếu không dùng Foreign Key, làm thế nào đảm bảo data integrity?**

**Options:**

1. **ETL validation**: Validate trong ETL process trước khi load
2. **Application validation**: Validate trong application code
3. **Periodic checks**: Chạy queries định kỳ để tìm orphan records
4. **Accept inconsistency**: Chấp nhận một số inconsistency (analytics không cần 100% accurate)

**c) Trade-offs:**

| Tiêu chí | Có Foreign Key | Không có Foreign Key |
|----------|----------------|---------------------|
| **Data integrity** | ✅ Đảm bảo | ❌ Phải tự validate |
| **Performance** | ❌ Có overhead | ✅ Nhanh hơn |
| **Flexibility** | ❌ Khó load inconsistent data | ✅ Linh hoạt |
| **Complexity** | ✅ Database tự enforce | ❌ Phải code logic |

**Kết luận:** Data warehouse thường KHÔNG dùng Foreign Key vì performance và flexibility quan trọng hơn real-time integrity.

---

### Câu 4.4: Multiple Foreign Keys

**a) Tại sao có thể có ON DELETE actions khác nhau?**

**Lý do:**
- Mỗi Foreign Key có business logic riêng
- `order_id` → orders: Order items không có giá trị nếu không có order → CASCADE
- `product_id` → products: Products quan trọng, cần giữ lại → RESTRICT

**b) Nếu xóa order:**

```sql
DELETE FROM orders WHERE id = 1;
```

**Kết quả:**
- `order_items` có `order_id = 1` → tự động xóa (CASCADE)
- `order_items` có `product_id` không bị ảnh hưởng (RESTRICT chỉ áp dụng khi xóa product)

**c) Nếu xóa product:**

```sql
DELETE FROM products WHERE id = 1;
```

**Kết quả:**
- ❌ **ERROR**: Có `order_items` reference đến `product_id = 1` → không cho xóa (RESTRICT)
- Phải xóa `order_items` trước, hoặc dùng CASCADE

**d) Có conflict không?**

**Đáp án: KHÔNG**

Mỗi Foreign Key có action riêng, không conflict với nhau. Chỉ conflict nếu:
- Xóa order → CASCADE xóa order_items (OK)
- Xóa product → RESTRICT không cho xóa (OK)
- Không có conflict vì chúng là independent operations

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Tables với Foreign Keys

**a) `users` và `orders` (RESTRICT):**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);
```

**b) `categories` và `products` (SET NULL):**

```sql
CREATE TABLE categories (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE products (
  id INT PRIMARY KEY,
  category_id INT,  -- Có thể NULL
  name VARCHAR(200),
  FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL
);
```

**c) `posts` và `comments` (CASCADE):**

```sql
CREATE TABLE posts (
  id INT PRIMARY KEY,
  title VARCHAR(300),
  content TEXT
);

CREATE TABLE comments (
  id INT PRIMARY KEY,
  post_id INT NOT NULL,
  content TEXT,
  FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE
);
```

---

### Câu 5.2: Xử lý Orphan Records

**a) Xóa orphan orders:**

```sql
DELETE o
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;
```

**b) Assign orphan orders đến default user:**

```sql
-- Tạo default user nếu chưa có
INSERT INTO users (id, name, email)
VALUES (0, 'Deleted User', 'deleted@example.com')
ON DUPLICATE KEY UPDATE name = name;

-- Assign orphan orders
UPDATE orders o
LEFT JOIN users u ON o.user_id = u.id
SET o.user_id = 0
WHERE u.id IS NULL;
```

**c) Tạo default user và assign:**

```sql
-- Tạo default user nếu chưa có
INSERT INTO users (id, name, email)
SELECT 0, 'Deleted User', 'deleted@example.com'
WHERE NOT EXISTS (SELECT 1 FROM users WHERE id = 0);

-- Assign orphan orders
UPDATE orders o
LEFT JOIN users u ON o.user_id = u.id
SET o.user_id = 0
WHERE u.id IS NULL;
```

---

### Câu 5.3: Migrate từ không có Foreign Key sang có Foreign Key

**a) Các bước:**

1. **Tìm orphan records**: Query để tìm records vi phạm Referential Integrity
2. **Fix orphan records**: Xóa hoặc assign lại
3. **Thêm Foreign Key constraint**: ALTER TABLE ADD CONSTRAINT
4. **Verify**: Test insert/update/delete để đảm bảo constraint hoạt động

**b) Migration script (pseudo-code):**

```python
def migrate_add_foreign_key():
    # Bước 1: Tìm orphan records
    orphan_orders = db.execute("""
        SELECT o.id, o.user_id
        FROM orders o
        LEFT JOIN users u ON o.user_id = u.id
        WHERE u.id IS NULL
    """)
    
    if orphan_orders:
        print(f"Found {len(orphan_orders)} orphan orders")
        
        # Bước 2: Fix orphan records
        # Option A: Xóa
        db.execute("""
            DELETE o
            FROM orders o
            LEFT JOIN users u ON o.user_id = u.id
            WHERE u.id IS NULL
        """)
        
        # Option B: Assign đến default user
        # db.execute("""
        #     INSERT INTO users (id, name) VALUES (0, 'Deleted User')
        #     ON DUPLICATE KEY UPDATE name = name
        # """)
        # db.execute("""
        #     UPDATE orders o
        #     LEFT JOIN users u ON o.user_id = u.id
        #     SET o.user_id = 0
        #     WHERE u.id IS NULL
        # """)
    
    # Bước 3: Thêm Foreign Key constraint
    try:
        db.execute("""
        ALTER TABLE orders
        ADD CONSTRAINT fk_orders_user_id
        FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
        """)
        print("Foreign Key constraint added successfully")
    except Exception as e:
        print(f"Error adding Foreign Key: {e}")
        return
    
    # Bước 4: Verify
    # Test insert invalid data
    try:
        db.execute("INSERT INTO orders (user_id, total_amount) VALUES (999, 100.00)")
        print("ERROR: Should not allow invalid user_id")
    except:
        print("✅ Foreign Key constraint working: Rejected invalid user_id")
    
    # Test delete user with orders
    try:
        db.execute("DELETE FROM users WHERE id = 1")
        print("ERROR: Should not allow delete user with orders")
    except:
        print("✅ Foreign Key constraint working: Rejected delete user with orders")
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Foreign Key là gì?**
   - Column reference đến Primary Key của table khác
   - Tạo mối quan hệ và đảm bảo Referential Integrity

2. **Referential Integrity:**
   - Đảm bảo mọi Foreign Key value đều tồn tại
   - Không có orphan records

3. **ON DELETE actions:**
   - CASCADE: Xóa child records
   - RESTRICT: Không cho xóa
   - SET NULL: Set Foreign Key = NULL

4. **Khi nào dùng Foreign Key:**
   - OLTP systems, business-critical data
   - KHÔNG dùng trong data warehouse

5. **Orphan records:**
   - Records có Foreign Key value không tồn tại
   - Tránh bằng Foreign Key constraint

---

### Câu 6.2: Hệ thống quản lý dự án

**a) Foreign Keys:**

```sql
-- Projects
CREATE TABLE projects (
  id INT PRIMARY KEY,
  name VARCHAR(200)
);

-- Users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

-- Project Members
CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role VARCHAR(50),
  PRIMARY KEY (project_id, user_id),
  FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);

-- Tasks
CREATE TABLE tasks (
  id INT PRIMARY KEY,
  project_id INT NOT NULL,
  title VARCHAR(200),
  FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE
);

-- Task Assignments
CREATE TABLE task_assignments (
  task_id INT,
  user_id INT,
  PRIMARY KEY (task_id, user_id),
  FOREIGN KEY (task_id) REFERENCES tasks(id) ON DELETE CASCADE,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);
```

**b) Giải thích ON DELETE actions:**

- **`project_members.project_id` → `projects.id` (CASCADE)**: Xóa project → xóa members
- **`project_members.user_id` → `users.id` (RESTRICT)**: Không cho xóa user nếu có project members
- **`tasks.project_id` → `projects.id` (CASCADE)**: Xóa project → xóa tasks
- **`task_assignments.task_id` → `tasks.id` (CASCADE)**: Xóa task → xóa assignments
- **`task_assignments.user_id` → `users.id` (RESTRICT)**: Không cho xóa user nếu có task assignments

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: Self-referencing Foreign Key

**a) Self-referencing Foreign Key:**

Foreign Key reference đến chính table đó.

**Ví dụ:**

```sql
-- Employees (manager là employee khác)
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  manager_id INT,
  FOREIGN KEY (manager_id) REFERENCES employees(id)
);
```

**b) Khi nào dùng:**

- Hierarchical data (tree structure)
- Employees → Managers
- Categories → Parent categories
- Comments → Parent comments

**c) Vấn đề:**

**Circular reference:**

```sql
-- Employee A có manager = B
-- Employee B có manager = A
-- ❌ Circular reference!
```

**Giải pháp:**
- Database thường không cho phép circular reference (có thể check)
- Hoặc dùng application logic để tránh

---

### Câu A.2: Foreign Key và Soft Delete

**a) Foreign Key có hoạt động với soft delete không?**

**Đáp án: CÓ, nhưng có vấn đề**

```sql
-- Users dùng soft delete
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  deleted_at TIMESTAMP
);

-- Orders
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Vấn đề:**
- Foreign Key chỉ check `id` tồn tại, không check `deleted_at`
- Có thể có orders với `user_id` của user đã bị soft delete

**b) Làm thế nào đảm bảo Referential Integrity với soft delete?**

**Option 1: Check trong application**

```python
def create_order(user_id, total_amount):
    user = db.execute("SELECT id FROM users WHERE id = %s AND deleted_at IS NULL", [user_id])
    if not user:
        raise ValueError("User does not exist or is deleted")
    # Insert order
```

**Option 2: View với Foreign Key**

```sql
-- View chỉ lấy users chưa xóa
CREATE VIEW active_users AS
SELECT * FROM users WHERE deleted_at IS NULL;

-- Foreign Key reference đến view (nếu database hỗ trợ)
-- Hoặc dùng application logic
```

**c) Có cần Foreign Key constraint không?**

**Đáp án: CÓ**

Foreign Key vẫn cần để:
- Đảm bảo `user_id` tồn tại (không phải random number)
- Ngăn chặn invalid IDs
- Application logic check `deleted_at` riêng

---

### Câu A.3: Cross-database Foreign Key

**a) Foreign Key có thể reference đến table trong database khác không?**

**Đáp án: KHÔNG (hầu hết databases)**

Foreign Key chỉ hoạt động trong cùng database.

**Một số databases hỗ trợ:**
- SQL Server: Có thể (với linked servers, nhưng phức tạp)
- PostgreSQL: Không hỗ trợ
- MySQL: Không hỗ trợ

**b) Nếu không thể, làm thế nào xử lý relationships giữa databases?**

**Options:**

1. **Application logic**: Check trong application code
2. **Replication**: Replicate data vào cùng database
3. **API calls**: Check qua API
4. **Accept inconsistency**: Chấp nhận một số inconsistency

**c) Trade-offs:**

| Tiêu chí | Có Foreign Key | Không có Foreign Key |
|----------|----------------|---------------------|
| **Data integrity** | ✅ Đảm bảo | ❌ Phải tự check |
| **Performance** | ✅ Nhanh (cùng DB) | ❌ Chậm (cross-DB) |
| **Complexity** | ✅ Database tự enforce | ❌ Phải code logic |
| **Flexibility** | ❌ Phải cùng DB | ✅ Linh hoạt |

**Kết luận:** Cross-database relationships thường không dùng Foreign Key, dùng application logic thay thế.

---

## 📝 TÓM TẮT

### Key Learnings

1. **Foreign Key** tạo mối quan hệ và đảm bảo Referential Integrity
2. **Referential Integrity** đảm bảo không có orphan records
3. **ON DELETE CASCADE/RESTRICT/SET NULL** - chọn đúng cho từng use case
4. **Nên dùng Foreign Key** trong OLTP, **KHÔNG nên** trong data warehouse
5. **Database constraints > Application validation** - database enforce tốt hơn

### Best Practices

✅ **Luôn dùng Foreign Key** cho relationships trong OLTP systems
✅ **ON DELETE RESTRICT** là default (an toàn nhất)
✅ **Đặt tên constraint rõ ràng**: `fk_orders_user_id`
✅ **Verify indexes** trên Foreign Key columns
✅ **Clean up orphan records** trước khi thêm Foreign Key

---

**Chúc mừng hoàn thành Day-004!** 🎉

**Chuẩn bị cho Day-005: Normalization - Chuẩn hóa dữ liệu (1NF)** 🚀

