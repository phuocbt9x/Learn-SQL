# Day-010: Bài Tập - Logical vs Physical Design

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Logical và Physical Design. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Logical vs Physical Design

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Logical design là gì?
- Physical design là gì?
- Sự khác biệt giữa logical và physical design?

---

### Câu 1.2: ERD và SQL

**Câu hỏi:**

a) ERD (Entity Relationship Diagram) thuộc logical hay physical design?

b) CREATE TABLE statements thuộc logical hay physical design?

c) Indexes thuộc logical hay physical design?

d) Tại sao cần cả logical và physical design?

---

### Câu 1.3: Gap giữa Logical và Physical

**Câu hỏi:**

a) Gap giữa logical và physical design là gì?

b) Ví dụ cụ thể về gap?

c) Làm thế nào bridge gap này?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: ERD đẹp nhưng Physical Design sai

**Tình huống:**

Logical design (ERD) rất đẹp:
- Users, Orders, Products
- Relationships rõ ràng

Nhưng physical design sai:

```sql
CREATE TABLE users (
  id VARCHAR(255) PRIMARY KEY,  -- ❌ SAI
  email VARCHAR(1000),          -- ❌ SAI
  name VARCHAR(1000)            -- ❌ SAI
);
-- ❌ KHÔNG có indexes!
```

**Câu hỏi:**

a) Phân tích các vấn đề với physical design này.

b) Viết lại physical design đúng.

c) Làm thế nào đảm bảo physical design tốt?

---

### Câu 2.2: Logical Design thiếu thông tin

**Tình huống:**

Logical design chỉ có:
- Users (entity)
- Orders (entity)
- Relationship: User has many Orders

**Câu hỏi:**

a) Logical design này thiếu gì?

b) Làm thế nào chuyển sang physical design?

c) Cần thêm thông tin gì để physical design tốt?

---

### Câu 2.3: Physical Design không match Logical Design

**Tình huống:**

Logical design: User has many Orders (one-to-many)

Physical design:

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  order_ids VARCHAR(500)  -- "1,2,3,4,5"  -- ❌ SAI
);
```

**Câu hỏi:**

a) Physical design này có match logical design không?

b) Vấn đề gì với cách này?

c) Viết lại physical design đúng.

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Chuyển từ Logical sang Physical

**Logical Design (ERD):**

```
Users
- id
- email
- name
- phone

Products
- id
- name
- price
- category

Orders
- id
- user_id (relationship to Users)
- total_amount
- created_at

Order Items
- order_id (relationship to Orders)
- product_id (relationship to Products)
- quantity
```

**Yêu cầu:**

a) Viết physical design (CREATE TABLE statements).

b) Thêm indexes cho performance.

c) Giải thích tại sao chọn mỗi data type và index.

---

### Câu 3.2: E-commerce System

**Yêu cầu:** Thiết kế cả logical và physical cho e-commerce:

**Logical Design:**
- Users, Products, Categories, Orders, Order Items
- Relationships: User has many Orders, Product belongs to Category, Order has many Order Items

**Câu hỏi:**

a) Vẽ ERD (mô tả bằng text).

b) Viết physical design (CREATE TABLE statements).

c) Thêm indexes và giải thích.

---

### Câu 3.3: Blog System

**Yêu cầu:** Thiết kế cả logical và physical cho blog:

**Logical Design:**
- Users (authors), Posts, Tags, Comments
- Relationships: User has many Posts, Post has many Tags, Post has many Comments

**Câu hỏi:**

a) Vẽ ERD (mô tả bằng text).

b) Viết physical design.

c) Thêm indexes cho queries thường dùng.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Logical Design vs Physical Design

**Tình huống:**

Bạn có 2 options:

**Option A: Chỉ có Logical Design (ERD)**
- ERD đẹp, relationships rõ ràng
- Không có physical design

**Option B: Có cả Logical và Physical Design**
- ERD + CREATE TABLE statements
- Indexes, partitions

**Câu hỏi:**

a) So sánh 2 options về:
   - Implementation
   - Performance
   - Maintainability
   - Production readiness

b) Option nào tốt hơn? Tại sao?

c) Có thể chỉ có physical design không có logical design không?

---

### Câu 4.2: Physical Design và Performance

**Tình huống:**

Logical design giống nhau, nhưng có 2 physical designs:

**Physical Design A:**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100)
);
-- Không có indexes
```

**Physical Design B:**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100)
);
CREATE INDEX idx_users_email ON users(email);
```

**Câu hỏi:**

a) Performance khác nhau như thế nào?

b) Tại sao physical design quan trọng cho performance?

c) Logical design có ảnh hưởng đến performance không?

---

### Câu 4.3: Iterative Design

**Câu hỏi:**

a) Có thể bắt đầu với physical design không có logical design không?

b) Khi nào nên update logical design? Khi nào nên update physical design?

c) Làm thế nào maintain cả logical và physical design?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Vẽ ERD

**Yêu cầu:** Vẽ ERD (mô tả bằng text) cho hệ thống quản lý thư viện:

- Books, Authors, Members, Loans
- Relationships: Book has many Authors, Member has many Loans, Loan has many Books

---

### Câu 5.2: Chuyển ERD sang SQL

**ERD:**

```
Projects
- id
- name

Users
- id
- name

Project Members
- project_id (relationship to Projects)
- user_id (relationship to Users)
- role
```

**Yêu cầu:**

a) Viết CREATE TABLE statements.

b) Thêm indexes.

c) Thêm Foreign Keys.

---

### Câu 5.3: Review Physical Design

**Tình huống:**

Physical design hiện tại:

```sql
CREATE TABLE orders (
  id VARCHAR(255) PRIMARY KEY,
  user_id VARCHAR(255),
  total_amount VARCHAR(100),
  created_at VARCHAR(100)
);
```

**Yêu cầu:**

a) Review và tìm các vấn đề.

b) Viết lại physical design tốt hơn.

c) Giải thích các cải thiện.

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Logical design là gì? Physical design là gì?

2. Gap giữa logical và physical là gì?

3. Làm thế nào chuyển từ logical sang physical?

4. Tại sao cần cả logical và physical design?

5. Physical design ảnh hưởng đến performance như thế nào?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý dự án**:

**Yêu cầu:**

a) Vẽ ERD (logical design).

b) Viết physical design (CREATE TABLE statements).

c) Thêm indexes và giải thích.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: ERD Tools

**Câu hỏi:**

a) ERD tools phổ biến là gì? (draw.io, Lucidchart, etc.)

b) ERD có thể generate SQL tự động không?

c) Trade-offs của việc dùng ERD tools?

---

### Câu A.2: Database Migration

**Câu hỏi:**

a) Database migration tools (Flyway, Liquibase) thuộc logical hay physical?

b) Làm thế nào maintain cả logical và physical design trong migration?

c) Best practices cho database migrations?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Logical design và physical design đều quan trọng
- Senior SQL Engineer hiểu cả 2 levels và biết bridge gap

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

