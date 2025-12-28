# Day-076: DDL - CREATE TABLE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- CREATE TABLE syntax
- Constraints (PRIMARY KEY, FOREIGN KEY, CHECK, UNIQUE, NOT NULL)
- Default values
- Khi nào dùng constraints nào?
- Hậu quả nếu thiếu constraints

---

## 1️⃣ CREATE TABLE LÀ GÌ?

**CREATE TABLE** là câu lệnh DDL (Data Definition Language) để **tạo bảng mới** trong database:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Đặc điểm:**
- Tạo structure của bảng
- Định nghĩa columns và data types
- Định nghĩa constraints
- Không thể rollback (DDL là auto-commit)

---

## 2️⃣ TẠI SAO TỒN TẠI CREATE TABLE?

**CREATE TABLE tồn tại để:**
- **Định nghĩa schema**: Cấu trúc dữ liệu trước khi insert
- **Đảm bảo data integrity**: Constraints ngăn invalid data
- **Performance**: Indexes, data types ảnh hưởng performance
- **Documentation**: Schema là documentation sống

**Nếu không có CREATE TABLE:**
- Không có cấu trúc dữ liệu rõ ràng
- Không có data validation
- Khó maintain và scale

---

## 3️⃣ CONSTRAINTS - CÁC RÀNG BUỘC

### **PRIMARY KEY**

**PRIMARY KEY** là **khóa chính**, đảm bảo:
- Mỗi row có giá trị unique
- Không thể NULL
- Tự động tạo index

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,  -- PRIMARY KEY constraint
  email VARCHAR(255)
);
```

**Khi nào dùng:**
- Mỗi table nên có PRIMARY KEY
- Dùng cho column định danh duy nhất mỗi row

**Hậu quả nếu không dùng:**
- Không có cách định danh row duy nhất
- Khó join, update, delete chính xác
- Performance tệ hơn (không có index tự động)

---

### **FOREIGN KEY**

**FOREIGN KEY** là **khóa ngoại**, đảm bảo:
- Giá trị phải tồn tại trong table khác
- Referential integrity

```sql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id),  -- FOREIGN KEY
  total DECIMAL(10, 2)
);
```

**Khi nào dùng:**
- Khi có quan hệ giữa tables
- Cần đảm bảo referential integrity

**Hậu quả nếu không dùng:**
- Orphan records (orders không có user)
- Data inconsistency
- Khó maintain

---

### **NOT NULL**

**NOT NULL** đảm bảo column **không thể NULL**:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL,  -- NOT NULL constraint
  name VARCHAR(255)  -- Có thể NULL
);
```

**Khi nào dùng:**
- Khi column bắt buộc phải có giá trị
- Business logic yêu cầu

**Hậu quả nếu không dùng:**
- NULL values → logic errors
- Khó query (phải check NULL mọi nơi)

---

### **UNIQUE**

**UNIQUE** đảm bảo giá trị **không trùng lặp**:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE,  -- UNIQUE constraint
  username VARCHAR(50) UNIQUE
);
```

**Khi nào dùng:**
- Khi cần đảm bảo giá trị unique (nhưng không phải PRIMARY KEY)
- Email, username, etc.

**Hậu quả nếu không dùng:**
- Duplicate values → data inconsistency
- Logic errors

---

### **CHECK**

**CHECK** đảm bảo giá trị **thỏa mãn điều kiện**:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  price DECIMAL(10, 2) CHECK (price > 0),  -- CHECK constraint
  stock INTEGER CHECK (stock >= 0)
);
```

**Khi nào dùng:**
- Khi cần validate business rules
- Range checks, format checks

**Hậu quả nếu không dùng:**
- Invalid data → business logic errors
- Phải validate ở application layer

---

### **DEFAULT**

