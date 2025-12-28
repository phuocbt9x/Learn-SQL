# Day-003: Solutions - Primary Key

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Primary Key là gì?

**Đáp án:**

**Primary Key là gì?**

Primary Key (Khóa chính) là một hoặc nhiều columns trong table dùng để định danh duy nhất mỗi row.

**Tại sao cần Primary Key?**

1. **Xác định row duy nhất**: Làm sao biết "user John" là user nào nếu có nhiều users tên John?
2. **Reference từ bảng khác**: Foreign Key cần Primary Key để reference
3. **Update/Delete chính xác**: Biết chính xác row nào cần thao tác
4. **Index tự động**: Database tự động tạo index → query nhanh

**4 đặc điểm chính:**

1. **UNIQUE**: Không có 2 rows nào có cùng Primary Key value
2. **NOT NULL**: Primary Key không thể là NULL
3. **IMMUTABLE**: Giá trị Primary Key không nên thay đổi (best practice)
4. **INDEXED**: Database tự động tạo index trên Primary Key

---

### Câu 1.2: Single Key vs Composite Key

**a) Table `users` với `id`**

**Đáp án: Single Key**

**Lý do:**
- Có ID column riêng → dùng làm Primary Key
- Đơn giản, dễ hiểu
- Foreign Key dễ reference (chỉ cần 1 column)

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,  -- Single Key
  name VARCHAR(100)
);
```

---

**b) Table `order_items`**

**Đáp án: Composite Key HOẶC Single Key + UNIQUE**

**Lý do:**
- Junction table (many-to-many relationship)
- Một order có nhiều products, một product có trong nhiều orders
- Combination (order_id, product_id) đảm bảo unique

**Option A: Composite Key**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)  -- Composite Key
);
```

**Option B: Single Key + UNIQUE**
```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,  -- Single Key
  order_id INT,
  product_id INT,
  quantity INT,
  UNIQUE (order_id, product_id)  -- Đảm bảo unique
);
```

**Recommendation:** Composite Key phù hợp hơn cho junction tables (không cần ID riêng).

---

**c) Table `enrollments`**

**Đáp án: Composite Key**

**Lý do:**
- Một student chỉ enroll một course một lần mỗi semester
- Combination (student_id, course_id, semester) đảm bảo unique
- Không cần ID riêng

```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  semester VARCHAR(20),
  enrolled_at TIMESTAMP,
  PRIMARY KEY (student_id, course_id, semester)  -- Composite Key
);
```

---

**d) Table `products` với UUID**

**Đáp án: Single Key**

