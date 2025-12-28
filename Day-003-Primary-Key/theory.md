# Day-003: Primary Key - Định danh duy nhất

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Primary Key là gì và tại sao cần Primary Key
- Single Key vs Composite Key - khi nào dùng gì
- Auto-increment vs UUID vs Natural Key - trade-offs
- Cách chọn Primary Key phù hợp cho từng use case
- Hậu quả nếu không có Primary Key hoặc chọn sai Primary Key

---

## 1️⃣ PRIMARY KEY LÀ GÌ?

### **Nó là gì?**

**Primary Key** (Khóa chính) là một hoặc nhiều columns trong table dùng để **định danh duy nhất** mỗi row.

**Đặc điểm của Primary Key:**

1. **UNIQUE**: Không có 2 rows nào có cùng Primary Key value
2. **NOT NULL**: Primary Key không thể là NULL
3. **IMMUTABLE**: Giá trị Primary Key không nên thay đổi (best practice)
4. **INDEXED**: Database tự động tạo index trên Primary Key

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,    -- Primary Key
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**Trong table:**

```
┌────┬──────────┬─────────────┐
│ id │   name   │    email    │  ← id là Primary Key
├────┼──────────┼─────────────┤
│  1 │ John Doe │ john@ex.com │  ← Row 1: id = 1 (unique)
│  2 │ Jane Doe │ jane@ex.com │  ← Row 2: id = 2 (unique)
│  3 │ Bob Smith│ bob@ex.com  │  ← Row 3: id = 3 (unique)
└────┴──────────┴─────────────┘
```

**Lưu ý:**
- Mỗi table chỉ có **MỘT** Primary Key
- Primary Key có thể là **một column** (Single Key) hoặc **nhiều columns** (Composite Key)

### **Tại sao tồn tại?**

Primary Key tồn tại để giải quyết vấn đề **"Làm thế nào để xác định một row cụ thể?"**

**Vấn đề không có Primary Key:**

1. **Không thể xác định row duy nhất**: Làm sao biết "user John" là user nào nếu có nhiều users tên John?
2. **Không thể reference**: Làm sao bảng khác reference đến row này? (Foreign Key cần Primary Key)
3. **Khó update/delete**: Không biết chính xác row nào cần sửa/xóa
4. **Không có index tự động**: Query chậm hơn

**Với Primary Key:**

✅ **Xác định row duy nhất**: `WHERE id = 1` → chắc chắn chỉ có 1 row
✅ **Reference dễ dàng**: Foreign Key có thể reference đến Primary Key
✅ **Update/Delete chính xác**: Biết chính xác row nào cần thao tác
✅ **Index tự động**: Query nhanh hơn

### **Khi nào dùng trong production?**

**Primary Key là BẮT BUỘC** trong mọi table production:

✅ **Mọi table đều nên có Primary Key**: Không có exception
✅ **Chọn column phù hợp**: ID thường là Primary Key (auto-increment hoặc UUID)
✅ **Natural Key nếu phù hợp**: Email, SSN nếu đảm bảo unique

**KHÔNG nên:**
❌ Tạo table không có Primary Key
❌ Dùng Primary Key có thể thay đổi (như name, email - có thể đổi)
❌ Dùng Primary Key không stable (như timestamp - có thể trùng)

### **Hậu quả nếu không có Primary Key?**

**Tình huống thực tế:**

Developer tạo table `orders` không có Primary Key:

```sql
CREATE TABLE orders (
  user_id INT,
  product_id INT,
  quantity INT,
  order_date DATE
);
```

**Vấn đề:**

1. **Không thể xác định order cụ thể**: 
   - User đặt 2 orders cùng ngày → không biết order nào là order nào
   - Không thể reference từ bảng khác

2. **Không thể update/delete chính xác**:
   ```sql
   -- ❌ Không biết xóa order nào
   DELETE FROM orders WHERE user_id = 123;  -- Xóa TẤT CẢ orders của user!
   ```

3. **Không có index tự động**: Query chậm

4. **Foreign Key không thể reference**: Bảng `order_items` không thể có Foreign Key đến `orders`

**Cách fix:**

```sql
-- ✅ ĐÚNG: Thêm Primary Key
CREATE TABLE orders (
  id INT PRIMARY KEY,    -- Primary Key
  user_id INT,
  product_id INT,
  quantity INT,
  order_date DATE
);
```

