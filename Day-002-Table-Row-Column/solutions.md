# Day-002: Solutions - Table, Row, Column

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Table, Row, Column

**Đáp án:**

**Table (Bảng):**
- Là cấu trúc chứa dữ liệu, giống như một bảng tính Excel
- Có tên (ví dụ: `users`, `orders`)
- Có schema cố định (định nghĩa columns)

**Row (Hàng/Dòng):**
- Là một bản ghi (record) trong table
- Đại diện cho một entity (ví dụ: một user, một order)
- Mỗi row có cùng cấu trúc (cùng columns) nhưng values khác nhau

**Column (Cột):**
- Là một trường (field) trong table
- Định nghĩa loại dữ liệu (data type) và tên
- Ví dụ: `id INT`, `name VARCHAR(100)`

**Ví dụ cụ thể:**

```sql
-- Table: users
-- Columns: id, name, email, age

┌────┬──────────┬─────────────┬─────┐
│ id │   name   │    email    │ age │  ← Columns
├────┼──────────┼─────────────┼─────┤
│  1 │ John Doe │ john@ex.com │  25 │  ← Row 1
│  2 │ Jane Doe │ jane@ex.com │  30 │  ← Row 2
└────┴──────────┴─────────────┴─────┘
```

**Analogy:**
- **Table** = Tờ giấy
- **Row** = Một dòng trên tờ giấy
- **Column** = Một cột trên tờ giấy

---

### Câu 1.2: Data Types

**a) User ID**

**Đáp án: `INT` hoặc `BIGINT`**

**Lý do:**
- ID là số nguyên (không có phần thập phân)
- Nếu có thể có > 2 tỷ users → dùng `BIGINT`
- Nếu chắc chắn < 2 tỷ → dùng `INT` (tiết kiệm storage)

**Ví dụ:**
```sql
id INT PRIMARY KEY  -- Hoặc BIGINT nếu cần scale lớn
```

---

**b) Email address**

**Đáp án: `VARCHAR(100)` hoặc `VARCHAR(255)`**

**Lý do:**
- Email là chuỗi ký tự, độ dài thay đổi
- Email thường < 100 ký tự (RFC 5321: max 254, nhưng thực tế thường < 100)
- `VARCHAR(100)` đủ cho hầu hết emails, tiết kiệm storage hơn `VARCHAR(255)`

**Ví dụ:**
```sql
email VARCHAR(100) UNIQUE
```

**Lưu ý:** Có thể dùng `VARCHAR(255)` nếu muốn an toàn, nhưng `VARCHAR(100)` thường đủ.

---

**c) Giá sản phẩm**

**Đáp án: `DECIMAL(10, 2)`**

**Lý do:**
- Tiền cần chính xác, không được làm tròn
- `DECIMAL(10, 2)` = 10 chữ số tổng cộng, 2 chữ số sau dấu phẩy
- Có thể lưu giá đến 99,999,999.99

**Ví dụ:**
```sql
price DECIMAL(10, 2) NOT NULL
-- Có thể lưu: 99.99, 1000.50, 999999.99
```

**KHÔNG nên dùng FLOAT** vì có thể làm tròn → sai số tiền.

---

**d) Ngày sinh**

**Đáp án: `DATE`**

**Lý do:**
- Chỉ cần ngày, không cần giờ phút giây
- `DATE` đơn giản hơn, tiết kiệm storage hơn `TIMESTAMP`

**Ví dụ:**
```sql
birth_date DATE
-- Lưu: 1990-05-15
```

---

**e) Thời gian tạo order**

**Đáp án: `TIMESTAMP` hoặc `TIMESTAMPTZ`**

**Lý do:**
- Cần biết chính xác giờ phút giây
- Nếu app là global (nhiều timezone) → dùng `TIMESTAMPTZ`
- Nếu chỉ local → dùng `TIMESTAMP`

**Ví dụ:**
```sql
created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
-- Lưu: 2024-01-15 10:30:45+07:00
```

---

**f) Số điện thoại**

**Đáp án: `VARCHAR(20)`**

**Lý do:**
- Số điện thoại là chuỗi (có thể có +, -, spaces)
- Độ dài thay đổi (ví dụ: +84123456789 = 13 ký tự)
- `VARCHAR(20)` đủ cho hầu hết số điện thoại quốc tế