**Lý do:**
- Có ID column riêng (UUID) → dùng làm Primary Key
- UUID đảm bảo unique
- Đơn giản, dễ reference

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY,  -- Single Key
  name VARCHAR(200)
);
```

---

### Câu 1.3: Auto-increment vs UUID vs Natural Key

**a) Single database, table `users`, cần performance**

**Đáp án: Auto-increment INT**

**Lý do:**
- Single database → không cần UUID
- Performance quan trọng → INT nhanh hơn UUID
- Auto-increment đơn giản, hiệu quả

```sql
id INT AUTO_INCREMENT PRIMARY KEY
```

---

**b) Distributed system, table `events`, cần security**

**Đáp án: UUID**

**Lý do:**
- Distributed system → nhiều servers → cần UUID để tránh conflict
- Security quan trọng → UUID không thể đoán được (không sequential)
- Events không cần sequential access

```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
```

---

**c) Table `citizens` với SSN**

**Đáp án: Natural Key (SSN) HOẶC Surrogate Key + UNIQUE**

**Lý do:**
- SSN đảm bảo unique và không bao giờ đổi
- Có thể dùng SSN làm Primary Key

**Option A: Natural Key**
```sql
CREATE TABLE citizens (
  ssn VARCHAR(20) PRIMARY KEY,  -- Natural Key
  name VARCHAR(100)
);
```

**Option B: Surrogate Key + UNIQUE (TỐT HƠN)**
```sql
CREATE TABLE citizens (
  id INT PRIMARY KEY,           -- Surrogate Key
  ssn VARCHAR(20) UNIQUE,        -- Natural Key với UNIQUE
  name VARCHAR(100)
);
```

**Recommendation:** Option B tốt hơn vì:
- ID không đổi (SSN có thể đổi trong edge cases)
- Foreign Key reference ID (ngắn hơn, nhanh hơn)
- SSN vẫn unique (UNIQUE constraint)

---

**d) Table `orders` trong single database, cần query newest first**

**Đáp án: Auto-increment INT**

**Lý do:**
- Single database → không cần UUID
- Query newest first → Auto-increment INT sequential → dễ sort
- Performance tốt

```sql
id INT AUTO_INCREMENT PRIMARY KEY
-- Query: ORDER BY id DESC (newest first)
```

---

**e) Microservices architecture**

**Đáp án: UUID**

**Lý do:**
- Mỗi service tự generate ID → cần UUID để tránh conflict
- Distributed → UUID phù hợp
- Không cần sequential

```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
```

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Table không có Primary Key

**a) Phân tích vấn đề:**

1. **Không thể xác định row duy nhất**: Làm sao biết product nào là product nào?
2. **Không thể reference**: Bảng khác không thể có Foreign Key đến `products`
3. **Không có index tự động**: Query chậm
4. **Khó update/delete**: Không biết chính xác row nào cần thao tác

**b) CREATE TABLE đúng:**

```sql
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Primary Key
  name VARCHAR(200) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  category VARCHAR(100)
);
```

**c) Giải thích:**

- **`id INT AUTO_INCREMENT`**: ID tự động tăng, đơn giản, hiệu quả
- **Single database**: Auto-increment phù hợp
- **Performance**: INT index nhanh
- **Reference**: Dễ dàng cho Foreign Keys

---

### Câu 2.2: Chọn sai Primary Key

**a) Phân tích vấn đề:**

1. **Email có thể thay đổi**: User đổi email → phải update Primary Key → phức tạp
2. **Foreign Key phải update**: Nếu có Foreign Keys reference đến email → phải update tất cả
3. **Storage lớn hơn**: VARCHAR(100) tốn nhiều hơn INT (4 bytes)
4. **Index chậm hơn**: String index chậm hơn integer index
5. **Không phải lúc nào cũng unique**: Nếu không enforce đúng → có thể trùng

**b) CREATE TABLE tốt hơn:**

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Surrogate Key
  email VARCHAR(100) UNIQUE NOT NULL,  -- Natural Key với UNIQUE
  name VARCHAR(100),
  phone VARCHAR(20)
);
```

**c) Nếu vẫn muốn email unique:**

Dùng **UNIQUE constraint** thay vì Primary Key:

```sql
email VARCHAR(100) UNIQUE NOT NULL
```

**Ưu điểm:**
- Email vẫn unique (UNIQUE constraint)
- ID không đổi → dễ update email
- Foreign Key reference ID (ngắn, nhanh)
- Email có thể đổi mà không ảnh hưởng Primary Key

---

### Câu 2.3: Composite Key vs Single Key

**a) So sánh:**

| Tiêu chí | Composite Key | Single Key |
|----------|---------------|------------|
| **Độ phức tạp** | Phức tạp hơn | Đơn giản |
| **Foreign Key reference** | Phải reference 2 columns | Chỉ cần 1 column |
| **Storage** | Tốn hơn (2 INTs) | Tiết kiệm hơn (1 INT) |
| **Performance** | Index trên 2 columns (có thể chậm hơn) | Index trên 1 column (nhanh) |

**b) Chọn cách nào?**

**Đáp án: Tùy vào use case**

**Dùng Composite Key khi:**
- ✅ Junction table (many-to-many)
- ✅ Không cần ID riêng
- ✅ Combination là natural unique

**Dùng Single Key khi:**
- ✅ Cần ID riêng (ví dụ: để reference từ bảng khác)
- ✅ Có thể có thêm columns (ví dụ: `added_at`, `notes`)
- ✅ Đơn giản hơn

**Recommendation:** 
- Junction table đơn giản → Composite Key
- Junction table có thêm columns/queries phức tạp → Single Key

**c) Tình huống:**

**Option A (Composite Key) phù hợp khi:**
- Junction table đơn giản
- Chỉ cần lưu relationship
- Không cần reference từ bảng khác

**Option B (Single Key) phù hợp khi:**
- Cần reference từ bảng khác (ví dụ: `task_assignments` có `comments` table reference đến)
- Có thêm columns (ví dụ: `assigned_at`, `notes`)
- Queries phức tạp (cần JOIN với nhiều bảng)

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce

**a) Primary Key cho mỗi table:**