**Kết luận**: **LUÔN có Primary Key** trong mọi table production.

---

## 2️⃣ SINGLE KEY VS COMPOSITE KEY

### **2.1. Single Key (Khóa đơn)**

**Nó là gì?**

Single Key là Primary Key chỉ gồm **MỘT column**.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,    -- Single Key (chỉ 1 column)
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**Đặc điểm:**

- ✅ Đơn giản, dễ hiểu
- ✅ Dễ reference (Foreign Key chỉ cần 1 column)
- ✅ Index hiệu quả (index trên 1 column)
- ✅ Thường dùng ID (auto-increment hoặc UUID)

**Khi nào dùng:**

✅ **Hầu hết các trường hợp**: Single Key là lựa chọn mặc định
✅ **Khi có ID column**: Auto-increment ID, UUID
✅ **Khi có Natural Key unique**: Email, SSN (nếu đảm bảo unique)

---

### **2.2. Composite Key (Khóa phức hợp)**

**Nó là gì?**

Composite Key là Primary Key gồm **NHIỀU columns** kết hợp lại.

**Ví dụ:**

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)  -- Composite Key (2 columns)
);
```

**Đặc điểm:**

- ✅ Phù hợp khi không có ID column riêng
- ✅ Đảm bảo unique dựa trên combination của columns
- ❌ Phức tạp hơn Single Key
- ❌ Foreign Key phải reference nhiều columns

**Khi nào dùng:**

✅ **Junction tables (many-to-many)**: 
   - `order_items`: (order_id, product_id) - một order có nhiều products, một product có trong nhiều orders
   - `user_roles`: (user_id, role_id)

✅ **Khi combination là unique tự nhiên**:
   - `enrollments`: (student_id, course_id, semester) - một student chỉ enroll một course một lần mỗi semester

**Ví dụ cụ thể:**

```sql
-- Junction table: user_roles
CREATE TABLE user_roles (
  user_id INT,
  role_id INT,
  assigned_at TIMESTAMP,
  PRIMARY KEY (user_id, role_id)  -- Composite Key
);
-- Một user có nhiều roles, một role có nhiều users
-- (user_id, role_id) đảm bảo unique: một user không thể có cùng role 2 lần
```

**So sánh:**

| Tiêu chí | Single Key | Composite Key |
|----------|------------|---------------|
| **Độ phức tạp** | Đơn giản | Phức tạp hơn |
| **Foreign Key** | Dễ (1 column) | Khó hơn (nhiều columns) |
| **Index** | Hiệu quả (1 column) | Có thể chậm hơn (nhiều columns) |
| **Khi nào dùng** | Hầu hết cases | Junction tables, natural combinations |

**Best practice:**

- **Ưu tiên Single Key**: Dùng ID column nếu có thể
- **Dùng Composite Key khi cần**: Junction tables, natural unique combinations

---

## 3️⃣ AUTO-INCREMENT VS UUID VS NATURAL KEY

### **3.1. Auto-increment (Tự động tăng)**

**Nó là gì?**

Auto-increment là Primary Key tự động tăng (1, 2, 3, 4, ...) mỗi khi insert row mới.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,  -- Auto-increment
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Insert
INSERT INTO users (name, email) VALUES ('John', 'john@ex.com');
-- id tự động = 1

INSERT INTO users (name, email) VALUES ('Jane', 'jane@ex.com');
-- id tự động = 2
```

**Đặc điểm:**

✅ **Đơn giản**: Database tự động tạo ID
✅ **Nhanh**: Integer, index hiệu quả
✅ **Sequential**: Dễ đọc, dễ debug (1, 2, 3, ...)
✅ **Storage nhỏ**: INT chỉ tốn 4 bytes

❌ **Predictable**: Có thể đoán được ID tiếp theo
❌ **Không phù hợp distributed systems**: Nhiều servers có thể tạo ID trùng
❌ **Không thể merge databases**: ID có thể conflict khi merge

**Khi nào dùng:**

✅ **Single database**: Một database, một server
✅ **Không cần security**: ID không cần ẩn
✅ **Performance quan trọng**: Cần query nhanh
✅ **Sequential access**: Thường query theo thứ tự (newest first)

**Lưu ý production:**

- **Range**: INT (2 tỷ) hoặc BIGINT (9 tỷ tỷ)
- **Gap**: Có thể có gap nếu rollback transaction
- **Not thread-safe across servers**: Nhiều servers → có thể conflict

