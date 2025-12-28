# Day-008: Bài Tập - Data Types & Storage

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Data Types và Storage. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: INTEGER Types

**Câu hỏi:**

a) SMALLINT, INT, BIGINT khác nhau như thế nào? (Size, range)

b) Khi nào dùng SMALLINT? Khi nào dùng INT? Khi nào dùng BIGINT?

c) Với 1 triệu rows, chênh lệch storage giữa INT và BIGINT là bao nhiêu?

---

### Câu 1.2: VARCHAR vs CHAR vs TEXT

**Câu hỏi:**

a) VARCHAR, CHAR, TEXT khác nhau như thế nào?

b) Khi nào dùng VARCHAR? Khi nào dùng CHAR? Khi nào dùng TEXT?

c) VARCHAR(100) và VARCHAR(255) - storage có khác nhau không nếu value = "John"?

---

### Câu 1.3: DATE vs TIMESTAMP vs TIMESTAMPTZ

**Câu hỏi:**

a) DATE, TIMESTAMP, TIMESTAMPTZ khác nhau như thế nào?

b) Khi nào dùng DATE? Khi nào dùng TIMESTAMP? Khi nào dùng TIMESTAMPTZ?

c) Tại sao nên dùng TIMESTAMPTZ cho global apps?

---

### Câu 1.4: DECIMAL vs FLOAT

**Câu hỏi:**

a) DECIMAL và FLOAT khác nhau như thế nào? (Precision, storage, performance)

b) Tại sao tiền tệ phải dùng DECIMAL, không được dùng FLOAT?

c) Khi nào có thể dùng FLOAT?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Chọn sai Data Type - ID

**Tình huống:**

Table `users` dùng VARCHAR cho ID:

```sql
CREATE TABLE users (
  id VARCHAR(255) PRIMARY KEY,  -- ❌ SAI
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này (storage, performance, indexing).

b) Viết lại với data type đúng.

c) Nếu có 1 triệu users, chênh lệch storage là bao nhiêu?

---

### Câu 2.2: Chọn sai Data Type - Price

**Tình huống:**

Table `products` dùng VARCHAR cho price:

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price VARCHAR(100)  -- ❌ SAI
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại với data type đúng.

c) Nếu muốn query "tất cả products có price > 100", làm thế nào với schema cũ? Với schema mới?

---

### Câu 2.3: Chọn sai Size - VARCHAR quá lớn

**Tình huống:**

Table `users` dùng VARCHAR(1000) cho email:

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(1000)  -- ❌ SAI: Email chỉ cần ~100 ký tự
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này (storage, index, performance).

b) Viết lại với size đúng.

c) Với 1 triệu users, chênh lệch storage là bao nhiêu?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Products

**Yêu cầu:** Thiết kế schema cho products với data types phù hợp:

- Product ID (có thể có > 1 tỷ products)
- Product name
- Description (có thể rất dài)
- Price (tiền tệ, cần chính xác)
- Stock quantity (0-100,000)
- Category
- Created date (cần biết giờ phút)

**Câu hỏi:**

a) Viết CREATE TABLE với data types phù hợp.

b) Giải thích tại sao chọn mỗi data type.

c) Tính storage cho 1 triệu products (ước tính).

---

### Câu 3.2: Users với Addresses

**Yêu cầu:** Thiết kế schema cho users với addresses:

- User ID
- Name
- Email
- Phone
- Birth date (chỉ cần ngày)
- Created at (cần biết giờ phút, global app)
- Address: street, city, state (2 ký tự), zip (10 ký tự)

**Câu hỏi:**

a) Viết CREATE TABLE với data types phù hợp.

b) Giải thích tại sao chọn mỗi data type.

c) Nếu muốn query "tất cả users ở state 'NY'", data type nào quan trọng?

---

### Câu 3.3: Financial Transactions

**Yêu cầu:** Thiết kế schema cho financial transactions:

- Transaction ID
- Account ID
- Amount (tiền, cần chính xác)
- Transaction type (deposit, withdrawal, transfer)
- Transaction date (cần biết giờ phút, global app)
- Description (có thể dài)

**Câu hỏi:**

a) Viết CREATE TABLE với data types phù hợp.

b) Tại sao amount phải dùng DECIMAL, không được dùng FLOAT?

c) Nếu muốn query "tất cả transactions > $1000", data type nào quan trọng?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Storage và Performance

**Tình huống:**

Bạn có 2 options cho user_id:

**Option A: INT (4 bytes)**
```sql
user_id INT
```

**Option B: BIGINT (8 bytes)**
```sql
user_id BIGINT
```

**Câu hỏi:**

a) Với 1 triệu users, chênh lệch storage là bao nhiêu?

b) Với 1 tỷ users, chênlệch storage là bao nhiêu?

c) Performance impact của INT vs BIGINT? (Index, JOIN, sort)

d) Khi nào nên dùng BIGINT? Khi nào INT đủ?

---

### Câu 4.2: VARCHAR Size và Index

**Tình huống:**

Bạn có 2 options cho email:

**Option A: VARCHAR(100)**
```sql
email VARCHAR(100)
```

**Option B: VARCHAR(1000)**
```sql
email VARCHAR(1000)
```

**Câu hỏi:**

a) Storage có khác nhau không nếu value = "user@example.com"?

b) Index size có khác nhau không?

c) Query performance có khác nhau không?

d) Best practice: Chọn size như thế nào?

---

### Câu 4.3: DECIMAL Precision

**Tình huống:**

Bạn cần lưu giá sản phẩm. Có 2 options:

**Option A: DECIMAL(10, 2)**
```sql
price DECIMAL(10, 2)  -- 99,999,999.99
```

**Option B: DECIMAL(20, 2)**
```sql
price DECIMAL(20, 2)  -- 99,999,999,999,999,999,999.99
```

**Câu hỏi:**

a) Storage có khác nhau không?

b) Performance có khác nhau không?

c) Khi nào cần DECIMAL(20, 2)? Khi nào DECIMAL(10, 2) đủ?

d) Best practice: Chọn precision như thế nào?

---

### Câu 4.4: TIMESTAMP vs TIMESTAMPTZ

**Tình huống:**

Bạn đang thiết kế global app (users ở nhiều timezones).

**Câu hỏi:**

a) Nên dùng TIMESTAMP hay TIMESTAMPTZ? Tại sao?

b) Nếu dùng TIMESTAMP, vấn đề gì có thể xảy ra?

c) Nếu dùng TIMESTAMPTZ, storage và performance có khác không?

d) Best practice cho global apps?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Chọn Data Type

**Câu hỏi:** Trong các tình huống sau, chọn data type phù hợp và giải thích:

a) User ID (có thể có > 1 tỷ users)

b) Product price (tiền tệ, cần chính xác)

c) Product description (có thể rất dài, hàng nghìn ký tự)

d) Country code (luôn 2 ký tự: "US", "VN")

e) Birth date (chỉ cần ngày)

f) Event timestamp (global app, cần timezone)

g) Stock quantity (0-10,000)

h) Temperature (khoa học, không cần chính xác tuyệt đối)

---

### Câu 5.2: Tính Storage

**Tình huống:**

Table `orders` có 1 triệu rows:

```sql
CREATE TABLE orders (
  id INT,                    -- 4 bytes
  user_id INT,               -- 4 bytes
  total_amount DECIMAL(10, 2), -- ~5 bytes
  status VARCHAR(20),        -- ~20 bytes (average)
  created_at TIMESTAMP      -- 8 bytes
);
```

**Câu hỏi:**

a) Tính storage cho 1 row (ước tính).

b) Tính storage cho 1 triệu rows.

c) Nếu đổi `user_id` từ INT → BIGINT, storage tăng bao nhiêu?

d) Nếu đổi `status` từ VARCHAR(20) → VARCHAR(100), storage tăng bao nhiêu? (Giả sử average length = 20)

---

### Câu 5.3: Migrate Data Types

**Tình huống:**

Table `products` hiện tại dùng VARCHAR cho price:

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  price VARCHAR(100)  -- ❌ SAI
);
```