```sql
-- Users
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Auto-increment
  email VARCHAR(100) UNIQUE,
  name VARCHAR(100)
);

-- Products
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Auto-increment
  name VARCHAR(200),
  price DECIMAL(10, 2)
);

-- Orders
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Auto-increment
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP
);

-- Order Items
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)  -- Composite Key
);
```

**b) Giải thích:**

- **`users.id`**: Auto-increment INT - đơn giản, hiệu quả cho single database
- **`products.id`**: Auto-increment INT - tương tự users
- **`orders.id`**: Auto-increment INT - tương tự
- **`order_items`**: Composite Key - junction table, combination (order_id, product_id) đảm bảo unique

**c) Nếu distributed:**

**Thay đổi:**

```sql
-- Users
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Products
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Orders
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Order Items (không đổi)
PRIMARY KEY (order_id, product_id)  -- Vẫn Composite Key
```

**Lý do:**
- Distributed system → cần UUID để tránh conflict
- `order_items` vẫn dùng Composite Key (order_id và product_id là UUIDs)

---

### Câu 3.2: Blog System

**a) Primary Key cho mỗi table:**

```sql
-- Posts
CREATE TABLE posts (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Single Key
  title VARCHAR(300),
  content TEXT,
  created_at TIMESTAMP
);

-- Tags
CREATE TABLE tags (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Single Key
  name VARCHAR(50) UNIQUE
);

-- Post Tags
CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  added_at TIMESTAMP,
  PRIMARY KEY (post_id, tag_id)  -- Composite Key
);
```

**b) Table `post_tags` nên dùng gì?**

**Đáp án: Composite Key**

**Lý do:**
- Junction table (many-to-many)
- Combination (post_id, tag_id) đảm bảo unique
- Không cần ID riêng

**c) Nếu có thêm `added_at`:**

**KHÔNG ảnh hưởng đến Primary Key**

```sql
CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  added_at TIMESTAMP,  -- Thêm column này
  PRIMARY KEY (post_id, tag_id)  -- Vẫn Composite Key
);
```

**Lý do:**
- `added_at` chỉ là metadata, không ảnh hưởng uniqueness
- Primary Key vẫn là (post_id, tag_id) - một post chỉ có một tag một lần
- `added_at` chỉ lưu thời gian thêm tag (có thể update nếu cần)

**Lưu ý:** Nếu muốn lưu nhiều lần thêm tag (một post có thể thêm tag nhiều lần), cần thêm vào Primary Key:

```sql
-- Nếu muốn lưu nhiều lần thêm tag
PRIMARY KEY (post_id, tag_id, added_at)  -- Composite Key với 3 columns
-- Hoặc
id INT PRIMARY KEY,  -- Single Key
UNIQUE (post_id, tag_id, added_at)
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Auto-increment vs UUID - Trade-offs

**a) Phân tích trade-offs:**

| Tiêu chí | Auto-increment | UUID |
|----------|----------------|------|
| **Storage** | Nhỏ (4-8 bytes) | Lớn (16 bytes) |
| **Performance** | Nhanh nhất (integer index) | Chậm hơn (string index) |
| **Security** | Kém (dễ đoán) | Tốt (không thể đoán) |
| **Scalability** | Single DB | Distributed |
| **Distributed** | Không phù hợp | Phù hợp |

**b) Chọn option nào?**

- **Single database, performance quan trọng**: Auto-increment INT
- **Distributed system, nhiều servers**: UUID
- **Cần security (không muốn expose sequential IDs)**: UUID
- **Cần merge data từ nhiều databases**: UUID

**c) Có thể dùng cả 2 không?**

**Đáp án: CÓ**

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Internal ID (performance)
  uuid UUID UNIQUE DEFAULT gen_random_uuid(),  -- External ID (security)
  email VARCHAR(100),
  name VARCHAR(100)
);
```

**Ưu điểm:**
- `id`: Dùng cho internal queries (nhanh)
- `uuid`: Dùng cho external API (security, không expose sequential ID)

**Trade-off:**
- Tốn storage hơn (4 + 16 = 20 bytes)
- Phức tạp hơn (phải quyết định dùng ID nào)

**Recommendation:** Thường chỉ cần một trong hai, trừ khi có yêu cầu cụ thể.

---

### Câu 4.2: Natural Key vs Surrogate Key

**a) So sánh:**