**Ví dụ:**
```sql
phone VARCHAR(20)
-- Có thể lưu: "+84123456789", "0123456789", "+1-234-567-8900"
```

**Lưu ý:** KHÔNG nên dùng INTEGER vì số điện thoại có thể có ký tự đặc biệt (+, -, spaces).

---

**g) Trạng thái active/inactive**

**Đáp án: `BOOLEAN`**

**Lý do:**
- Chỉ có 2 giá trị: true/false
- `BOOLEAN` rõ ràng, dễ hiểu hơn `TINYINT(1)` hoặc `CHAR(1)`

**Ví dụ:**
```sql
is_active BOOLEAN DEFAULT true
```

**Lưu ý:** Một số databases không có BOOLEAN → dùng `TINYINT(1)` (1 = true, 0 = false).

---

### Câu 1.3: NULL

**Sự khác biệt:**

| Giá trị | Ý nghĩa | Ví dụ |
|---------|---------|-------|
| `NULL` | Không có giá trị / Chưa biết | User chưa nhập phone |
| `0` | Số không (có giá trị) | User có 0 orders |
| `''` | Chuỗi rỗng (có giá trị) | User không muốn cung cấp phone (rỗng) |
| `false` | Boolean false (có giá trị) | User không active |

**Ví dụ cụ thể:**

```sql
-- User chưa có phone (chưa biết)
phone = NULL

-- User có phone nhưng không muốn cung cấp (có giá trị rỗng)
phone = ''

-- User có 0 orders (có giá trị = 0)
order_count = 0

-- User không active (có giá trị = false)
is_active = false
```

**Khi nào dùng:**

- **NULL**: Dữ liệu chưa có hoặc không áp dụng
- **0**: Có giá trị, nhưng bằng 0
- **''**: Có giá trị, nhưng rỗng
- **false**: Có giá trị boolean, nhưng là false

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Chọn sai Data Type

**a) Phân tích vấn đề:**

1. **`id VARCHAR(100)`**: 
   - ❌ ID nên là số (INT/BIGINT) → dễ so sánh, index hiệu quả
   - ❌ VARCHAR tốn storage hơn INTEGER

2. **`name VARCHAR(1000)`**:
   - ❌ Quá lớn, name thường < 100 ký tự
   - ❌ Tốn storage, index lớn

3. **`price VARCHAR(100)`**:
   - ❌ Price nên là số (DECIMAL) → có thể tính toán, so sánh
   - ❌ VARCHAR không thể tính toán trực tiếp

4. **`stock_quantity VARCHAR(100)`**:
   - ❌ Quantity nên là số (INT) → có thể tính toán
   - ❌ VARCHAR không thể so sánh số

5. **`created_at VARCHAR(100)`**:
   - ❌ Date/time nên là TIMESTAMP → có thể so sánh, sort
   - ❌ VARCHAR không thể so sánh date

