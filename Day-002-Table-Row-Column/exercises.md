# Day-002: Bài Tập - Table, Row, Column

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Table, Row, Column, Data Types và NULL. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Table, Row, Column

**Câu hỏi:** Hãy giải thích ngắn gọn sự khác biệt giữa Table, Row và Column. Cho ví dụ cụ thể.

---

### Câu 1.2: Data Types

**Câu hỏi:** Trong các tình huống sau, nên dùng data type nào? Giải thích tại sao.

a) Lưu **user ID** (có thể có hàng triệu users)

b) Lưu **email address** (ví dụ: "user@example.com")

c) Lưu **giá sản phẩm** (ví dụ: 99.99 USD)

d) Lưu **ngày sinh** (chỉ cần ngày, không cần giờ)

e) Lưu **thời gian tạo order** (cần biết chính xác giờ phút giây)

f) Lưu **số điện thoại** (ví dụ: "+84123456789")

g) Lưu **trạng thái active/inactive** của user

---

### Câu 1.3: NULL

**Câu hỏi:** Giải thích sự khác biệt giữa các giá trị sau:

- `NULL`
- `0` (số không)
- `''` (chuỗi rỗng)
- `false` (boolean)

Cho ví dụ cụ thể khi nào dùng giá trị nào.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Chọn sai Data Type

**Tình huống:**

Developer mới tạo table `products` như sau:

```sql
CREATE TABLE products (
  id VARCHAR(100),
  name VARCHAR(1000),
  price VARCHAR(100),
  stock_quantity VARCHAR(100),
  created_at VARCHAR(100)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với table này.

b) Viết lại CREATE TABLE với data types đúng.

c) Giải thích tại sao chọn data types đó.

---

### Câu 2.2: NULL trong WHERE clause

**Tình hỏi:** Tại sao query sau không trả về kết quả gì?

```sql
SELECT * FROM users WHERE age = NULL;
```

**Câu hỏi:**

a) Giải thích tại sao query không hoạt động.

b) Viết lại query đúng để tìm users có age = NULL.

c) Viết query tìm users có age KHÔNG phải NULL.

---

### Câu 2.3: NULL trong Aggregate Functions

**Tình huống:**

Table `orders` có một số orders có `total_amount = NULL`:

```sql
CREATE TABLE orders (
  id INT,
  user_id INT,
  total_amount DECIMAL(10, 2),  -- Có thể NULL
  status VARCHAR(20)
);
```

**Câu hỏi:**

a) Query sau trả về gì nếu có NULL?

```sql
SELECT SUM(total_amount) FROM orders WHERE status = 'completed';
```

b) Làm thế nào để đảm bảo query luôn trả về số (không phải NULL)?

c) So sánh kết quả của các query sau:

```sql
-- Query 1
SELECT COUNT(*) FROM orders;

-- Query 2
SELECT COUNT(total_amount) FROM orders;

-- Query 3
SELECT COUNT(DISTINCT total_amount) FROM orders;
```

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: Thiết kế Table cho E-commerce

**Yêu cầu:** Thiết kế table `products` cho hệ thống e-commerce với các thông tin:

- Product ID (số nguyên, unique)
- Product name (tên sản phẩm)
- Description (mô tả, có thể dài)
- Price (giá, cần chính xác)
- Stock quantity (số lượng tồn kho)
- Category (danh mục)
- Created date (ngày tạo)
- Is active (có đang bán không)

**Câu hỏi:**

a) Viết CREATE TABLE với data types phù hợp.

b) Giải thích tại sao chọn mỗi data type.

c) Có columns nào nên là NOT NULL? Tại sao?

---

### Câu 3.2: Thiết kế Table cho Blog Posts

**Yêu cầu:** Thiết kế table `posts` cho blog với:

- Post ID
- Title (tiêu đề)
- Content (nội dung, có thể rất dài)
- Author ID
- Published date (ngày đăng, cần biết giờ phút)
- View count (số lượt xem)
- Is published (đã publish chưa)
- Tags (mảng tags, ví dụ: ["sql", "database"])

**Câu hỏi:**

a) Viết CREATE TABLE. (Lưu ý: Tags có thể lưu dạng TEXT hoặc JSON, tùy database)

b) Có nên lưu tags trong cùng table không? Tại sao?

c) Nếu cần query "tất cả posts có tag 'sql'", cách nào tốt hơn?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: VARCHAR Size - Trade-offs

**Tình huống:**

Bạn đang thiết kế table `users` và cần quyết định size cho column `email`.

**Options:**

A) `email VARCHAR(50)`
B) `email VARCHAR(100)`
C) `email VARCHAR(255)`
D) `email VARCHAR(1000)`

**Câu hỏi:**

a) Phân tích trade-offs của mỗi option.

b) Bạn sẽ chọn option nào? Tại sao?

c) Có cách nào để không cần giới hạn size không? (Ví dụ: TEXT type)

---

### Câu 4.2: NULL vs Default Value

**Tình huống:**

Table `users` có column `phone`. Có 2 cách thiết kế:

**Option A: Cho phép NULL**
```sql
phone VARCHAR(20)  -- Có thể NULL
```

**Option B: Dùng Default Value**
```sql
phone VARCHAR(20) DEFAULT ''  -- Mặc định là chuỗi rỗng
```

**Câu hỏi:**

a) So sánh 2 cách trên về:
   - Storage
   - Query performance
   - Business logic clarity
   - Application code complexity

b) Bạn sẽ chọn cách nào? Tại sao?

c) Trong tình huống nào nên dùng NULL? Trong tình huống nào nên dùng default value?

---

### Câu 4.3: DATE vs TIMESTAMP

**Tình huống:**

Bạn đang thiết kế table `events` để lưu các sự kiện.

**Câu hỏi:**

a) Khi nào nên dùng DATE? Khi nào nên dùng TIMESTAMP?

b) Nếu event là "Birthday party on 2024-05-15" → dùng DATE hay TIMESTAMP?

c) Nếu event là "Meeting at 2024-05-15 14:30:00" → dùng DATE hay TIMESTAMP?

d) Nếu app là global (users ở nhiều timezone) → có cần TIMESTAMPTZ không?

---

### Câu 4.4: DECIMAL vs FLOAT cho Tiền

**Tình huống:**

Bạn đang thiết kế table `payments` để lưu số tiền.

**Options:**

A) `amount DECIMAL(10, 2)`
B) `amount FLOAT`

**Câu hỏi:**

a) Tính toán với mỗi type:

```sql
-- Với DECIMAL
SELECT 10.50 + 20.30;  -- Kết quả?

