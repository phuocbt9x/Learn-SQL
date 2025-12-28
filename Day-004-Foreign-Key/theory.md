# Day-004: Foreign Key - Mối quan hệ giữa các bảng

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Foreign Key là gì và tại sao cần Foreign Key
- Referential Integrity là gì và tại sao quan trọng
- ON DELETE CASCADE vs RESTRICT vs SET NULL - khi nào dùng gì
- Khi nào nên/nên không dùng Foreign Key
- Hậu quả nếu không có Foreign Key constraint

---

## 1️⃣ FOREIGN KEY LÀ GÌ?

### **Nó là gì?**

**Foreign Key** (Khóa ngoại) là một hoặc nhiều columns trong table reference đến **Primary Key** (hoặc UNIQUE column) của table khác.

**Mục đích:** Tạo **mối quan hệ** (relationship) giữa các tables và đảm bảo **Referential Integrity** (tính toàn vẹn tham chiếu).

**Ví dụ:**

```sql
-- Table users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Table orders (reference đến users)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,                    -- Foreign Key column
  total_amount DECIMAL(10, 2),
  FOREIGN KEY (user_id) REFERENCES users(id)  -- Foreign Key constraint
);
```

**Trong table:**

```
users table:
┌────┬──────────┬─────────────┐
│ id │   name   │    email    │
├────┼──────────┼─────────────┤
│  1 │ John Doe │ john@ex.com │
│  2 │ Jane Doe │ jane@ex.com │
└────┴──────────┴─────────────┘

orders table:
┌────┬─────────┬──────────────┐
│ id │ user_id │ total_amount │  ← user_id là Foreign Key
├────┼─────────┼──────────────┤
│  1 │    1    │   100.00     │  ← Reference đến users.id = 1
│  2 │    1    │   200.00     │  ← Reference đến users.id = 1
│  3 │    2    │   150.00     │  ← Reference đến users.id = 2
└────┴─────────┴──────────────┘
```

**Đặc điểm của Foreign Key:**

1. **Reference đến Primary Key hoặc UNIQUE column**: Foreign Key phải reference đến column có UNIQUE constraint
2. **Đảm bảo Referential Integrity**: Không thể insert/update giá trị không tồn tại trong referenced table
3. **Có thể NULL**: Foreign Key có thể là NULL (nếu cho phép) - nghĩa là "không có relationship"
4. **Có thể có nhiều Foreign Keys**: Một table có thể có nhiều Foreign Keys reference đến nhiều tables khác

### **Tại sao tồn tại?**

Foreign Key tồn tại để giải quyết vấn đề **"Làm thế nào đảm bảo dữ liệu nhất quán giữa các tables?"**

**Vấn đề không có Foreign Key:**

1. **Orphan records**: Order có `user_id = 999` nhưng user với `id = 999` không tồn tại
2. **Data inconsistency**: Dữ liệu không nhất quán giữa các tables
3. **Khó maintain**: Phải tự check trong application code
4. **Dễ lỗi**: Developer quên check → insert invalid data

**Với Foreign Key:**

✅ **Đảm bảo Referential Integrity**: Không thể insert order với user_id không tồn tại
✅ **Database tự động enforce**: Không cần check trong application code
✅ **Cascade operations**: Có thể tự động xóa/update related records
✅ **Self-documenting**: Schema tự giải thích relationships

### **Khi nào dùng trong production?**

Foreign Key nên dùng khi:

✅ **Có relationship giữa tables**: One-to-many, many-to-one
✅ **Cần đảm bảo data integrity**: Không thể có orphan records
✅ **Cần cascade operations**: Xóa user → tự động xóa orders
✅ **OLTP systems**: Transaction systems cần data integrity

**KHÔNG nên dùng Foreign Key khi:**

❌ **Performance-critical, read-heavy**: Foreign Key có overhead (check constraint)
❌ **Data warehouse**: Analytics, không cần real-time integrity
❌ **Temporary/staging tables**: Data import, không cần constraints
❌ **Cross-database relationships**: Foreign Key chỉ hoạt động trong cùng database

### **Hậu quả nếu không có Foreign Key?**

**Tình huống thực tế: Lỗi orphan records do thiếu Foreign Key constraint**

**Context:**

Hệ thống e-commerce có 2 tables:

```sql
-- Table users (KHÔNG có Foreign Key constraint)
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

-- Table orders (KHÔNG có Foreign Key constraint)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- Không có FOREIGN KEY constraint!
  total_amount DECIMAL(10, 2)
);
```

**Vấn đề:**

