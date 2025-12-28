# Day-010: Logical vs Physical Design

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Logical design (ERD, relationships) là gì
- Physical design (tables, indexes, partitions) là gì
- Gap giữa logical và physical design
- Cách chuyển từ logical sang physical design
- Hậu quả nếu chỉ có logical design mà không có physical design tốt

---

## 1️⃣ LOGICAL DESIGN LÀ GÌ?

### **Nó là gì?**

**Logical Design** (Thiết kế logic) là thiết kế database ở **mức khái niệm** (conceptual level), tập trung vào:
- **Entities** (thực thể) và **relationships** (mối quan hệ)
- **Business rules** và **data requirements**
- **ERD** (Entity Relationship Diagram)
- **Không quan tâm** đến implementation details (storage, indexes, performance)

**Ví dụ:**

```
Logical Design:
- Users (entity)
- Orders (entity)
- Relationship: User has many Orders (one-to-many)
- Business rule: Mỗi order phải có user
```

**Đặc điểm:**

1. **Conceptual**: Mô tả "cái gì" (what), không phải "như thế nào" (how)
2. **Business-focused**: Tập trung vào business requirements
3. **Technology-agnostic**: Không phụ thuộc vào database cụ thể
4. **ERD**: Thường được biểu diễn bằng ERD

### **Tại sao tồn tại?**

Logical design tồn tại để:

1. **Hiểu business requirements**: Hiểu rõ entities và relationships
2. **Communication**: Giao tiếp giữa business và technical teams
3. **Documentation**: Tài liệu hóa database structure
4. **Foundation**: Nền tảng cho physical design

### **Khi nào dùng trong production?**

Logical design được dùng khi:

✅ **Planning phase**: Thiết kế database mới
✅ **Requirements gathering**: Thu thập yêu cầu từ business
✅ **Documentation**: Tài liệu hóa database structure
✅ **Communication**: Giải thích database cho stakeholders

**Lưu ý:** Logical design là bước đầu tiên, nhưng **KHÔNG đủ** cho production. Cần physical design để implement.

---

## 2️⃣ PHYSICAL DESIGN LÀ GÌ?

### **Nó là gì?**

**Physical Design** (Thiết kế vật lý) là thiết kế database ở **mức implementation** (implementation level), tập trung vào:
- **Tables** và **columns** với data types cụ thể
- **Indexes** để optimize performance
- **Partitions** để scale
- **Storage** và **performance** optimization

**Ví dụ:**

```sql
-- Physical Design: CREATE TABLE statements
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE,
  name VARCHAR(100),
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);
```

**Đặc điểm:**

1. **Implementation**: Mô tả "như thế nào" (how)
2. **Technology-specific**: Phụ thuộc vào database cụ thể (PostgreSQL, MySQL, etc.)
3. **Performance-focused**: Tối ưu cho performance và storage
4. **SQL**: Được implement bằng SQL (CREATE TABLE, CREATE INDEX, etc.)

### **Tại sao tồn tại?**

Physical design tồn tại để:

1. **Implement database**: Chuyển logical design thành actual database
2. **Optimize performance**: Tạo indexes, partitions để query nhanh
3. **Storage optimization**: Chọn data types, partitions để tiết kiệm storage
4. **Production-ready**: Database sẵn sàng cho production

### **Khi nào dùng trong production?**

Physical design được dùng khi:

✅ **Implementation phase**: Implement database từ logical design
✅ **Performance optimization**: Tối ưu queries và storage
✅ **Production deployment**: Deploy database lên production
✅ **Maintenance**: Update indexes, partitions khi cần

**Lưu ý:** Physical design là bước cuối cùng, **BẮT BUỘC** cho production.

---

## 3️⃣ GAP GIỮA LOGICAL VÀ PHYSICAL

### **3.1. Sự khác biệt**

| Tiêu chí | Logical Design | Physical Design |
|----------|----------------|-----------------|
| **Level** | Conceptual | Implementation |
| **Focus** | Business requirements | Performance, storage |
| **Output** | ERD, relationships | CREATE TABLE, indexes |
| **Technology** | Agnostic | Specific (PostgreSQL, MySQL) |
| **Details** | High-level | Low-level (data types, indexes) |

### **3.2. Gap thường gặp**

**Gap 1: Logical design không có indexes**

**Logical design:**
- ERD có entities và relationships
- Không có indexes

**Physical design:**
- Cần indexes để performance tốt
- Phải thêm indexes vào physical design

**Gap 2: Logical design không có data types**

**Logical design:**
- ERD chỉ có "email", "name"
- Không có data types

**Physical design:**
- Cần data types cụ thể: VARCHAR(100), INT, etc.
- Phải quyết định data types

**Gap 3: Logical design không có partitions**

**Logical design:**
- ERD không có partitions
- Không quan tâm đến scale

**Physical design:**
- Cần partitions để scale
- Phải thêm partitions vào physical design

### **3.3. Cách bridge gap**

**Bước 1: Logical design**
- Tạo ERD với entities và relationships
- Document business rules

**Bước 2: Physical design**
- Chuyển entities thành tables
- Chọn data types phù hợp
- Tạo indexes cho performance
- Thêm partitions nếu cần

**Bước 3: Review và optimize**
- Review physical design
- Optimize indexes và partitions
- Test performance

---

## 4️⃣ CHUYỂN TỪ LOGICAL SANG PHYSICAL

### **4.1. Quy trình**

**Bước 1: Entities → Tables**

```
Logical: Users (entity)
Physical: CREATE TABLE users (...)
```

**Bước 2: Attributes → Columns**

```
Logical: Users có email, name
Physical: email VARCHAR(100), name VARCHAR(100)
```

**Bước 3: Relationships → Foreign Keys**

