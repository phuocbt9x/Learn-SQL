# Day-007: Normalization - 3NF (Third Normal Form)

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- 3NF (Third Normal Form) là gì và tại sao cần 3NF
- Transitive dependency là gì và cách nhận biết
- Khi nào dừng ở 2NF? Khi nào cần 3NF?
- Cách sửa vi phạm 3NF
- Hậu quả nếu vi phạm 3NF trong production

---

## 1️⃣ 3NF (THIRD NORMAL FORM) LÀ GÌ?

### **Nó là gì?**

**3NF (Third Normal Form)** yêu cầu:

1. **Đã tuân thủ 2NF** (Second Normal Form)
2. **Không có Transitive Dependency** (Phụ thuộc bắc cầu)

**Transitive Dependency là gì?**

**Transitive Dependency** (Phụ thuộc bắc cầu) xảy ra khi:
- Một non-key column phụ thuộc vào **một non-key column khác**
- Non-key column đó lại phụ thuộc vào Primary Key
- → Tạo ra "chuỗi phụ thuộc": PK → non-key A → non-key B

**Nói cách khác:**
- Column B phụ thuộc vào Column A (không phải PK)
- Column A phụ thuộc vào PK
- → Column B phụ thuộc vào PK **qua** Column A (transitive)

### **Tại sao tồn tại?**

3NF tồn tại để giải quyết vấn đề **Transitive Dependency**:

1. **Data redundancy**: Dữ liệu trùng lặp không cần thiết
2. **Update anomalies**: Sửa một chỗ, phải sửa nhiều chỗ
3. **Insert anomalies**: Khó insert một số dữ liệu
4. **Delete anomalies**: Xóa một record → mất dữ liệu khác

**Ví dụ đơn giản:**

```
❌ VI PHẠM 3NF:
orders table:
id | user_id | user_name | user_email        | total_amount
---|---------|-----------|-------------------|-------------
1  | 101     | John Doe  | john@example.com | 100.00
2  | 101     | John Doe  | john@example.com | 200.00
3  | 102     | Jane Doe  | jane@example.com | 150.00

Vấn đề:
- user_name, user_email phụ thuộc vào user_id (không phải PK)
- user_id phụ thuộc vào id (PK)
- → Transitive Dependency: id → user_id → user_name/user_email
- user_name, user_email bị duplicate (John xuất hiện 2 lần)
- Nếu đổi email "john@example.com" → "john.new@example.com" → phải sửa 2 rows

✅ TUÂN THỦ 3NF:
users table:
user_id | user_name | user_email
--------|-----------|-------------------
101     | John Doe  | john@example.com
102     | Jane Doe  | jane@example.com

orders table:
id | user_id | total_amount
---|---------|-------------
1  | 101     | 100.00
2  | 101     | 200.00
3  | 102     | 150.00

Ưu điểm:
- user_name, user_email chỉ lưu một lần
- Đổi email → chỉ sửa 1 chỗ
- Không duplicate
```

### **Khi nào cần trong production?**

**3NF cần khi:**

✅ **Đã tuân thủ 2NF**: Phải tuân thủ 2NF trước
✅ **Có Transitive Dependency**: Non-key column phụ thuộc vào non-key column khác
✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Frequent updates**: Dữ liệu thường xuyên thay đổi

**KHÔNG cần 3NF khi:**

❌ **Data warehouse**: Analytics, có thể denormalize để tăng performance
❌ **Read-only data**: Data không thay đổi → không có update anomalies
❌ **Simple applications**: Ứng dụng đơn giản, ít relationships

**Lưu ý:** Hầu hết tables production nên tuân thủ 3NF để đảm bảo data integrity.

---

## 2️⃣ TRANSITIVE DEPENDENCY LÀ GÌ?

### **Nó là gì?**

**Transitive Dependency** (Phụ thuộc bắc cầu) xảy ra khi:

1. **Có Primary Key** (PK)
2. **Có non-key column A** phụ thuộc vào PK
3. **Có non-key column B** phụ thuộc vào column A (không phải PK)
4. → Column B phụ thuộc vào PK **qua** column A (transitive)

**Ví dụ:**

```sql
-- ❌ VI PHẠM 3NF
CREATE TABLE orders (
  id INT PRIMARY KEY,           -- PK
  user_id INT,                   -- Non-key, phụ thuộc vào id
  user_name VARCHAR(100),        -- Non-key, phụ thuộc vào user_id (không phải PK)
  user_email VARCHAR(100),       -- Non-key, phụ thuộc vào user_id (không phải PK)
  total_amount DECIMAL(10, 2)
);
```

**Phân tích:**