1. **Orphan records**: 
   - User với `id = 1` bị xóa
   - Orders với `user_id = 1` vẫn còn → **orphan records** (không có parent)

2. **Invalid data**:
   - Insert order với `user_id = 999` (user không tồn tại) → **không bị lỗi!**
   - Data không nhất quán

3. **Query errors**:
   ```sql
   -- Query này có thể trả về NULL hoặc không trả về gì
   SELECT o.*, u.name
   FROM orders o
   LEFT JOIN users u ON o.user_id = u.id
   WHERE u.id IS NULL;  -- Tìm orphan records
   ```

**Cách fix:**

```sql
-- ✅ ĐÚNG: Thêm Foreign Key constraint
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);
```

**Kết quả:**
- Không thể insert order với user_id không tồn tại
- Không thể xóa user nếu có orders (hoặc cascade delete)
- Data luôn nhất quán

---

## 2️⃣ REFERENTIAL INTEGRITY LÀ GÌ?

### **Nó là gì?**

**Referential Integrity** (Tính toàn vẹn tham chiếu) là đảm bảo rằng **mọi Foreign Key value đều tồn tại** trong referenced table.

**Nói cách khác:** Không thể có "orphan records" - records reference đến records không tồn tại.

**Ví dụ:**

```sql
-- ✅ CÓ Referential Integrity
users: id = 1, 2, 3
orders: user_id = 1, 2, 3  -- Tất cả đều tồn tại trong users

-- ❌ KHÔNG có Referential Integrity
users: id = 1, 2, 3
orders: user_id = 1, 2, 999  -- 999 không tồn tại trong users → VI PHẠM
```

**Foreign Key constraint đảm bảo Referential Integrity:**

1. **INSERT**: Không thể insert Foreign Key value không tồn tại
2. **UPDATE**: Không thể update Foreign Key value thành giá trị không tồn tại
3. **DELETE**: Không thể xóa referenced record nếu có Foreign Keys reference đến (trừ khi dùng CASCADE)

### **Tại sao quan trọng?**

Referential Integrity đảm bảo:

1. **Data consistency**: Dữ liệu luôn nhất quán giữa các tables
2. **No orphan records**: Không có records "mồ côi" (không có parent)
3. **Query reliability**: JOINs luôn trả về kết quả đúng
4. **Business logic correctness**: Đảm bảo business rules (ví dụ: order phải có user)

**Ví dụ thực tế:**

```sql
-- ❌ KHÔNG có Referential Integrity
SELECT o.*, u.name
FROM orders o
LEFT JOIN users u ON o.user_id = u.id;
-- Có thể có orders với user_id không tồn tại → u.name = NULL

-- ✅ CÓ Referential Integrity
-- Foreign Key đảm bảo mọi user_id đều tồn tại
-- JOIN luôn trả về kết quả đúng
```

### **Khi nào cần Referential Integrity?**

**Cần khi:**

✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Business-critical data**: Orders, payments, accounts
✅ **Complex relationships**: Nhiều tables có relationships
✅ **Multi-user systems**: Nhiều users cùng thao tác → cần database enforce

**KHÔNG cần khi:**

❌ **Data warehouse**: Analytics, data đã được clean
❌ **Staging tables**: Temporary data, sẽ được validate sau
❌ **Read-only data**: Data không thay đổi, không cần real-time integrity

---

## 3️⃣ ON DELETE CASCADE VS RESTRICT VS SET NULL

### **3.1. ON DELETE CASCADE**

**Nó là gì?**

**ON DELETE CASCADE** nghĩa là: Khi xóa record trong **referenced table** (parent), tự động xóa tất cả records trong **referencing table** (child) có Foreign Key reference đến.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

**Hành vi:**

```sql
-- Xóa user
DELETE FROM users WHERE id = 1;

-- Tự động xóa TẤT CẢ orders có user_id = 1
-- Không cần xóa thủ công!
```

**Khi nào dùng:**

✅ **Parent-child relationship rõ ràng**: User → Orders (orders không có ý nghĩa nếu không có user)
✅ **Cascade makes sense**: Xóa user → xóa orders là hợp lý
✅ **Không cần giữ lại child records**: Child records không có giá trị nếu không có parent

**KHÔNG nên dùng khi:**

❌ **Child records có giá trị độc lập**: Ví dụ: Orders có thể cần giữ lại cho audit
❌ **Cascade quá sâu**: Xóa user → xóa orders → xóa order_items → xóa payments (có thể nguy hiểm)
❌ **Business logic phức tạp**: Có thể cần soft delete thay vì hard delete

**Lưu ý production:**