| Tiêu chí | Natural Key | Surrogate Key |
|----------|-------------|---------------|
| **Storage** | Tùy (VARCHAR) | Nhỏ (INT) |
| **Foreign Key reference** | Dài (country_code) | Ngắn (id) |
| **Performance** | Chậm hơn (string index) | Nhanh hơn (integer index) |
| **Flexibility** | Khó đổi (Primary Key) | Dễ đổi (chỉ UNIQUE) |

**b) Chọn cách nào?**

**Đáp án: Surrogate Key (Option B)**

**Lý do:**
- `country_code` có thể đổi (edge cases: country merge, split)
- Foreign Key reference ID ngắn hơn, nhanh hơn
- `country_code` vẫn unique (UNIQUE constraint)
- Linh hoạt hơn (có thể đổi country_code mà không ảnh hưởng Primary Key)

**c) Tình huống:**

**Dùng Natural Key khi:**
- ✅ Chắc chắn không bao giờ đổi (ví dụ: SSN - nhưng vẫn nên dùng Surrogate Key)
- ✅ Table đơn giản, ít relationships
- ✅ Không cần performance cao

**Dùng Surrogate Key khi:**
- ✅ Hầu hết các trường hợp (recommended)
- ✅ Có thể đổi natural key
- ✅ Cần performance (Foreign Key reference)
- ✅ Nhiều relationships

**Best practice:** Luôn dùng Surrogate Key, dùng UNIQUE constraint cho natural key.

---

### Câu 4.3: Primary Key và Performance

**a) Tại sao Primary Key tự động có index?**

**Giải thích:**

Database tự động tạo index trên Primary Key để:
- Đảm bảo UNIQUE constraint (phải check nhanh)
- Tối ưu queries (WHERE id = 1 nhanh)
- Tối ưu JOINs (JOIN trên Primary Key nhanh)

**b) Index trên Primary Key là gì?**

**Clustered Index (một số databases):**

- Rows được sắp xếp theo Primary Key
- Index và data cùng một structure
- Query sequential rất nhanh (1, 2, 3, ...)

**Non-clustered Index (một số databases):**

- Index tách biệt với data
- Index chỉ chứa pointer đến data
- Vẫn nhanh, nhưng không nhanh bằng clustered

**c) Query `WHERE id = 1` vs `WHERE name = 'John'`:**

**`WHERE id = 1` (Primary Key):**
- Có index tự động → O(log n) - rất nhanh
- Index là integer → so sánh nhanh

**`WHERE name = 'John'` (không có index):**
- Không có index → Full Table Scan → O(n) - chậm
- Phải scan tất cả rows

**Ví dụ:**
- 1 triệu rows
- `WHERE id = 1`: ~10 comparisons (log2(1M) ≈ 20) → vài milliseconds
- `WHERE name = 'John'`: 1 triệu comparisons → vài giây

**d) UUID (string) vs INT:**

**Performance:**

- **INT**: Integer comparison → rất nhanh
- **UUID**: String comparison → chậm hơn (phải so sánh từng ký tự)

**Ví dụ:**
- `WHERE id = 1` (INT): 1 comparison
- `WHERE id = '550e8400-...'` (UUID): 36 comparisons (từng ký tự)

**Trade-off:**
- UUID chậm hơn INT, nhưng vẫn acceptable (có index)
- Với modern databases, performance difference không đáng kể (< 10%)

---

### Câu 4.4: Primary Key và Foreign Key

**a) Làm thế nào để `orders` reference đến `users`?**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id)  -- Reference đến Primary Key
);
```

**b) Nếu `users.id` thay đổi:**

**Vấn đề:**

```sql
-- ❌ SAI: Update Primary Key
UPDATE users SET id = 999 WHERE id = 1;
-- Nếu có Foreign Keys reference đến id=1 → ERROR hoặc phải CASCADE
```

**Hậu quả:**
- Foreign Keys bị broken (reference đến id không tồn tại)
- Phải update tất cả Foreign Keys
- Phức tạp, dễ lỗi

**c) Tại sao Primary Key không nên thay đổi?**

1. **Foreign Keys**: Nhiều bảng reference đến → phải update tất cả
2. **Index**: Update Primary Key → phải rebuild index
3. **Performance**: Update Primary Key chậm
4. **Complexity**: Phức tạp, dễ lỗi

**Best practice:** Primary Key = Immutable (không bao giờ update).

**d) Nếu bắt buộc phải thay đổi:**

**Migration strategy:**

1. **Tạo column mới:**
   ```sql
   ALTER TABLE users ADD COLUMN new_id INT;
   ```

2. **Generate new IDs:**
   ```sql
   UPDATE users SET new_id = ...;
   ```

3. **Update Foreign Keys:**
   ```sql
   UPDATE orders SET user_id = (SELECT new_id FROM users WHERE users.id = orders.user_id);
   ```

4. **Drop old Primary Key, add new:**
   ```sql
   ALTER TABLE users DROP PRIMARY KEY;
   ALTER TABLE users ADD PRIMARY KEY (new_id);
   ```

5. **Drop old column:**
   ```sql
   ALTER TABLE users DROP COLUMN id;
   ALTER TABLE users RENAME COLUMN new_id TO id;
   ```

**Lưu ý:** Rất phức tạp, tốn thời gian, có rủi ro. **Tránh nếu có thể!**

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Tables với Primary Key

**a) `users` với auto-increment:**

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**b) `products` với UUID:**

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(200),
  price DECIMAL(10, 2)
);
```