---

### **3.2. UUID (Universally Unique Identifier)**

**Nó là gì?**

UUID là chuỗi 128-bit (36 ký tự) đảm bảo unique trên toàn thế giới.

**Ví dụ:**

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),  -- UUID
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Insert
INSERT INTO users (name, email) VALUES ('John', 'john@ex.com');
-- id = '550e8400-e29b-41d4-a716-446655440000' (tự động generate)
```

**Đặc điểm:**

✅ **Globally unique**: Đảm bảo unique trên toàn thế giới
✅ **Distributed-friendly**: Nhiều servers có thể tạo UUID không trùng
✅ **Security**: Không thể đoán được ID
✅ **Merge databases**: Có thể merge mà không conflict

❌ **Storage lớn**: 16 bytes (vs 4 bytes của INT)
❌ **Index chậm hơn**: String index chậm hơn integer index
❌ **Khó đọc**: '550e8400-e29b-41d4-a716-446655440000' khó nhớ
❌ **Not sequential**: Không thể sort theo creation time từ UUID

**Khi nào dùng:**

✅ **Distributed systems**: Nhiều servers, nhiều databases
✅ **Security quan trọng**: Không muốn expose sequential IDs
✅ **Merge databases**: Cần merge data từ nhiều sources
✅ **Microservices**: Mỗi service tự generate ID

**Lưu ý production:**

- **Version**: UUID v4 (random) phổ biến nhất
- **Performance**: Index trên UUID chậm hơn INT, nhưng vẫn acceptable
- **Storage**: 16 bytes vs 4 bytes INT → tốn hơn 4x, nhưng không đáng kể với modern systems

---

### **3.3. Natural Key (Khóa tự nhiên)**

**Nó là gì?**

Natural Key là Primary Key dựa trên **dữ liệu thực tế** (business data), không phải ID nhân tạo.

**Ví dụ:**

```sql
-- Email làm Primary Key
CREATE TABLE users (
  email VARCHAR(100) PRIMARY KEY,  -- Natural Key
  name VARCHAR(100)
);

-- SSN làm Primary Key
CREATE TABLE citizens (
  ssn VARCHAR(20) PRIMARY KEY,  -- Natural Key
  name VARCHAR(100)
);
```

**Đặc điểm:**

✅ **Có ý nghĩa business**: Email, SSN có ý nghĩa thực tế
✅ **Không cần ID riêng**: Tiết kiệm một column
✅ **Dễ query**: `WHERE email = 'user@ex.com'` không cần JOIN

❌ **Có thể thay đổi**: Email có thể đổi → phải update Primary Key (phức tạp)
❌ **Không phải lúc nào cũng unique**: Email có thể trùng (nếu không enforce)
❌ **Storage lớn**: VARCHAR tốn nhiều hơn INT
❌ **Index chậm hơn**: String index chậm hơn integer

**Khi nào dùng:**

✅ **Đảm bảo unique**: Email, SSN thực sự unique và stable
✅ **Không thay đổi**: Email, SSN không bao giờ đổi
✅ **Simple tables**: Table đơn giản, ít relationships

**KHÔNG nên dùng khi:**

❌ **Có thể thay đổi**: Name, address có thể đổi
❌ **Không chắc chắn unique**: Phone number có thể trùng
❌ **Complex relationships**: Nhiều Foreign Keys → tốn storage

**Best practice:**

- **Thường KHÔNG nên dùng Natural Key**: Dùng ID (auto-increment hoặc UUID) + UNIQUE constraint trên natural key
- **Chỉ dùng khi chắc chắn**: Natural key thực sự unique và không bao giờ đổi

**Ví dụ tốt hơn:**

```sql
-- ✅ TỐT HƠN: ID + UNIQUE constraint
CREATE TABLE users (
  id INT PRIMARY KEY,              -- Surrogate Key
  email VARCHAR(100) UNIQUE,        -- Natural Key với UNIQUE constraint
  name VARCHAR(100)
);
-- Ưu điểm: ID không đổi, email có thể đổi (update dễ dàng)
```

---

### **3.4. So sánh tổng hợp**

| Tiêu chí | Auto-increment | UUID | Natural Key |
|----------|----------------|------|-------------|
| **Storage** | Nhỏ (4-8 bytes) | Lớn (16 bytes) | Tùy (VARCHAR) |
| **Performance** | Nhanh nhất | Chậm hơn | Chậm nhất |
| **Unique scope** | Database | Global | Business domain |
| **Predictable** | Có | Không | Có thể |
| **Distributed** | Không | Có | Tùy |
| **Security** | Kém (dễ đoán) | Tốt | Tùy |
| **Khi nào dùng** | Single DB | Distributed | Simple, stable |

**Recommendation:**

1. **Single database, performance quan trọng**: Auto-increment INT
2. **Distributed systems, security quan trọng**: UUID
3. **Natural Key**: Chỉ dùng khi chắc chắn unique và stable, hoặc dùng làm UNIQUE constraint thay vì Primary Key

---

## 4️⃣ PRODUCTION STORY: VẤN ĐỀ DUPLICATE KEY TRONG PRODUCTION

### **Context**

Startup e-commerce có hệ thống đơn hàng. Table `orders` ban đầu:

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  order_number VARCHAR(20),  -- Mã đơn hàng (ví dụ: "ORD-2024-001")
  total_amount DECIMAL(10, 2),
  status VARCHAR(20),
  created_at TIMESTAMP
);
```