- **Cẩn thận với CASCADE**: Một DELETE có thể xóa nhiều records → có thể mất dữ liệu nếu không cẩn thận
- **Test thoroughly**: Đảm bảo CASCADE hoạt động đúng như mong đợi
- **Consider soft delete**: Thay vì xóa, đánh dấu `deleted_at = NOW()`

---

### **3.2. ON DELETE RESTRICT (hoặc NO ACTION)**

**Nó là gì?**

**ON DELETE RESTRICT** nghĩa là: **KHÔNG cho phép** xóa record trong referenced table nếu có Foreign Keys reference đến.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
);
```

**Hành vi:**

```sql
-- Cố gắng xóa user có orders
DELETE FROM users WHERE id = 1;
-- ❌ ERROR: Cannot delete user because there are orders referencing it

-- Phải xóa orders trước
DELETE FROM orders WHERE user_id = 1;
DELETE FROM users WHERE id = 1;  -- ✅ OK
```

**Khi nào dùng:**

✅ **Child records quan trọng**: Orders cần giữ lại (audit, history)
✅ **Explicit deletion**: Muốn developer phải xóa child records thủ công (an toàn hơn)
✅ **Business logic phức tạp**: Có thể cần check business rules trước khi xóa

**Đây là DEFAULT** trong hầu hết databases (nếu không specify).

---

### **3.3. ON DELETE SET NULL**

**Nó là gì?**

**ON DELETE SET NULL** nghĩa là: Khi xóa record trong referenced table, set Foreign Key column trong referencing table thành **NULL**.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- Phải cho phép NULL
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);
```

**Hành vi:**

```sql
-- Xóa user
DELETE FROM users WHERE id = 1;

-- Tự động set user_id = NULL cho tất cả orders có user_id = 1
-- Orders vẫn còn, nhưng user_id = NULL
```

**Khi nào dùng:**

✅ **Optional relationship**: Foreign Key có thể NULL (không bắt buộc)
✅ **Preserve child records**: Muốn giữ lại child records nhưng đánh dấu "không có parent"
✅ **Historical data**: Orders cần giữ lại cho audit, nhưng user đã bị xóa

**KHÔNG nên dùng khi:**

❌ **Required relationship**: Foreign Key không được NULL (NOT NULL constraint)
❌ **Business logic không cho phép**: Orders phải có user, không thể NULL

---

### **3.4. So sánh tổng hợp**

| Option | Hành vi khi xóa parent | Khi nào dùng |
|--------|------------------------|-------------|
| **CASCADE** | Tự động xóa child records | Child không có giá trị nếu không có parent |
| **RESTRICT** | Không cho phép xóa (error) | Child quan trọng, cần giữ lại |
| **SET NULL** | Set Foreign Key = NULL | Optional relationship, preserve child |

**Ví dụ cụ thể:**

```sql
-- Users và Orders
-- Option 1: CASCADE (xóa user → xóa orders)
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
-- Dùng khi: Orders không có giá trị nếu không có user

-- Option 2: RESTRICT (không cho xóa user nếu có orders)
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT
-- Dùng khi: Orders cần giữ lại cho audit

-- Option 3: SET NULL (xóa user → set user_id = NULL)
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
-- Dùng khi: Orders có thể không có user (optional)
```

**Best practice:**

- **Default: RESTRICT** - An toàn nhất, phải explicit xóa child
- **CASCADE**: Chỉ dùng khi chắc chắn cascade là đúng
- **SET NULL**: Chỉ dùng khi relationship là optional

---

## 4️⃣ KHI NÀO NÊN/NÊN KHÔNG DÙNG FOREIGN KEY?

### **4.1. Nên dùng Foreign Key khi:**

✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Business-critical data**: Orders, payments, accounts
✅ **Complex relationships**: Nhiều tables có relationships
✅ **Multi-user systems**: Nhiều users cùng thao tác
✅ **Need referential integrity**: Cần đảm bảo không có orphan records
✅ **Self-documenting schema**: Foreign Key tự giải thích relationships

**Ví dụ:**

```sql
-- E-commerce system
CREATE TABLE users (...);
CREATE TABLE orders (
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)  -- ✅ Nên dùng
);
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  FOREIGN KEY (order_id) REFERENCES orders(id),  -- ✅ Nên dùng
  FOREIGN KEY (product_id) REFERENCES products(id)  -- ✅ Nên dùng
);
```

---

### **4.2. KHÔNG nên dùng Foreign Key khi:**