**b) CREATE TABLE đúng:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,                    -- Số nguyên
  name VARCHAR(200) NOT NULL,            -- Đủ cho tên sản phẩm
  price DECIMAL(10, 2) NOT NULL,         -- Số thập phân chính xác
  stock_quantity INT NOT NULL DEFAULT 0,  -- Số nguyên
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP  -- Ngày + giờ
);
```

**c) Giải thích:**

- **`id INT`**: ID là số, dễ so sánh, index hiệu quả
- **`name VARCHAR(200)`**: Đủ cho tên sản phẩm (thường < 100), không cần 1000
- **`price DECIMAL(10, 2)`**: Tiền cần chính xác, không làm tròn
- **`stock_quantity INT`**: Số lượng là số nguyên
- **`created_at TIMESTAMP`**: Thời gian cần so sánh, sort

---

### Câu 2.2: NULL trong WHERE clause

**a) Tại sao không hoạt động:**

**Giải thích:**

`NULL` không bằng bất cứ gì, kể cả `NULL`:

```sql
NULL = NULL  -- Kết quả: NULL (không phải true)
NULL = 5     -- Kết quả: NULL (không phải false)
```

Vì vậy `WHERE age = NULL` không bao giờ match với bất cứ row nào.

**b) Query đúng:**

```sql
-- ✅ ĐÚNG: Dùng IS NULL
SELECT * FROM users WHERE age IS NULL;
```

**c) Query tìm age KHÔNG phải NULL:**

```sql
-- ✅ ĐÚNG: Dùng IS NOT NULL
SELECT * FROM users WHERE age IS NOT NULL;
```

**Lưu ý:** Luôn dùng `IS NULL` và `IS NOT NULL` để check NULL, không dùng `= NULL` hoặc `!= NULL`.

---

### Câu 2.3: NULL trong Aggregate Functions

**a) Query với NULL:**

```sql
SELECT SUM(total_amount) FROM orders WHERE status = 'completed';
```

**Kết quả:**
- Nếu TẤT CẢ `total_amount` đều NULL → trả về `NULL`
- Nếu có ÍT NHẤT một giá trị không NULL → trả về tổng (bỏ qua NULL)

**Ví dụ:**
- 10 orders, tất cả `total_amount = NULL` → `SUM = NULL`
- 10 orders, 5 có `total_amount = 100`, 5 có `NULL` → `SUM = 500` (bỏ qua NULL)

**b) Đảm bảo luôn trả về số:**

```sql
-- ✅ ĐÚNG: Dùng COALESCE
SELECT COALESCE(SUM(total_amount), 0) as total
FROM orders
WHERE status = 'completed';
-- Nếu SUM = NULL → trả về 0
```

**c) So sánh COUNT:**

**Query 1:**
```sql
SELECT COUNT(*) FROM orders;
```
- Đếm TẤT CẢ rows (kể cả NULL)
- Kết quả: Tổng số orders

**Query 2:**
```sql
SELECT COUNT(total_amount) FROM orders;
```
- Đếm rows có `total_amount` KHÔNG phải NULL
- Bỏ qua rows có `total_amount = NULL`
- Kết quả: Số orders có total_amount

**Query 3:**
```sql
SELECT COUNT(DISTINCT total_amount) FROM orders;
```
- Đếm số giá trị DISTINCT của `total_amount` (không NULL)
- Bỏ qua NULL, chỉ đếm giá trị unique
- Kết quả: Số giá trị total_amount khác nhau

**Ví dụ:**

```
orders:
id | total_amount
1  | 100
2  | 200
3  | NULL
4  | 100
5  | NULL
```

- `COUNT(*)` = 5 (tất cả rows)
- `COUNT(total_amount)` = 3 (chỉ rows không NULL)
- `COUNT(DISTINCT total_amount)` = 2 (100 và 200, bỏ qua NULL và duplicate)

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: Table Products

**a) CREATE TABLE:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  description TEXT,                      -- Có thể dài
  price DECIMAL(10, 2) NOT NULL,          -- Tiền cần chính xác
  stock_quantity INT NOT NULL DEFAULT 0,  -- Số lượng
  category VARCHAR(100),                  -- Danh mục
  created_date DATE,                      -- Chỉ cần ngày
  is_active BOOLEAN DEFAULT true          -- Active/inactive
);
```

**b) Giải thích data types:**

- **`id INT`**: ID là số, dễ so sánh, index hiệu quả
- **`name VARCHAR(200)`**: Tên sản phẩm thường < 200 ký tự
- **`description TEXT`**: Mô tả có thể rất dài → dùng TEXT
- **`price DECIMAL(10, 2)`**: Tiền cần chính xác, không làm tròn
- **`stock_quantity INT`**: Số lượng là số nguyên
- **`category VARCHAR(100)`**: Danh mục thường < 100 ký tự
- **`created_date DATE`**: Chỉ cần ngày, không cần giờ
- **`is_active BOOLEAN`**: Chỉ có 2 giá trị: true/false

**c) Columns nên NOT NULL:**

- **`id`**: PRIMARY KEY → tự động NOT NULL
- **`name`**: Tên sản phẩm bắt buộc → NOT NULL
- **`price`**: Giá bắt buộc → NOT NULL
- **`stock_quantity`**: Số lượng bắt buộc → NOT NULL (có thể = 0)

**Có thể NULL:**
- **`description`**: Mô tả có thể không có
- **`category`**: Danh mục có thể chưa phân loại
- **`created_date`**: Có thể dùng DEFAULT CURRENT_DATE

---

### Câu 3.2: Table Posts

**a) CREATE TABLE:**