**c) `order_items` với composite key:**

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**d) `user_roles` với composite key:**

```sql
CREATE TABLE user_roles (
  user_id INT,
  role_id INT,
  assigned_at TIMESTAMP,
  PRIMARY KEY (user_id, role_id)
);
```

---

### Câu 5.2: Xử lý Duplicate Key

**a) Đảm bảo `order_number` unique:**

```sql
-- Thêm UNIQUE constraint
ALTER TABLE orders
ADD CONSTRAINT unique_order_number UNIQUE (order_number);
```

**b) Tránh duplicate trong concurrent requests:**

**Option 1: Database sequence (PostgreSQL)**

```sql
CREATE SEQUENCE order_number_seq START 1;

CREATE OR REPLACE FUNCTION generate_order_number()
RETURNS VARCHAR(20) AS $$
DECLARE
    new_num INT;
BEGIN
    new_num := nextval('order_number_seq');
    RETURN 'ORD-2024-' || LPAD(new_num::TEXT, 3, '0');
END;
$$ LANGUAGE plpgsql;

-- Insert
INSERT INTO orders (order_number, total_amount)
VALUES (generate_order_number(), 100.00);
```

**Option 2: Application-level với lock**

```python
@transaction.atomic
def create_order(total_amount):
    with connection.cursor() as cursor:
        # Lock để đảm bảo atomic
        cursor.execute("SELECT order_number FROM orders ORDER BY id DESC LIMIT 1 FOR UPDATE")
        last_order = cursor.fetchone()
        
        if last_order:
            last_num = int(last_order[0].split('-')[-1])
            new_num = last_num + 1
        else:
            new_num = 1
        
        order_number = f"ORD-2024-{new_num:03d}"
        
        cursor.execute(
            "INSERT INTO orders (order_number, total_amount) VALUES (%s, %s)",
            [order_number, total_amount]
        )
```

**c) Code thread-safe (pseudo-code):**

```python
def generate_order_number_thread_safe():
    # Option 1: Database sequence (best)
    return db.execute("SELECT nextval('order_number_seq')")
    
    # Option 2: Lock-based
    with transaction.atomic():
        last_order = Order.objects.select_for_update().order_by('-id').first()
        if last_order:
            new_num = extract_number(last_order.order_number) + 1
        else:
            new_num = 1
        return f"ORD-2024-{new_num:03d}"
```

---

### Câu 5.3: Migrate từ Natural Key sang Surrogate Key

**a) Migration strategy:**

```sql
-- Bước 1: Thêm column mới
ALTER TABLE users ADD COLUMN new_id INT;

-- Bước 2: Generate IDs
SET @counter = 0;
UPDATE users SET new_id = (@counter := @counter + 1);

-- Bước 3: Update Foreign Keys (nếu có)
-- Ví dụ: orders.user_email → orders.user_id
UPDATE orders o
JOIN users u ON o.user_email = u.email
SET o.user_id = u.new_id;

-- Bước 4: Drop Foreign Keys cũ (nếu có)
ALTER TABLE orders DROP FOREIGN KEY fk_user_email;

-- Bước 5: Drop Primary Key cũ, add Primary Key mới
ALTER TABLE users DROP PRIMARY KEY;
ALTER TABLE users ADD PRIMARY KEY (new_id);

-- Bước 6: Add UNIQUE constraint cho email
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email);

-- Bước 7: Rename column
ALTER TABLE users DROP COLUMN email;  -- Không, giữ lại
-- Hoặc rename
ALTER TABLE users CHANGE COLUMN new_id id INT;

-- Bước 8: Add Foreign Keys mới
ALTER TABLE orders ADD FOREIGN KEY (user_id) REFERENCES users(id);
```