**Business requirement**: `order_number` phải unique (không được trùng).

### **Vấn đề xuất hiện**

**Ngày 1: Bug trong code generate order_number**

Code generate `order_number`:

```python
# ❌ SAI: Không thread-safe
def generate_order_number():
    last_order = Order.objects.order_by('-id').first()
    if last_order:
        last_num = int(last_order.order_number.split('-')[-1])
        new_num = last_num + 1
    else:
        new_num = 1
    return f"ORD-2024-{new_num:03d}"
```

**Vấn đề:**
- 2 requests cùng lúc → cả 2 đọc `last_num = 100`
- Cả 2 tạo `order_number = "ORD-2024-101"`
- **Duplicate key error!**

**Ngày 2: Race condition**

Khi có nhiều users đặt hàng cùng lúc:
- User A và User B cùng đặt hàng
- Cả 2 được assign cùng `order_number`
- Insert thứ 2 fail với duplicate key error
- **User B mất đơn hàng!**

### **Investigation**

**Bước 1: Check duplicate orders**

```sql
SELECT order_number, COUNT(*) as count
FROM orders
GROUP BY order_number
HAVING COUNT(*) > 1;
```

Kết quả: Có 15 orders bị duplicate `order_number`!

**Bước 2: Tìm root cause**

- Code không thread-safe
- Không có database-level constraint đảm bảo unique
- Race condition khi nhiều requests cùng lúc

**Root cause:**
1. Application-level generation không đảm bảo unique
2. Không có UNIQUE constraint trên `order_number`
3. Race condition trong concurrent requests

### **Fix**

**Fix 1: Thêm UNIQUE constraint**

```sql
ALTER TABLE orders
ADD CONSTRAINT unique_order_number UNIQUE (order_number);
```

**Fix 2: Fix code generation (Option A - Database sequence)**

```sql
-- Tạo sequence
CREATE SEQUENCE order_number_seq START 1;

-- Function generate order_number
CREATE OR REPLACE FUNCTION generate_order_number()
RETURNS VARCHAR(20) AS $$
DECLARE
    new_num INT;
BEGIN
    new_num := nextval('order_number_seq');
    RETURN 'ORD-2024-' || LPAD(new_num::TEXT, 3, '0');
END;
$$ LANGUAGE plpgsql;

-- Insert với function
INSERT INTO orders (user_id, order_number, total_amount, status)
VALUES (123, generate_order_number(), 100.00, 'pending');
```

**Fix 3: Fix code generation (Option B - Application-level với lock)**

```python
# ✅ ĐÚNG: Dùng database lock
from django.db import transaction

@transaction.atomic
def create_order(user_id, total_amount):
    # Lock table để đảm bảo atomic
    with connection.cursor() as cursor:
        cursor.execute("SELECT order_number FROM orders ORDER BY id DESC LIMIT 1 FOR UPDATE")
        last_order = cursor.fetchone()
        
        if last_order:
            last_num = int(last_order[0].split('-')[-1])
            new_num = last_num + 1
        else:
            new_num = 1
        
        order_number = f"ORD-2024-{new_num:03d}"
        
        # Insert
        cursor.execute(
            "INSERT INTO orders (user_id, order_number, total_amount, status) VALUES (%s, %s, %s, %s)",
            [user_id, order_number, total_amount, 'pending']
        )
```