❌ **Performance-critical, read-heavy**: Foreign Key có overhead (check constraint mỗi lần insert/update)
❌ **Data warehouse**: Analytics, data đã được clean, không cần real-time integrity
❌ **Temporary/staging tables**: Data import, sẽ được validate sau
❌ **Cross-database relationships**: Foreign Key chỉ hoạt động trong cùng database
❌ **High-frequency inserts**: Nhiều inserts/giây → Foreign Key check có thể chậm
❌ **Legacy systems**: Hệ thống cũ không có Foreign Key, khó thêm vào

**Ví dụ:**

```sql
-- Data warehouse (analytics)
CREATE TABLE fact_sales (...);  -- ❌ Không cần Foreign Key
CREATE TABLE dim_products (...);  -- ❌ Không cần Foreign Key

-- Staging table (data import)
CREATE TABLE staging_orders (...);  -- ❌ Không cần Foreign Key
-- Data sẽ được validate và import vào production tables sau
```

**Trade-offs:**

| Tiêu chí | Có Foreign Key | Không có Foreign Key |
|----------|----------------|---------------------|
| **Data integrity** | ✅ Đảm bảo | ❌ Phải tự check |
| **Performance** | ❌ Có overhead | ✅ Nhanh hơn |
| **Complexity** | ✅ Database tự enforce | ❌ Phải code logic |
| **Flexibility** | ❌ Khó thay đổi | ✅ Linh hoạt hơn |

---

## 5️⃣ PRODUCTION STORY: LỖI ORPHAN RECORDS DO THIẾU FOREIGN KEY CONSTRAINT

### **Context**

Startup e-commerce có hệ thống đơn hàng. Ban đầu không có Foreign Key constraints:

```sql
-- Table users
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Table orders (KHÔNG có Foreign Key)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- ❌ Không có FOREIGN KEY constraint
  total_amount DECIMAL(10, 2),
  status VARCHAR(20)
);
```

**Business logic:** Mỗi order phải có user hợp lệ.

### **Vấn đề xuất hiện**

**Tháng 1: Bug trong code**

Code xóa user không check orders:

```python
# ❌ SAI: Xóa user mà không check orders
def delete_user(user_id):
    db.execute("DELETE FROM users WHERE id = %s", [user_id])
    # Không check xem user có orders không!
```

**Hậu quả:**
- User với `id = 1` bị xóa
- Orders với `user_id = 1` vẫn còn → **orphan records**

**Tháng 2: Invalid data**

Code insert order không validate user_id:

```python
# ❌ SAI: Không validate user_id
def create_order(user_id, total_amount):
    db.execute(
        "INSERT INTO orders (user_id, total_amount) VALUES (%s, %s)",
        [user_id, total_amount]
    )
    # Không check xem user_id có tồn tại không!
```

**Hậu quả:**
- Insert order với `user_id = 999` (user không tồn tại) → **không bị lỗi!**
- Data không nhất quán

**Tháng 3: Query errors**

Queries bắt đầu trả về kết quả sai:

```sql
-- Query tính revenue theo user
SELECT u.name, SUM(o.total_amount) as revenue
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
-- ❌ Thiếu revenue từ orphan orders (user_id không tồn tại)

-- Query tìm orders của user
SELECT * FROM orders WHERE user_id = 1;
-- ❌ Trả về orders nhưng user không tồn tại → không biết user là ai
```

### **Investigation**

**Bước 1: Tìm orphan records**

```sql
-- Tìm orders có user_id không tồn tại
SELECT o.*
FROM orders o
LEFT JOIN users u ON o.user_id = u.id
WHERE u.id IS NULL;
```

Kết quả: **150 orders** là orphan records!

**Bước 2: Tìm invalid user_ids**

```sql
-- Tìm user_ids không hợp lệ
SELECT DISTINCT user_id
FROM orders
WHERE user_id NOT IN (SELECT id FROM users);
```

Kết quả: `user_id = 999, 1000, 1001` không tồn tại.

**Root cause:**
1. Không có Foreign Key constraint → database không enforce
2. Application code không validate → insert invalid data
3. Delete user không check → tạo orphan records

### **Fix**

**Fix 1: Thêm Foreign Key constraint**

```sql
-- Thêm Foreign Key constraint
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT;
```

**Fix 2: Clean up orphan records**

```sql
-- Option 1: Xóa orphan orders (nếu không quan trọng)
DELETE FROM orders
WHERE user_id NOT IN (SELECT id FROM users);

-- Option 2: Assign orphan orders to a default user
UPDATE orders
SET user_id = (SELECT id FROM users WHERE email = 'admin@example.com')
WHERE user_id NOT IN (SELECT id FROM users);
```