```sql
CREATE TABLE posts (
  id INT PRIMARY KEY,
  title VARCHAR(300) NOT NULL,           -- Tiêu đề
  content TEXT NOT NULL,                  -- Nội dung có thể rất dài
  author_id INT NOT NULL,                -- Tác giả
  published_at TIMESTAMP,                -- Ngày đăng (cần giờ phút)
  view_count INT DEFAULT 0,              -- Số lượt xem
  is_published BOOLEAN DEFAULT false,     -- Đã publish chưa
  tags TEXT                               -- Tags (lưu dạng JSON hoặc comma-separated)
);
```

**Lưu ý về tags:**
- **PostgreSQL**: Có thể dùng `TEXT[]` (array) hoặc `JSONB`
- **MySQL**: Có thể dùng `TEXT` (lưu JSON string) hoặc `VARCHAR` (comma-separated)
- **Tốt nhất**: Tách thành bảng riêng `post_tags` (sẽ học ở Day sau)

**b) Có nên lưu tags trong cùng table?**

**Đáp án: KHÔNG nên (trong production)**

**Lý do:**

1. **Khó query**: "Tìm tất cả posts có tag 'sql'" → phải dùng LIKE hoặc JSON functions
2. **Khó index**: Không thể index hiệu quả trên tags nếu lưu trong TEXT
3. **Vi phạm normalization**: Tags nên là bảng riêng (many-to-many relationship)

**Cách đúng:**

```sql
-- Table posts
CREATE TABLE posts (
  id INT PRIMARY KEY,
  title VARCHAR(300) NOT NULL,
  content TEXT NOT NULL,
  -- ... other columns
);

-- Table tags
CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE
);

-- Table post_tags (many-to-many)
CREATE TABLE post_tags (
  post_id INT,
  tag_id INT,
  PRIMARY KEY (post_id, tag_id),
  FOREIGN KEY (post_id) REFERENCES posts(id),
  FOREIGN KEY (tag_id) REFERENCES tags(id)
);
```

**c) Query "tất cả posts có tag 'sql'":**

**Cách 1: Tags trong cùng table (TEXT)**

```sql
-- ❌ CHẬM: Phải scan tất cả posts, dùng LIKE
SELECT * FROM posts
WHERE tags LIKE '%sql%';
-- Vấn đề: Có thể match "mysql", "postgresql" (không chính xác)
```

**Cách 2: Tags trong bảng riêng**

```sql
-- ✅ NHANH: Dùng JOIN, có thể index
SELECT p.*
FROM posts p
JOIN post_tags pt ON p.id = pt.post_id
JOIN tags t ON pt.tag_id = t.id
WHERE t.name = 'sql';
-- Có thể index trên tags.name → nhanh hơn nhiều
```

**Kết luận:** Tách tags thành bảng riêng → dễ query, dễ index, đúng normalization.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: VARCHAR Size - Trade-offs

**a) Phân tích trade-offs:**

**Option A: `VARCHAR(50)`**
- ✅ Tiết kiệm storage (chỉ lưu ký tự thực tế)
- ✅ Index nhỏ hơn → nhanh hơn
- ❌ Có thể không đủ cho một số email dài (rất hiếm)

**Option B: `VARCHAR(100)`**
- ✅ Đủ cho hầu hết emails (99.9%)
- ✅ Vẫn tiết kiệm storage (chỉ lưu ký tự thực tế)
- ✅ Index vừa phải
- ❌ Vẫn có thể không đủ cho edge cases (rất hiếm)

**Option C: `VARCHAR(255)`**
- ✅ Chắc chắn đủ cho mọi email (RFC 5321: max 254)
- ✅ An toàn, không lo thiếu
- ❌ Index lớn hơn một chút (nhưng không đáng kể vì chỉ lưu ký tự thực tế)

**Option D: `VARCHAR(1000)`**
- ❌ Quá lớn, không cần thiết
- ❌ Index lớn hơn (overhead)
- ❌ Tốn storage hơn (mặc dù chỉ lưu ký tự thực tế, nhưng metadata lớn hơn)

**b) Chọn option nào?**

**Đáp án: `VARCHAR(100)` hoặc `VARCHAR(255)`**

**Lý do:**
- `VARCHAR(100)` đủ cho 99.9% emails, tiết kiệm hơn
- `VARCHAR(255)` an toàn hơn, không lo edge cases
- Cả 2 đều tốt, tùy vào yêu cầu