**Fix 4: Better approach - Dùng UUID hoặc timestamp-based**

```sql
-- Option: UUID-based
order_number VARCHAR(36) DEFAULT gen_random_uuid()::TEXT

-- Option: Timestamp-based (với random suffix)
order_number VARCHAR(50) DEFAULT CONCAT('ORD-', TO_CHAR(NOW(), 'YYYYMMDDHH24MISS'), '-', SUBSTRING(MD5(RANDOM()::TEXT) FROM 1 FOR 6))
```

### **Kết quả**

✅ **UNIQUE constraint**: Đảm bảo không có duplicate ở database level
✅ **Thread-safe generation**: Sequence hoặc lock đảm bảo atomic
✅ **No more race conditions**: Mỗi order có order_number unique

### **Lesson Learned**

1. **LUÔN có UNIQUE constraint** trên columns cần unique (không chỉ Primary Key)
2. **Thread-safe generation**: Dùng database sequence hoặc lock
3. **Database-level constraints > Application-level**: Database đảm bảo data integrity tốt hơn
4. **Consider UUID**: Nếu không cần sequential, UUID đơn giản và an toàn hơn

---

## 5️⃣ BEST PRACTICES

### **5.1. Chọn Primary Key**

**Quy tắc:**

1. **Luôn có Primary Key**: Mọi table đều phải có
2. **Ưu tiên Single Key**: Dùng ID column nếu có thể
3. **Auto-increment cho single DB**: INT AUTO_INCREMENT
4. **UUID cho distributed**: Nếu nhiều servers
5. **Tránh Natural Key**: Trừ khi chắc chắn unique và stable

### **5.2. Primary Key không nên thay đổi**

**Vấn đề nếu thay đổi Primary Key:**

```sql
-- ❌ SAI: Update Primary Key
UPDATE users SET id = 999 WHERE id = 1;
-- Nếu có Foreign Keys reference đến id=1 → phải update tất cả!
```

**Best practice:**

- **Primary Key = Immutable**: Không bao giờ update
- **Nếu cần thay đổi**: Tạo column mới, migrate data, xóa column cũ

### **5.3. Primary Key và Index**

**Database tự động tạo index trên Primary Key:**

- ✅ Query `WHERE id = 1` rất nhanh
- ✅ JOIN trên Primary Key nhanh
- ✅ Không cần tạo index thủ công

**Lưu ý:**

- Index trên Primary Key là **clustered index** (một số databases)
- Rows được sắp xếp theo Primary Key → query sequential nhanh

### **5.4. Primary Key và Foreign Key**

**Primary Key được dùng làm Foreign Key:**

```sql
-- Table users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

-- Table orders (reference đến users)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)  -- Reference đến Primary Key
);
```

**Lưu ý:**

- Foreign Key phải reference đến Primary Key hoặc UNIQUE column
- Primary Key phải stable → Foreign Key không bị broken

---

## 6️⃣ TÓM TẮT

### **Key Takeaways**

1. **Primary Key là BẮT BUỘC**: Mọi table đều phải có
2. **Single Key vs Composite Key**: Single Key đơn giản hơn, Composite Key cho junction tables
3. **Auto-increment vs UUID**: Auto-increment cho single DB, UUID cho distributed
4. **Natural Key**: Thường không nên dùng, dùng UNIQUE constraint thay thế
5. **Primary Key không nên thay đổi**: Immutable best practice

### **Best Practices**

✅ **Luôn có Primary Key**: Không có exception
✅ **Ưu tiên Single Key**: ID column (auto-increment hoặc UUID)
✅ **Auto-increment cho single DB**: INT AUTO_INCREMENT
✅ **UUID cho distributed**: Nếu nhiều servers
✅ **UNIQUE constraint cho natural keys**: Thay vì dùng làm Primary Key
✅ **Primary Key = Immutable**: Không update Primary Key

### **Câu hỏi tự kiểm tra**

1. Tại sao cần Primary Key?
2. Single Key vs Composite Key - khi nào dùng gì?
3. Auto-increment vs UUID - trade-offs?
4. Tại sao không nên dùng Natural Key làm Primary Key?
5. Làm thế nào để đảm bảo order_number unique trong concurrent requests?

---




**Chuẩn bị cho [Day-004: Foreign-Key](Day-004-Foreign-Key/theory.md)** 🚀