- **PK**: `id`
- **user_id**: Phụ thuộc vào `id` (PK) → OK
- **user_name**: Phụ thuộc vào `user_id` (không phải PK) → **Transitive Dependency**
- **user_email**: Phụ thuộc vào `user_id` (không phải PK) → **Transitive Dependency**

→ **Vi phạm 3NF**

### **Tại sao quan trọng?**

Transitive Dependency gây ra:

1. **Data redundancy**: 
   - `user_name`, `user_email` bị duplicate trong nhiều orders
   - Tốn storage không cần thiết

2. **Update anomalies**:
   - Đổi email user → phải sửa nhiều rows trong `orders`
   - Dễ quên, dễ lỗi

3. **Insert anomalies**:
   - Không thể insert user mới nếu chưa có order
   - Phải tạo order giả → không hợp lý

4. **Delete anomalies**:
   - Xóa order cuối cùng của user → mất thông tin user
   - Không hợp lý

### **Khi nào xảy ra?**

Transitive Dependency xảy ra khi:

✅ **Có non-key column phụ thuộc vào non-key column khác** (không phải PK)

**Ví dụ cụ thể:**

```sql
-- Transitive Dependency
orders: id (PK) → user_id → user_name, user_email

-- Không phải Transitive Dependency
orders: id (PK) → user_id → total_amount (phụ thuộc trực tiếp vào id, không qua user_id)
```

---

## 3️⃣ CÁCH NHẬN BIẾT VÀ SỬA VI PHẠM 3NF

### **3.1. Cách nhận biết**

**Bước 1: Xác định Primary Key**
- Primary Key là gì?

**Bước 2: Xác định dependencies**
- Non-key columns phụ thuộc vào gì?
- Có column nào phụ thuộc vào non-key column khác không?

**Bước 3: Kiểm tra Transitive Dependency**
- Column B phụ thuộc vào Column A (không phải PK)?
- Column A phụ thuộc vào PK?
- → Transitive Dependency

**Ví dụ:**

```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  department_name VARCHAR(100),  -- Phụ thuộc vào department_id
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2)
);
```

**Phân tích:**
- **PK**: `id`
- **department_id**: Phụ thuộc vào `id` (PK) → OK
- **department_name**: Phụ thuộc vào `department_id` (không phải PK) → **Transitive Dependency**
- **employee_name**: Phụ thuộc vào `id` (PK) → OK
- **salary**: Phụ thuộc vào `id` (PK) → OK

→ **Vi phạm 3NF**

### **3.2. Cách sửa**

**Nguyên tắc:** Tách columns có Transitive Dependency thành bảng riêng.

**Ví dụ:**

```sql
-- ❌ VI PHẠM 3NF
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),  -- Transitive Dependency
  user_email VARCHAR(100),  -- Transitive Dependency
  total_amount DECIMAL(10, 2)
);
```

**Cách sửa:**

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

**Kết quả:**
- `user_name`, `user_email` chỉ lưu một lần trong `users`
- `orders` chỉ lưu `user_id` (reference)
- Không có Transitive Dependency

---

## 4️⃣ VÍ DỤ CỤ THỂ

### **4.1. Ví dụ: Orders với User Info**

**Vi phạm 3NF:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),      -- Transitive Dependency
  user_email VARCHAR(100),      -- Transitive Dependency
  user_phone VARCHAR(20),       -- Transitive Dependency
  total_amount DECIMAL(10, 2)
);
```

**Vấn đề:**
- `user_name`, `user_email`, `user_phone` phụ thuộc vào `user_id` (không phải PK)
- Bị duplicate trong nhiều orders
- Update user → phải sửa nhiều rows

**Cách sửa:**

```sql
-- Tách thành 2 tables
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
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

### **4.2. Ví dụ: Employees với Department Info**

**Vi phạm 3NF:**

```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  department_id INT,
  department_name VARCHAR(100),  -- Transitive Dependency
  department_location VARCHAR(100),  -- Transitive Dependency
  employee_name VARCHAR(100),
  salary DECIMAL(10, 2)
);
```

**Vấn đề:**
- `department_name`, `department_location` phụ thuộc vào `department_id` (không phải PK)
- Bị duplicate trong nhiều employees
- Update department → phải sửa nhiều rows

**Cách sửa:**

```sql
-- Tách thành 2 tables
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

---

### **4.3. Ví dụ: Products với Category Info**

**Vi phạm 3NF:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  category_id INT,
  category_name VARCHAR(100),    -- Transitive Dependency
  category_description TEXT,     -- Transitive Dependency
  product_name VARCHAR(200),
  price DECIMAL(10, 2)
);
```

**Vấn đề:**
- `category_name`, `category_description` phụ thuộc vào `category_id` (không phải PK)
- Bị duplicate trong nhiều products
- Update category → phải sửa nhiều rows