**Recommendation:** Dùng `VARCHAR(255)` cho email vì:
- RFC 5321 cho phép email dài đến 254 ký tự
- Storage overhead không đáng kể (VARCHAR chỉ lưu ký tự thực tế)
- An toàn, không lo thiếu

**c) Có cách nào không cần giới hạn size?**

**Đáp án: CÓ - dùng TEXT**

```sql
email TEXT  -- Không giới hạn size
```

**Trade-offs:**

✅ **Ưu điểm:**
- Không lo thiếu size
- Linh hoạt

❌ **Nhược điểm:**
- Không thể index hiệu quả (một số databases không cho index trên TEXT)
- Performance chậm hơn VARCHAR
- Không có length constraint → có thể lưu email quá dài (không hợp lệ)

**Kết luận:** Với email, nên dùng `VARCHAR(255)` thay vì TEXT vì:
- Email có giới hạn (254 ký tự theo RFC)
- Cần index để query nhanh
- VARCHAR(255) đủ và hiệu quả hơn TEXT

---

### Câu 4.2: NULL vs Default Value

**a) So sánh:**

| Tiêu chí | NULL | Default Value |
|----------|------|---------------|
| **Storage** | Tốn 1 byte để đánh dấu NULL | Tốn storage cho giá trị (ví dụ: '' = 1 byte) |
| **Query performance** | Cần check IS NULL (có thể chậm) | So sánh với '' nhanh hơn |
| **Business logic clarity** | Rõ ràng: "chưa có" | Mơ hồ: '' có nghĩa là gì? |
| **Application code** | Phải check NULL | Có thể check empty string |

**b) Chọn cách nào?**

**Đáp án: Tùy vào use case**

**Dùng NULL khi:**
- ✅ Cần phân biệt "chưa có" vs "có nhưng rỗng"
- ✅ Business logic cần biết "chưa set" vs "set rồi nhưng rỗng"
- ✅ Optional field thực sự (có thể không có)

**Dùng Default Value khi:**
- ✅ Field luôn có giá trị (không thể "chưa có")
- ✅ Giá trị mặc định có ý nghĩa rõ ràng
- ✅ Đơn giản hóa application code (không cần check NULL)

**Ví dụ cụ thể:**

```sql
-- ✅ NULL: Phone thực sự optional
phone VARCHAR(20)  -- NULL = chưa có, '' = có nhưng rỗng

-- ✅ Default: Status luôn có giá trị
status VARCHAR(20) DEFAULT 'pending'  -- Luôn có status
```

**c) Tình huống:**

**Dùng NULL:**
- Phone number (có thể không có)
- Middle name (không phải ai cũng có)
- Deleted at (NULL = chưa xóa)

**Dùng Default Value:**
- Status (luôn có: pending, active, inactive)
- Created at (DEFAULT CURRENT_TIMESTAMP)
- Is active (DEFAULT true)

---

### Câu 4.3: DATE vs TIMESTAMP

**a) Khi nào dùng gì?**

**Dùng DATE khi:**
- ✅ Chỉ cần ngày, không cần giờ phút
- ✅ Birth date, hire date, expiration date
- ✅ Events trong ngày (không cần biết giờ)

**Dùng TIMESTAMP khi:**
- ✅ Cần biết chính xác giờ phút giây
- ✅ Created at, updated at, event time
- ✅ Audit trail (biết khi nào thay đổi)

**b) "Birthday party on 2024-05-15"**

**Đáp án: DATE**

**Lý do:**
- Chỉ cần biết ngày, không cần biết giờ
- DATE đơn giản hơn, tiết kiệm storage hơn

```sql
event_date DATE  -- 2024-05-15
```

**c) "Meeting at 2024-05-15 14:30:00"**

**Đáp án: TIMESTAMP**

**Lý do:**
- Cần biết chính xác giờ (14:30:00)
- TIMESTAMP lưu cả ngày và giờ

```sql
event_time TIMESTAMP  -- 2024-05-15 14:30:00
```

**d) Global app với nhiều timezone**

**Đáp án: TIMESTAMPTZ (TIMESTAMP WITH TIME ZONE)**

**Lý do:**
- Users ở nhiều timezone → cần lưu timezone
- TIMESTAMPTZ đảm bảo thời gian đúng dù server ở đâu

**Ví dụ:**