**b) Nếu có Foreign Keys:**

1. **Tìm tất cả Foreign Keys:**
   ```sql
   SELECT * FROM information_schema.KEY_COLUMN_USAGE
   WHERE REFERENCED_TABLE_NAME = 'users'
     AND REFERENCED_COLUMN_NAME = 'email';
   ```

2. **Update Foreign Keys trước:**
   - Update tất cả Foreign Keys từ `email` sang `id`
   - Drop Foreign Keys cũ
   - Add Foreign Keys mới

**c) Migration script (pseudo-code):**

```python
def migrate_users_table():
    # 1. Add new_id column
    db.execute("ALTER TABLE users ADD COLUMN new_id INT")
    
    # 2. Generate IDs
    db.execute("SET @counter = 0")
    db.execute("UPDATE users SET new_id = (@counter := @counter + 1)")
    
    # 3. Update Foreign Keys
    for table in get_tables_with_fk_to_users_email():
        db.execute(f"""
            UPDATE {table} t
            JOIN users u ON t.user_email = u.email
            SET t.user_id = u.new_id
        """)
    
    # 4. Drop old Foreign Keys
    for fk in get_foreign_keys_to_users_email():
        db.execute(f"ALTER TABLE {fk.table} DROP FOREIGN KEY {fk.name}")
    
    # 5. Drop old Primary Key, add new
    db.execute("ALTER TABLE users DROP PRIMARY KEY")
    db.execute("ALTER TABLE users ADD PRIMARY KEY (new_id)")
    
    # 6. Add UNIQUE constraint
    db.execute("ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email)")
    
    # 7. Rename column
    db.execute("ALTER TABLE users CHANGE COLUMN new_id id INT")
    
    # 8. Add new Foreign Keys
    for fk in get_foreign_keys_to_users_email():
        db.execute(f"""
            ALTER TABLE {fk.table}
            ADD FOREIGN KEY (user_id) REFERENCES users(id)
        """)
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Primary Key là gì?**
   - Một hoặc nhiều columns định danh duy nhất mỗi row
   - UNIQUE, NOT NULL, INDEXED, IMMUTABLE

2. **Single Key vs Composite Key:**
   - Single Key: 1 column (đơn giản)
   - Composite Key: Nhiều columns (junction tables)

3. **Auto-increment vs UUID:**
   - Auto-increment: Single DB, performance
   - UUID: Distributed, security

4. **Tại sao không nên dùng Natural Key:**
   - Có thể thay đổi → phức tạp
   - Dùng Surrogate Key + UNIQUE constraint tốt hơn

5. **Primary Key có thể thay đổi không?**
   - Không nên (best practice)
   - Immutable → tránh phức tạp với Foreign Keys

---

### Câu 6.2: Hệ thống quản lý dự án

**a) Primary Key cho mỗi table:**

```sql
-- Projects
CREATE TABLE projects (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(200),
  created_at TIMESTAMP
);

-- Users
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(100) UNIQUE,
  name VARCHAR(100)
);

-- Project Members (junction table)
CREATE TABLE project_members (
  project_id INT,
  user_id INT,
  role VARCHAR(50),
  joined_at TIMESTAMP,
  PRIMARY KEY (project_id, user_id)  -- Composite Key
);

-- Tasks
CREATE TABLE tasks (
  id INT AUTO_INCREMENT PRIMARY KEY,
  project_id INT,
  title VARCHAR(200),
  status VARCHAR(50),
  created_at TIMESTAMP
);