**DEFAULT** đặt **giá trị mặc định** khi không specify:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  -- DEFAULT value
  status VARCHAR(20) DEFAULT 'active'
);
```

**Khi nào dùng:**
- Khi có giá trị mặc định hợp lý
- Timestamps, status, etc.

**Hậu quả nếu không dùng:**
- Phải specify giá trị mọi lúc
- Dễ quên → NULL hoặc invalid values

---

## 4️⃣ KHI NÀO DÙNG CREATE TABLE TRONG PRODUCTION?

**Dùng khi:**
- **Tạo table mới**: Feature mới cần table mới
- **Schema migration**: Thay đổi cấu trúc database
- **Partitioning**: Tạo partitioned tables

**Best practices:**
- **Design trước**: Thiết kế schema trước khi code
- **Constraints đầy đủ**: Đảm bảo data integrity
- **Indexes**: Tạo indexes phù hợp
- **Naming conventions**: Consistent naming

---

## 5️⃣ PRODUCTION STORY: THIẾT KẾ TABLE CHO HỆ THỐNG E-COMMERCE

**Context:**
Hệ thống e-commerce cần tables: users, products, orders, order_items.

**Problem:**
- Thiếu constraints → duplicate emails, orphan orders
- Thiếu indexes → queries chậm
- Thiếu timestamps → không biết khi nào tạo/update

**Investigation:**
- Duplicate emails → users không có UNIQUE constraint
- Orphan orders → orders không có FOREIGN KEY
- Slow queries → thiếu indexes

**Root Cause:**
- Thiếu constraints và indexes trong CREATE TABLE

**Fix:**
```sql
-- Users table với đầy đủ constraints
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);

-- Products table
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0),
  stock INTEGER NOT NULL CHECK (stock >= 0),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_products_name ON products(name);

-- Orders table với FOREIGN KEY
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id),
  total DECIMAL(10, 2) NOT NULL CHECK (total >= 0),
  status VARCHAR(20) DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);

-- Order items table
CREATE TABLE order_items (
  id SERIAL PRIMARY KEY,
  order_id INTEGER NOT NULL REFERENCES orders(id),
  product_id INTEGER NOT NULL REFERENCES products(id),
  quantity INTEGER NOT NULL CHECK (quantity > 0),
  price DECIMAL(10, 2) NOT NULL CHECK (price > 0)
);

CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

**Result:**
- Không còn duplicate emails
- Không còn orphan orders
- Queries nhanh hơn nhờ indexes
- Data integrity được đảm bảo

**Lesson Learned:**
- Luôn thiết kế schema với đầy đủ constraints
- Tạo indexes cho foreign keys và columns thường query
- Dùng DEFAULT values cho timestamps và status

---

## 6️⃣ SO SÁNH: CREATE TABLE VỚI VÀ KHÔNG CÓ CONSTRAINTS

**Query A: CREATE TABLE không có constraints**
```sql
CREATE TABLE users (
  id INTEGER,
  email VARCHAR(255),
  name VARCHAR(255)
);
```

**Query B: CREATE TABLE với đầy đủ constraints**
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**So sánh:**

| Aspect | Query A | Query B |
|--------|---------|---------|
| **Data Integrity** | ❌ Không đảm bảo | ✅ Đảm bảo |
| **Performance** | ❌ Không có index | ✅ Có index tự động |
| **Maintainability** | ❌ Khó maintain | ✅ Dễ maintain |
| **Errors** | ❌ Phải validate ở app | ✅ Database reject invalid data |

**Kết luận:**
- Query B tốt hơn cho production
- Constraints đảm bảo data integrity
- Indexes tự động cải thiện performance

---

## 7️⃣ TÓM TẮT

**Key Takeaways:**
1. **CREATE TABLE**: Tạo bảng mới với schema định nghĩa
2. **Constraints**: PRIMARY KEY, FOREIGN KEY, NOT NULL, UNIQUE, CHECK
3. **Default values**: Giá trị mặc định khi không specify
4. **Best practice**: Luôn dùng constraints đầy đủ, tạo indexes phù hợp
5. **Production**: Thiết kế schema trước, đảm bảo data integrity

---




**Chuẩn bị cho [Day-077: DDL-ALTER-TABLE](Day-077-DDL-ALTER-TABLE/theory.md)** 🚀