```
Logical: User has many Orders
Physical: orders.user_id → users.id (Foreign Key)
```

**Bước 4: Add Indexes**

```
Physical: CREATE INDEX idx_orders_user_id ON orders(user_id);
```

**Bước 5: Add Partitions (nếu cần)**

```
Physical: PARTITION BY RANGE (created_at);
```

### **4.2. Ví dụ cụ thể**

**Logical Design (ERD):**

```
Users
- id
- email
- name

Orders
- id
- user_id (relationship to Users)
- total_amount
- created_at

Relationship: User has many Orders (1:N)
```

**Physical Design (SQL):**

```sql
-- Tables
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100) UNIQUE NOT NULL,
  name VARCHAR(100) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT NOT NULL,
  total_amount DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Partitions (nếu cần)
-- CREATE TABLE orders_2024_01 PARTITION OF orders ...
```

---

## 5️⃣ PRODUCTION STORY: ERD ĐẸP NHƯNG PERFORMANCE TỆ DO PHYSICAL DESIGN SAI

### **Context**

Startup e-commerce có logical design rất đẹp:

**ERD:**
- Users, Products, Orders, Order Items
- Relationships rõ ràng
- Business rules đầy đủ

**Nhưng physical design không tốt:**

```sql
-- ❌ Physical design không tốt
CREATE TABLE users (
  id VARCHAR(255) PRIMARY KEY,  -- ❌ SAI: Dùng VARCHAR cho ID
  email VARCHAR(1000),           -- ❌ SAI: Quá lớn
  name VARCHAR(1000)             -- ❌ SAI: Quá lớn
);

CREATE TABLE orders (
  id VARCHAR(255) PRIMARY KEY,  -- ❌ SAI
  user_id VARCHAR(255),          -- ❌ SAI
  total_amount VARCHAR(100),      -- ❌ SAI: Dùng VARCHAR cho số
  created_at VARCHAR(100)        -- ❌ SAI: Dùng VARCHAR cho date
);
-- ❌ KHÔNG có indexes!
```

### **Vấn đề xuất hiện**

**Tháng 1: Queries chậm**

```sql
SELECT * FROM orders WHERE user_id = '12345';
-- Mất 30 giây (không có index, phải convert string)
```

**Tháng 2: Storage bloat**

- VARCHAR(1000) cho mọi column → tốn storage
- 1 triệu users → tốn ~1 GB (nếu dùng đúng types chỉ tốn ~100 MB)

**Tháng 3: Data inconsistency**

- VARCHAR cho số → có thể insert "abc" → không hợp lệ
- VARCHAR cho date → có thể insert "invalid date" → không hợp lệ

### **Investigation**

**Root cause:**
1. Logical design tốt (ERD đẹp)
2. Nhưng physical design sai (sai data types, không có indexes)
3. Gap giữa logical và physical → performance tệ

### **Fix**

**Fix 1: Correct data types**

```sql
-- ✅ ĐÚNG: Dùng đúng data types
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100),
  name VARCHAR(100),
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Fix 2: Add indexes**

```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

**Fix 3: Add partitions (nếu cần)**

```sql
-- Partition orders theo tháng
CREATE TABLE orders_2024_01 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

### **Kết quả**

✅ **Correct data types**: Performance tốt hơn, storage tiết kiệm
✅ **Indexes**: Queries nhanh hơn 1000x
✅ **Data integrity**: Không thể insert invalid data

### **Lesson Learned**

1. **Logical design không đủ**: Cần physical design tốt
2. **Bridge the gap**: Chuyển từ logical sang physical đúng cách
3. **Data types quan trọng**: Chọn đúng data types
4. **Indexes quan trọng**: Tạo indexes cho performance
5. **Review physical design**: Đảm bảo physical design tốt trước khi deploy

---

## 6️⃣ BEST PRACTICES

### **6.1. Quy trình thiết kế**

1. **Logical design**: ERD, relationships, business rules
2. **Physical design**: Tables, indexes, partitions
3. **Review**: Review cả logical và physical
4. **Optimize**: Optimize physical design cho performance
5. **Test**: Test performance trước khi deploy

### **6.2. Logical Design Best Practices**

✅ **Clear entities**: Entities rõ ràng, có ý nghĩa
✅ **Clear relationships**: Relationships rõ ràng
✅ **Document business rules**: Ghi rõ business rules
✅ **ERD**: Dùng ERD để visualize

### **6.3. Physical Design Best Practices**

✅ **Correct data types**: Chọn đúng data types
✅ **Indexes**: Tạo indexes cho performance
✅ **Foreign Keys**: Tạo Foreign Keys cho integrity
✅ **Partitions**: Thêm partitions nếu cần scale
✅ **Test performance**: Test performance trước khi deploy

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Logical design**: Conceptual level, ERD, relationships
2. **Physical design**: Implementation level, tables, indexes
3. **Gap**: Logical design không đủ, cần physical design tốt
4. **Bridge gap**: Chuyển từ logical sang physical đúng cách
5. **Both important**: Cả logical và physical đều quan trọng

### **Best Practices**

✅ **Start with logical**: Bắt đầu với logical design
✅ **Then physical**: Chuyển sang physical design
✅ **Review both**: Review cả logical và physical
✅ **Optimize physical**: Optimize physical design cho performance
✅ **Test**: Test performance trước khi deploy

### **Câu hỏi tự kiểm tra**

1. Logical design là gì? Physical design là gì?
2. Gap giữa logical và physical là gì?
3. Làm thế nào chuyển từ logical sang physical?
4. Tại sao cần cả logical và physical design?
5. Physical design ảnh hưởng đến performance như thế nào?

---

**Chuẩn bị cho Day-011: SQL Execution Flow - High-level** 🚀