**Cách sửa:**

```sql
-- Tách thành 2 tables
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

---

## 5️⃣ KHI NÀO DỪNG Ở 2NF? KHI NÀO CẦN 3NF?

### **5.1. Khi nào có thể dừng ở 2NF?**

**Có thể dừng ở 2NF khi:**

✅ **Không có Transitive Dependency**: Tất cả non-key columns phụ thuộc trực tiếp vào PK
✅ **Data warehouse**: Analytics, có thể denormalize để tăng performance
✅ **Read-only data**: Data không thay đổi → không có update anomalies
✅ **Simple applications**: Ứng dụng đơn giản, ít relationships

**Ví dụ:**

```sql
-- ✅ Có thể dừng ở 2NF (không có Transitive Dependency)
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  price_at_order DECIMAL(10, 2),  -- Phụ thuộc vào (order_id, product_id)
  PRIMARY KEY (order_id, product_id)
);
-- Tất cả columns phụ thuộc trực tiếp vào PK → không có Transitive Dependency
```

---

### **5.2. Khi nào cần 3NF?**

**Cần 3NF khi:**

✅ **Có Transitive Dependency**: Non-key column phụ thuộc vào non-key column khác
✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Frequent updates**: Dữ liệu thường xuyên thay đổi
✅ **Data integrity critical**: Cần đảm bảo dữ liệu nhất quán

**Ví dụ:**

```sql
-- ❌ Cần 3NF (có Transitive Dependency)
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),  -- Transitive Dependency
  total_amount DECIMAL(10, 2)
);
-- user_name phụ thuộc vào user_id (không phải PK) → cần normalize
```

---

### **5.3. Best Practice**

**Recommendation:**

- **OLTP systems**: Tuân thủ 3NF (đảm bảo data integrity)
- **Data warehouse**: Có thể dừng ở 2NF hoặc denormalize (performance quan trọng hơn)
- **Simple applications**: Có thể dừng ở 2NF nếu không có Transitive Dependency

**Lưu ý:** Hầu hết tables production nên tuân thủ 3NF để tránh update anomalies.

---

## 6️⃣ PRODUCTION STORY: UPDATE ANOMALY DO VI PHẠM 3NF

### **Context**

Startup e-commerce có table `orders` vi phạm 3NF:

```sql
-- ❌ VI PHẠM 3NF
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_name VARCHAR(100),      -- Transitive Dependency
  user_email VARCHAR(100),      -- Transitive Dependency
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP
);
```

**Business logic:** Mỗi order có một user, mỗi user có name và email.

### **Vấn đề xuất hiện**

**Tháng 1: Data redundancy**

Khi có 10,000 orders từ 1,000 users:
- `user_name` bị duplicate 10 lần trung bình
- `user_email` bị duplicate 10 lần trung bình
- **Storage: 10x redundancy!**

**Tháng 2: Update nightmare**

User "John Doe" đổi email từ "john@example.com" → "john.new@example.com":

```sql
-- ❌ Phải update 100 rows!
UPDATE orders 
SET user_email = 'john.new@example.com'
WHERE user_id = 101;
-- Mất 5 giây, lock table
```

**Vấn đề:**
- Phải update nhiều rows
- Dễ quên một số rows
- Lock table lâu

**Tháng 3: Inconsistency**

Sau một số updates, có inconsistency:

```sql
-- ❌ Inconsistency: Cùng user_id nhưng user_email khác nhau
SELECT user_id, user_email, COUNT(*)
FROM orders
GROUP BY user_id, user_email;
-- user_id = 101: "john@example.com" (90 rows), "john.new@example.com" (10 rows)
```

**Hậu quả:**
- Data không nhất quán
- Emails gửi đến sai địa chỉ
- Users complaints

**Tháng 4: Delete anomaly**

Xóa order cuối cùng của user:

```sql
-- Xóa order cuối cùng của user 101
DELETE FROM orders WHERE id = 1000 AND user_id = 101;
-- ❌ Mất thông tin user 101 (nếu không có orders khác)
```

**Hậu quả:**
- Mất thông tin user
- Không thể contact user
- Business loss

### **Investigation**

**Bước 1: Analyze redundancy**

```sql
-- Tính redundancy
SELECT 
  COUNT(DISTINCT user_id) as unique_users,
  COUNT(*) as total_orders,
  COUNT(*) / COUNT(DISTINCT user_id) as redundancy_factor