```sql
-- Server ở UTC
INSERT INTO events (name, event_time)
VALUES ('Meeting', '2024-01-15 10:00:00+07:00');

-- User ở +07:00 xem → 10:00:00 (đúng)
-- User ở UTC xem → 03:00:00 (tự động convert)
```

**Best practice:** Với global app, luôn dùng TIMESTAMPTZ.

---

### Câu 4.4: DECIMAL vs FLOAT cho Tiền

**a) Tính toán:**

**Với DECIMAL:**
```sql
SELECT 10.50 + 20.30;
-- Kết quả: 30.80 (chính xác)
```

**Với FLOAT:**
```sql
SELECT 10.50 + 20.30;
-- Kết quả: 30.799999999999997 (làm tròn, không chính xác)
```

**b) Tại sao FLOAT gây vấn đề với tiền?**

**Giải thích:**

FLOAT sử dụng **binary representation** (biểu diễn nhị phân), không thể biểu diễn chính xác một số số thập phân.

**Ví dụ:**
- `0.1` trong binary = `0.0001100110011...` (vô hạn)
- FLOAT chỉ lưu gần đúng → làm tròn
- Với tiền, sai số nhỏ cũng không chấp nhận được

**Hậu quả:**
- Tính tổng tiền → sai số
- So sánh tiền → có thể sai
- **Không thể dùng FLOAT cho tiền!**

**c) Khi nào có thể dùng FLOAT?**

**Dùng FLOAT khi:**
- ✅ Khoa học, tính toán (không cần chính xác tuyệt đối)
- ✅ Đo lường (weight, length) - sai số nhỏ chấp nhận được
- ✅ Statistics, analytics (không cần chính xác từng số)

**KHÔNG dùng FLOAT khi:**
- ❌ Tiền tệ (cần chính xác)
- ❌ Đếm, số lượng (dùng INTEGER)
- ❌ Bất kỳ tính toán nào cần chính xác

---

## 🎯 BÀI TẬP 5: THỰC HÀNH VỚI NULL

### Câu 5.1: Xử lý NULL trong Queries

**a) Tìm users có age = NULL:**

```sql
SELECT * FROM users WHERE age IS NULL;
-- Kết quả: Jane (id=2), Alice (id=4)
```

**b) Tìm users có age KHÔNG phải NULL:**

```sql
SELECT * FROM users WHERE age IS NOT NULL;
-- Kết quả: John (id=1, age=25), Bob (id=3, age=30)
```

**c) Tính tuổi trung bình:**

```sql
SELECT AVG(age) FROM users;
-- AVG bỏ qua NULL, chỉ tính: (25 + 30) / 2 = 27.5
-- Kết quả: 27.5
```

**d) Đếm số users có phone:**

```sql
SELECT COUNT(phone) FROM users;
-- COUNT(phone) chỉ đếm rows có phone không NULL
-- Kết quả: 2 (John và Alice có phone)
```

**e) Hiển thị phone, NULL → "N/A":**

```sql
SELECT 
  name,
  COALESCE(phone, 'N/A') as phone
FROM users;
```

**Kết quả:**
```
name  | phone
John  | +1234567890
Jane  | N/A
Bob   | N/A
Alice | +9876543210
```

---

### Câu 5.2: COALESCE và CASE WHEN

**a) COALESCE là gì?**

**COALESCE** trả về giá trị đầu tiên không NULL.

**Cú pháp:**
```sql
COALESCE(value1, value2, value3, ...)
```

**Ví dụ:**
```sql
COALESCE(NULL, NULL, 'default')  -- Trả về 'default'
COALESCE('value', 'default')     -- Trả về 'value'
COALESCE(NULL, 0)                -- Trả về 0
```

**b) Query với COALESCE:**

```sql
SELECT 
  name,
  COALESCE(age, 0) as age,
  COALESCE(phone, 'No phone') as phone
FROM users;
```

**c) Query tương tự với CASE WHEN:**

```sql
SELECT 
  name,
  CASE 
    WHEN age IS NULL THEN 0 
    ELSE age 
  END as age,
  CASE 
    WHEN phone IS NULL THEN 'No phone'
    ELSE phone
  END as phone
FROM users;
```

**d) Khi nào dùng gì?**