-- Task Assignments (junction table)
CREATE TABLE task_assignments (
  task_id INT,
  user_id INT,
  assigned_at TIMESTAMP,
  PRIMARY KEY (task_id, user_id)  -- Composite Key
);
```

**b) Giải thích:**

- **`projects.id`**: Auto-increment - đơn giản, hiệu quả
- **`users.id`**: Auto-increment - tương tự
- **`project_members`**: Composite Key - junction table
- **`tasks.id`**: Auto-increment - tương tự
- **`task_assignments`**: Composite Key - junction table

**c) Nếu distributed:**

**Thay đổi:**

```sql
-- Projects, Users, Tasks
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Junction tables (không đổi)
PRIMARY KEY (project_id, user_id)  -- Vẫn Composite Key
PRIMARY KEY (task_id, user_id)     -- Vẫn Composite Key
```

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: Sequence vs Auto-increment

**a) Sequence là gì?**

**Sequence** là database object tạo ra sequence of numbers (1, 2, 3, ...).

**Khác với Auto-increment:**

| Tiêu chí | Auto-increment | Sequence |
|----------|----------------|----------|
| **Scope** | Per table | Database-wide |
| **Reuse** | Không thể reuse | Có thể reuse (nhiều tables) |
| **Control** | Ít control | Nhiều control (nextval, setval) |

**b) Khi nào dùng gì?**

**Auto-increment:**
- ✅ Mỗi table có ID riêng
- ✅ Đơn giản, dễ dùng

**Sequence:**
- ✅ Nhiều tables dùng chung sequence
- ✅ Cần control sequence (reset, skip)
- ✅ PostgreSQL (không có AUTO_INCREMENT)

**c) Ưu điểm Sequence:**

1. **Reusable**: Nhiều tables dùng chung sequence
2. **Control**: Có thể reset, skip numbers
3. **Flexible**: Có thể dùng cho nhiều purposes

---

### Câu A.2: UUID v4 vs UUID v1

**a) Sự khác biệt:**

| Tiêu chí | UUID v1 | UUID v4 |
|----------|---------|---------|
| **Generation** | Dựa trên MAC address + timestamp | Random |
| **Uniqueness** | Đảm bảo unique (MAC + time) | Random (rất khó trùng) |
| **Sortable** | Có thể sort theo time (một phần) | Không sortable |
| **Privacy** | Expose MAC address | Không expose gì |

**b) Khi nào dùng gì?**

**UUID v1:**
- ✅ Cần sort theo creation time (một phần)
- ✅ Không quan tâm privacy

**UUID v4:**
- ✅ Security quan trọng (không expose MAC)
- ✅ Hầu hết các trường hợp (recommended)

**c) UUID v1 có thể sort theo creation time không?**

**Đáp án: CÓ (một phần)**

UUID v1 có timestamp trong đó, nhưng không chính xác 100%. Vẫn nên dùng `created_at` column để sort.

---

### Câu A.3: Primary Key và Partitioning

**a) Primary Key ảnh hưởng đến partitioning:**

- Primary Key phải bao gồm partition key (trong một số databases)
- Partitioning thường dựa trên một column (ví dụ: `created_at`)

**b) Nếu partition theo `created_at`:**

**Option 1: Composite Key**
```sql
PRIMARY KEY (id, created_at)  -- created_at trong Primary Key
```

**Option 2: Single Key + Partition Key**
```sql
id INT PRIMARY KEY,
created_at DATE,  -- Partition key (không trong Primary Key)
PARTITION BY RANGE (created_at)
```

**c) Composite Key có thể dùng cho partitioning:**

**Đáp án: CÓ**

```sql
CREATE TABLE orders (
  order_id INT,
  created_at DATE,
  total_amount DECIMAL(10, 2),
  PRIMARY KEY (order_id, created_at),  -- Composite Key
  PARTITION BY RANGE (created_at)
);
```

**Lưu ý:** Một số databases yêu cầu partition key phải trong Primary Key.

---

## 📝 TÓM TẮT

### Key Learnings

1. **Primary Key là BẮT BUỘC**: Mọi table đều phải có
2. **Single Key vs Composite Key**: Single Key đơn giản, Composite Key cho junction tables
3. **Auto-increment vs UUID**: Auto-increment cho single DB, UUID cho distributed
4. **Natural Key**: Thường không nên dùng, dùng UNIQUE constraint thay thế
5. **Primary Key = Immutable**: Không nên update

### Best Practices

✅ **Luôn có Primary Key**: Không có exception
✅ **Ưu tiên Single Key**: ID column (auto-increment hoặc UUID)
✅ **Auto-increment cho single DB**: INT AUTO_INCREMENT
✅ **UUID cho distributed**: Nếu nhiều servers
✅ **UNIQUE constraint cho natural keys**: Thay vì dùng làm Primary Key
✅ **Primary Key = Immutable**: Không update Primary Key

---

**Chúc mừng hoàn thành Day-003!** 🎉

**Chuẩn bị cho Day-004: Foreign Key - Mối quan hệ giữa các bảng** 🚀