Muốn migrate sang DECIMAL.

**Câu hỏi:**

a) Các bước cần làm để migrate an toàn?

b) Viết script migrate (pseudo-code).

c) Làm thế nào đảm bảo không mất dữ liệu?

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. SMALLINT vs INT vs BIGINT - khi nào dùng gì?

2. VARCHAR vs CHAR vs TEXT - trade-offs?

3. DATE vs TIMESTAMP vs TIMESTAMPTZ - khi nào dùng gì?

4. DECIMAL vs FLOAT - tại sao tiền phải dùng DECIMAL?

5. Storage size ảnh hưởng đến performance như thế nào?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý kho**:

- Products (sản phẩm)
- Warehouses (kho)
- Inventory (tồn kho) - một product có trong nhiều warehouses
- Transactions (giao dịch) - nhập/xuất kho

**Yêu cầu:**

a) Thiết kế schema với data types phù hợp.

b) Giải thích tại sao chọn mỗi data type.

c) Tính storage cho 100,000 products, 10 warehouses, 1 triệu transactions (ước tính).

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Data Type và Compression

**Câu hỏi:**

a) Database compression có ảnh hưởng đến data type choice không?

b) Data type nào compress tốt hơn? (INTEGER vs VARCHAR)

c) Trade-offs của compression?

---

### Câu A.2: Data Type và Partitioning

**Câu hỏi:**

a) Data type có ảnh hưởng đến partitioning không?

b) Partitioning theo TIMESTAMP vs DATE - cái nào tốt hơn?

c) Best practices cho partitioning với different data types?

---

### Câu A.3: Data Type Migration

**Câu hỏi:**

a) Làm thế nào migrate data type an toàn trong production?

b) Zero-downtime migration strategy?

c) Rollback plan nếu migration fail?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết chọn data type phù hợp

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