**Dùng COALESCE khi:**
- ✅ Chỉ cần thay thế NULL bằng một giá trị
- ✅ Đơn giản, dễ đọc hơn

**Dùng CASE WHEN khi:**
- ✅ Logic phức tạp hơn (nhiều điều kiện)
- ✅ Cần xử lý nhiều trường hợp

**Ví dụ:**

```sql
-- ✅ COALESCE: Đơn giản
COALESCE(phone, 'N/A')

-- ✅ CASE WHEN: Phức tạp
CASE 
  WHEN phone IS NULL THEN 'No phone'
  WHEN phone = '' THEN 'Empty phone'
  ELSE phone
END
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Table, Row, Column:**
   - Table: Cấu trúc chứa dữ liệu
   - Row: Một bản ghi (record)
   - Column: Một trường (field)

2. **VARCHAR vs CHAR:**
   - VARCHAR: Độ dài thay đổi
   - CHAR: Độ dài cố định

3. **DATE vs TIMESTAMP:**
   - DATE: Chỉ ngày
   - TIMESTAMP: Ngày + giờ

4. **DECIMAL vs FLOAT:**
   - DECIMAL: Chính xác (cho tiền)
   - FLOAT: Gần đúng (cho khoa học)

5. **NULL:**
   - NULL = không có giá trị
   - Khác với 0, '', false

6. **Check NULL:**
   - Dùng `IS NULL`, `IS NOT NULL`
   - Không dùng `= NULL`

---

### Câu 6.2: Hệ thống thư viện

**a) Table books:**

```sql
CREATE TABLE books (
  id INT PRIMARY KEY,
  title VARCHAR(300) NOT NULL,
  author VARCHAR(200) NOT NULL,
  isbn VARCHAR(20) UNIQUE,        -- ISBN có thể có ký tự
  published_year INT,            -- Năm xuất bản
  is_available BOOLEAN DEFAULT true
);
```

**b) Table members:**

```sql
CREATE TABLE members (
  id INT PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  phone VARCHAR(20),            -- Optional
  join_date DATE NOT NULL,
  is_active BOOLEAN DEFAULT true
);
```

**c) Table loans:**

```sql
CREATE TABLE loans (
  id INT PRIMARY KEY,
  book_id INT NOT NULL,
  member_id INT NOT NULL,
  loan_date DATE NOT NULL,
  return_date DATE,             -- NULL nếu chưa trả
  due_date DATE NOT NULL
);
```

**d) Giải thích data types:**

- **ID**: INT (số nguyên, dễ so sánh)
- **Title, Author, Name**: VARCHAR (text có độ dài thay đổi)
- **ISBN**: VARCHAR (có thể có ký tự đặc biệt)
- **Published year**: INT (năm là số nguyên)
- **Dates**: DATE (chỉ cần ngày)
- **Is available/active**: BOOLEAN (true/false)

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: TEXT vs VARCHAR

**a) Sự khác biệt:**

| Type | Size limit | Index | Performance |
|------|------------|-------|-------------|
| **VARCHAR(n)** | Giới hạn n ký tự | Có thể index | Nhanh hơn |
| **TEXT** | Không giới hạn | Khó index (tùy DB) | Chậm hơn |

**b) Khi nào dùng:**

**VARCHAR:**
- ✅ Biết giới hạn size (email, name, title)
- ✅ Cần index để query nhanh
- ✅ Performance quan trọng

**TEXT:**
- ✅ Không biết size (description, content, comments)
- ✅ Có thể rất dài
- ✅ Không cần index (hoặc dùng full-text search)

**c) Performance:**

- **VARCHAR**: Nhanh hơn vì có thể index, size nhỏ hơn
- **TEXT**: Chậm hơn vì khó index, size lớn hơn

**d) Index trên TEXT:**

- **PostgreSQL**: Có thể index TEXT (GIN index cho full-text search)
- **MySQL**: Có thể index TEXT (nhưng chỉ index prefix, không index toàn bộ)
- **SQL Server**: Có thể index TEXT (full-text index)

**Kết luận:** Với text ngắn (< 255 ký tự) → dùng VARCHAR. Với text dài → dùng TEXT.

---

### Câu A.2: TIMESTAMPTZ và Timezone

**a) TIMESTAMPTZ:**

**TIMESTAMPTZ** (TIMESTAMP WITH TIME ZONE) lưu thời gian kèm timezone.

**Khác với TIMESTAMP:**
- **TIMESTAMP**: Chỉ lưu thời gian, không có timezone
- **TIMESTAMPTZ**: Lưu thời gian + timezone

**b) Tại sao cần TIMESTAMPTZ?**

Với global app:
- Server có thể ở timezone khác với users
- Cần đảm bảo thời gian đúng dù ở đâu
- TIMESTAMPTZ tự động convert theo timezone của client

**c) Ví dụ:**

```sql
-- Server ở UTC
INSERT INTO events (name, event_time)
VALUES ('Meeting', '2024-01-15 10:00:00+07:00');
-- Lưu: 2024-01-15 03:00:00 UTC (tự động convert)