**Fix 3: Fix application code**

```python
# ✅ ĐÚNG: Validate user_id trước khi insert
def create_order(user_id, total_amount):
    # Check user exists
    user = db.execute("SELECT id FROM users WHERE id = %s", [user_id])
    if not user:
        raise ValueError(f"User {user_id} does not exist")
    
    # Insert order
    db.execute(
        "INSERT INTO orders (user_id, total_amount) VALUES (%s, %s)",
        [user_id, total_amount]
    )

# ✅ ĐÚNG: Check orders trước khi xóa user
def delete_user(user_id):
    # Check if user has orders
    orders = db.execute("SELECT id FROM orders WHERE user_id = %s", [user_id])
    if orders:
        raise ValueError(f"User {user_id} has orders, cannot delete")
    
    # Delete user
    db.execute("DELETE FROM users WHERE id = %s", [user_id])
```

### **Kết quả**

✅ **Foreign Key constraint**: Database tự động enforce → không thể insert invalid data
✅ **No more orphan records**: Mọi order đều có user hợp lệ
✅ **Query reliability**: JOINs luôn trả về kết quả đúng
✅ **Self-documenting**: Schema tự giải thích relationships

### **Lesson Learned**

1. **LUÔN dùng Foreign Key** cho relationships trong OLTP systems
2. **Database constraints > Application validation**: Database enforce tốt hơn application code
3. **ON DELETE RESTRICT** là an toàn nhất (default)
4. **Clean up orphan records** trước khi thêm Foreign Key constraint
5. **Test thoroughly**: Đảm bảo Foreign Key hoạt động đúng

---

## 6️⃣ BEST PRACTICES

### **6.1. Naming Convention**

**Best practice:** Đặt tên Foreign Key constraint rõ ràng:

```sql
-- ✅ TỐT: Tên rõ ràng
ALTER TABLE orders
ADD CONSTRAINT fk_orders_user_id
FOREIGN KEY (user_id) REFERENCES users(id);

-- ❌ XẤU: Tên không rõ ràng
ALTER TABLE orders
ADD CONSTRAINT fk1 FOREIGN KEY (user_id) REFERENCES users(id);
```

**Convention:**
- `fk_<table>_<column>`: `fk_orders_user_id`
- Hoặc: `fk_<referencing_table>_<referenced_table>`: `fk_orders_users`

---

### **6.2. Index trên Foreign Key**

**Best practice:** Database thường tự động tạo index trên Foreign Key, nhưng nên verify:

```sql
-- Check indexes
SHOW INDEXES FROM orders;

-- Nếu không có index, tạo thủ công
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**Lý do:** Foreign Key thường được dùng trong JOINs → cần index để nhanh.

---

### **6.3. Multiple Foreign Keys**

**Một table có thể có nhiều Foreign Keys:**

```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

**Lưu ý:** Mỗi Foreign Key có thể có ON DELETE action khác nhau.

---

### **6.4. Self-referencing Foreign Key**

**Foreign Key có thể reference đến chính table đó:**

```sql
-- Employees table (manager là employee khác)
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  manager_id INT,
  FOREIGN KEY (manager_id) REFERENCES employees(id)  -- Self-reference
);
```

**Use case:** Hierarchical data (tree structure).

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Foreign Key** tạo mối quan hệ giữa tables và đảm bảo Referential Integrity
2. **Referential Integrity** đảm bảo không có orphan records
3. **ON DELETE CASCADE/RESTRICT/SET NULL** - chọn đúng cho từng use case
4. **Nên dùng Foreign Key** trong OLTP systems, **KHÔNG nên** trong data warehouse
5. **Database constraints > Application validation** - database enforce tốt hơn

### **Best Practices**

✅ **Luôn dùng Foreign Key** cho relationships trong OLTP systems
✅ **ON DELETE RESTRICT** là default (an toàn nhất)
✅ **Đặt tên constraint rõ ràng**: `fk_orders_user_id`
✅ **Verify indexes** trên Foreign Key columns
✅ **Clean up orphan records** trước khi thêm Foreign Key

### **Câu hỏi tự kiểm tra**

1. Foreign Key là gì? Tại sao cần Foreign Key?
2. Referential Integrity là gì? Tại sao quan trọng?
3. ON DELETE CASCADE vs RESTRICT vs SET NULL - khi nào dùng gì?
4. Khi nào nên dùng Foreign Key? Khi nào không nên?
5. Làm thế nào để xử lý orphan records?

---

**Chuẩn bị cho Day-005: Normalization - Chuẩn hóa dữ liệu (1NF)** 🚀