FROM orders;
```

Kết quả:
- Unique users: 1,000
- Total orders: 10,000
- **Redundancy factor: 10x** (mỗi user xuất hiện 10 lần trung bình)

**Bước 2: Check inconsistencies**

```sql
-- Tìm inconsistencies
SELECT user_id, COUNT(DISTINCT user_email) as email_count
FROM orders
GROUP BY user_id
HAVING COUNT(DISTINCT user_email) > 1;
```

Kết quả: **50 users** có email không nhất quán!

**Root cause:**
1. Vi phạm 3NF: Transitive Dependency
2. Data redundancy: 10x duplication
3. Update không đầy đủ → inconsistency

### **Fix**

**Fix 1: Normalize schema**

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
  created_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**Fix 2: Migrate data**

```sql
-- Extract unique users
INSERT INTO users (user_id, user_name, user_email)
SELECT DISTINCT 
  user_id,
  MAX(user_name) as user_name,  -- Lấy tên mới nhất
  MAX(user_email) as user_email  -- Lấy email mới nhất
FROM old_orders
GROUP BY user_id;

-- Migrate orders (chỉ giữ id, user_id, total_amount, created_at)
INSERT INTO orders (id, user_id, total_amount, created_at)
SELECT id, user_id, total_amount, created_at
FROM old_orders;
```

**Fix 3: Update application code**

```python
# ✅ ĐÚNG: Update user trong users table
def update_user_email(user_id, new_email):
    db.execute(
        "UPDATE users SET user_email = %s WHERE user_id = %s",
        [new_email, user_id]
    )
    # Chỉ update 1 row!

# ✅ ĐÚNG: Query orders với user info
def get_order_with_user(order_id):
    return db.execute("""
        SELECT o.*, u.user_name, u.user_email
        FROM orders o
        JOIN users u ON o.user_id = u.user_id
        WHERE o.id = %s
    """, [order_id])
```

### **Kết quả**

✅ **Normalized schema**: Không có Transitive Dependency
✅ **No redundancy**: user_name, user_email chỉ lưu một lần
✅ **Fast updates**: Update user → chỉ sửa 1 row (từ 5 giây → 0.1 giây)
✅ **Data consistency**: Không có inconsistency
✅ **No delete anomaly**: Xóa order → không mất user info

**Performance:**
- Update: Từ 5 giây → 0.1 giây (chỉ update 1 row)
- Storage: Giảm 10x (từ 10,000 rows → 1,000 users + 10,000 orders)
- Query: Có thể index trên users.email → nhanh hơn

### **Lesson Learned**

1. **LUÔN tuân thủ 3NF** trong OLTP systems
2. **Transitive Dependency gây redundancy**: Tốn storage, chậm updates
3. **Normalize early**: Dễ hơn normalize sau khi có nhiều data
4. **Update anomalies**: Vi phạm 3NF → phải update nhiều chỗ, dễ lỗi
5. **Data integrity**: 3NF đảm bảo dữ liệu nhất quán

---

## 7️⃣ BEST PRACTICES

### **7.1. Quy tắc 3NF**

1. **Đã tuân thủ 2NF**
2. **Không có Transitive Dependency**
3. **Tách columns có Transitive Dependency** thành bảng riêng

### **7.2. Khi nào cần 3NF?**

**Cần khi:**
- ✅ Có Transitive Dependency
- ✅ OLTP systems
- ✅ Frequent updates

**Có thể dừng ở 2NF khi:**
- ✅ Không có Transitive Dependency
- ✅ Data warehouse (có thể denormalize)

### **7.3. Cách sửa vi phạm 3NF**

1. **Xác định Transitive Dependencies**
2. **Tách columns có Transitive Dependency** thành bảng riêng
3. **Tạo Foreign Key** để maintain relationships
4. **Migrate data** từ schema cũ sang schema mới

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **3NF yêu cầu**: Đã tuân thủ 2NF + không có Transitive Dependency
2. **Transitive Dependency**: Non-key column phụ thuộc vào non-key column khác
3. **Cách sửa**: Tách columns có Transitive Dependency thành bảng riêng
4. **Khi nào cần 3NF**: OLTP systems, có Transitive Dependency
5. **Khi nào dừng ở 2NF**: Không có Transitive Dependency, data warehouse

### **Best Practices**

✅ **Luôn tuân thủ 3NF** trong OLTP systems
✅ **Tách Transitive Dependencies** thành bảng riêng
✅ **Tạo Foreign Keys** để maintain relationships
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Consider denormalization** cho data warehouse nếu cần performance

### **Câu hỏi tự kiểm tra**

1. 3NF là gì? Yêu cầu gì?
2. Transitive Dependency là gì? Cách nhận biết?
3. Tại sao cần 3NF?
4. Khi nào có thể dừng ở 2NF?
5. Cách sửa vi phạm 3NF?

---







**Chuẩn bị cho [Day-008: Data-Types-Storage](../Day-008-Data-Types-Storage/theory.md)** 🚀