-- User ở +07:00 xem
SELECT event_time FROM events;
-- Hiển thị: 2024-01-15 10:00:00+07:00 (convert về timezone của user)

-- User ở UTC xem
SELECT event_time FROM events;
-- Hiển thị: 2024-01-15 03:00:00+00:00 (convert về UTC)
```

**d) Best practices:**

1. **Luôn dùng TIMESTAMPTZ** trong global app
2. **Lưu timezone của user** nếu cần
3. **Convert về UTC** khi lưu (hoặc để database tự convert)
4. **Convert về timezone của user** khi hiển thị

---

### Câu A.3: NULL và Index

**a) NULL có được index không?**

**Đáp án: CÓ, nhưng tùy database**

- **PostgreSQL**: Index bao gồm NULL (có thể query `WHERE column IS NULL` với index)
- **MySQL**: Index bao gồm NULL (nhưng không hiệu quả lắm)
- **SQL Server**: Index bao gồm NULL

**b) Query `WHERE column IS NULL` có dùng index không?**

**Đáp án: CÓ, nhưng không hiệu quả lắm**

- Index có thể dùng, nhưng phải scan nhiều NULL values
- Thường chậm hơn so với query giá trị cụ thể

**c) Nếu column có nhiều NULL, index có hiệu quả không?**

**Đáp án: KHÔNG hiệu quả lắm**

- Index vẫn hoạt động, nhưng:
  - Nhiều NULL → index lớn hơn
  - Query `IS NULL` phải scan nhiều entries
  - Query giá trị cụ thể vẫn nhanh (chỉ scan non-NULL)

**d) Có nên tạo index trên column có nhiều NULL không?**

**Đáp án: TÙY use case**

**Nên index nếu:**
- ✅ Query thường xuyên trên non-NULL values
- ✅ Số lượng non-NULL đủ lớn để index có ý nghĩa

**KHÔNG nên index nếu:**
- ❌ Column có quá nhiều NULL (> 80%)
- ❌ Chỉ query `IS NULL` (không cần index)
- ❌ Column ít được query

**Best practice:** 
- Nếu column có nhiều NULL nhưng vẫn cần query → dùng **partial index** (chỉ index non-NULL values)
- PostgreSQL hỗ trợ: `CREATE INDEX idx_name ON table (column) WHERE column IS NOT NULL;`

---

## 📝 TÓM TẮT

### Key Learnings

1. **Table, Row, Column** là cấu trúc cơ bản của database
2. **Data types** quan trọng: Chọn đúng → performance tốt, data integrity
3. **NULL** là "không có giá trị" - cần xử lý đúng cách
4. **VARCHAR vs CHAR vs TEXT**: Tùy vào use case
5. **DATE vs TIMESTAMP vs TIMESTAMPTZ**: Tùy vào yêu cầu
6. **DECIMAL vs FLOAT**: DECIMAL cho tiền, FLOAT cho khoa học

### Best Practices

✅ **Chọn đúng data type**: INTEGER cho số, VARCHAR cho text, DECIMAL cho tiền
✅ **Chọn đúng size**: VARCHAR(100) đủ cho email, không cần VARCHAR(1000)
✅ **Dùng NOT NULL khi có thể**: Đảm bảo dữ liệu luôn có giá trị
✅ **Xử lý NULL**: Dùng IS NULL, COALESCE, CASE WHEN
✅ **TIMESTAMPTZ cho global app**: Đảm bảo thời gian đúng

---

**Chúc mừng hoàn thành Day-002!** 🎉

**Chuẩn bị cho Day-003: Primary Key - Định danh duy nhất** 🚀