-- Với FLOAT
SELECT 10.50 + 20.30;  -- Kết quả?
```

b) Tại sao FLOAT có thể gây vấn đề với tiền?

c) Khi nào có thể dùng FLOAT? (Không phải tiền)

---

## 🎯 BÀI TẬP 5: THỰC HÀNH VỚI NULL

### Câu 5.1: Xử lý NULL trong Queries

**Table `users`:**

```sql
CREATE TABLE users (
  id INT,
  name VARCHAR(100),
  email VARCHAR(100),
  age INT,              -- Có thể NULL
  phone VARCHAR(20)    -- Có thể NULL
);
```

**Data:**

```
id | name    | email          | age | phone
1  | John    | john@ex.com    | 25  | +1234567890
2  | Jane    | jane@ex.com    | NULL| NULL
3  | Bob     | bob@ex.com     | 30  | NULL
4  | Alice   | alice@ex.com   | NULL| +9876543210
```

**Câu hỏi:**

a) Viết query tìm tất cả users có age = NULL.

b) Viết query tìm tất cả users có age KHÔNG phải NULL.

c) Viết query tính tuổi trung bình (AVG) của users. Kết quả là gì?

d) Viết query đếm số users có phone. Kết quả là gì?

e) Viết query hiển thị phone, nếu NULL thì hiển thị "N/A".

---

### Câu 5.2: COALESCE và CASE WHEN

**Câu hỏi:**

a) Giải thích COALESCE là gì. Cho ví dụ.

b) Viết query sử dụng COALESCE để:
   - Nếu age = NULL → hiển thị 0
   - Nếu phone = NULL → hiển thị "No phone"

c) Viết query tương tự nhưng dùng CASE WHEN thay vì COALESCE.

d) Khi nào nên dùng COALESCE? Khi nào nên dùng CASE WHEN?

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Table, Row, Column khác nhau như thế nào?

2. Khi nào dùng VARCHAR vs CHAR?

3. Khi nào dùng DATE vs TIMESTAMP?

4. Tại sao nên dùng DECIMAL cho tiền thay vì FLOAT?

5. NULL khác với 0, "", false như thế nào?

6. Làm thế nào để check NULL trong WHERE clause?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý thư viện**:

- Books (sách)
- Members (thành viên)
- Loans (mượn sách)

**Yêu cầu:**

a) Thiết kế table `books` với:
   - Book ID
   - Title
   - Author
   - ISBN (mã sách quốc tế)
   - Published year
   - Available (có sẵn không)

b) Thiết kế table `members` với:
   - Member ID
   - Name
   - Email
   - Phone (optional)
   - Join date
   - Is active

c) Thiết kế table `loans` với:
   - Loan ID
   - Book ID
   - Member ID
   - Loan date
   - Return date (có thể NULL nếu chưa trả)
   - Due date (hạn trả)

d) Giải thích tại sao chọn mỗi data type.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: TEXT vs VARCHAR

**Câu hỏi:**

a) Sự khác biệt giữa TEXT và VARCHAR là gì?

b) Khi nào nên dùng TEXT? Khi nào nên dùng VARCHAR?

c) Performance impact của TEXT vs VARCHAR?

d) Có thể index trên TEXT không?

---

### Câu A.2: TIMESTAMPTZ và Timezone

**Câu hỏi:**

a) TIMESTAMPTZ là gì? Khác với TIMESTAMP như thế nào?

b) Tại sao cần TIMESTAMPTZ trong global app?

c) Ví dụ:

```sql
-- Server ở timezone UTC
INSERT INTO events (name, event_time)
VALUES ('Meeting', '2024-01-15 10:00:00+07:00');

-- User ở timezone +07:00 xem → thấy gì?
-- User ở timezone UTC xem → thấy gì?
```

d) Best practices khi làm việc với timezone?

---

### Câu A.3: NULL và Index

**Câu hỏi:**

a) NULL có được index không?

b) Query `WHERE column IS NULL` có dùng index không?

c) Nếu column có nhiều NULL, index có hiệu quả không?

d) Có nên tạo index trên column có nhiều NULL không?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào dùng gì

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

