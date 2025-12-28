# Day-002: Table, Row, Column - Kiến trúc cơ bản

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Table (Bảng) là gì và cách tổ chức dữ liệu trong table
- Row (Hàng/Dòng) và Column (Cột) là gì
- Các data types cơ bản và khi nào dùng loại nào
- NULL là gì và tại sao NULL quan trọng
- Tác động của NULL đến queries và business logic

---

## 1️⃣ TABLE (BẢNG) LÀ GÌ?

### **Nó là gì?**

**Table** (Bảng) là cấu trúc cơ bản nhất trong RDBMS để lưu trữ dữ liệu. Table giống như một **bảng tính Excel**, nhưng có nhiều tính năng mạnh mẽ hơn.

**Cấu trúc của Table:**

```
┌────┬──────────┬─────────────┬──────────┐
│ id │   name   │    email    │   age    │  ← Columns (Cột)
├────┼──────────┼─────────────┼──────────┤
│  1 │ John Doe │ john@ex.com │   25     │  ← Row (Hàng)
│  2 │ Jane Doe │ jane@ex.com │   30     │  ← Row
│  3 │ Bob Smith│ bob@ex.com  │   28     │  ← Row
└────┴──────────┴─────────────┴──────────┘
```

**Đặc điểm của Table:**

1. **Có tên (Table name)**: Ví dụ: `users`, `orders`, `products`
2. **Có cấu trúc cố định (Schema)**: Định nghĩa các columns và data types
3. **Chứa nhiều rows**: Mỗi row là một record (bản ghi)
4. **Columns định nghĩa trước**: Phải khai báo columns trước khi insert data

### **Tại sao tồn tại?**

Table là cách tổ chức dữ liệu **có cấu trúc** và **dễ quản lý**:

1. **Cấu trúc rõ ràng**: Biết chính xác dữ liệu gì được lưu ở đâu
2. **Dễ query**: SQL được thiết kế để làm việc với tables
3. **Đảm bảo tính nhất quán**: Schema đảm bảo mọi row có cùng cấu trúc
4. **Hiệu quả**: Database có thể optimize storage và queries dựa trên schema

**So sánh với cách lưu trữ khác:**

| Cách lưu trữ | Ưu điểm | Nhược điểm |
|--------------|---------|------------|
| **Table (RDBMS)** | Cấu trúc rõ ràng, dễ query, có constraints | Schema cứng nhắc |
| **JSON file** | Linh hoạt, không cần schema | Khó query, không có validation |
| **CSV file** | Đơn giản, dễ đọc | Không có data types, không có relationships |

### **Khi nào dùng trong production?**

Table phù hợp khi:

✅ **Dữ liệu có cấu trúc rõ ràng**: Users có id, name, email, age - tất cả đều giống nhau
✅ **Cần query phức tạp**: JOIN, aggregate, filter
✅ **Cần data integrity**: Constraints, Foreign Keys
✅ **Cần performance**: Index, query optimization

**KHÔNG nên dùng table** khi:

❌ **Dữ liệu không có cấu trúc cố định**: Mỗi record có fields khác nhau → dùng Document DB
❌ **Dữ liệu là key-value đơn giản**: → dùng Key-Value DB (Redis)

### **Hậu quả nếu không hiểu table đúng cách?**

**Tình huống thực tế:**

Developer mới không hiểu table schema, tạo table với tất cả columns là `VARCHAR(255)`:

```sql
CREATE TABLE users (
  id VARCHAR(255),
  name VARCHAR(255),
  email VARCHAR(255),
  age VARCHAR(255),  -- ❌ SAI: age nên là INTEGER
  created_at VARCHAR(255)  -- ❌ SAI: created_at nên là TIMESTAMP
);
```

**Hậu quả:**

1. **Không có type safety**: Có thể insert `age = "abc"` → không bị lỗi
2. **Query chậm**: Không thể so sánh số (`WHERE age > 25` phải convert string → số)
3. **Không có index hiệu quả**: Index trên VARCHAR không tối ưu cho số
4. **Tốn storage**: VARCHAR(255) tốn nhiều hơn INTEGER

**Kết luận**: Hiểu table schema và data types là nền tảng quan trọng.

---

## 2️⃣ ROW (HÀNG/DÒNG) LÀ GÌ?

### **Nó là gì?**

**Row** (Hàng/Dòng) là một **bản ghi (record)** trong table. Mỗi row đại diện cho một **entity** (thực thể) trong thế giới thực.

**Ví dụ:**

Trong table `users`, mỗi row đại diện cho một user:

```
┌────┬──────────┬─────────────┬─────┐
│ id │   name   │    email    │ age │
├────┼──────────┼─────────────┼─────┤
│  1 │ John Doe │ john@ex.com │  25 │  ← Row 1: User John
│  2 │ Jane Doe │ jane@ex.com │  30 │  ← Row 2: User Jane
│  3 │ Bob Smith│ bob@ex.com  │  28 │  ← Row 3: User Bob
└────┴──────────┴─────────────┴─────┘
```

**Đặc điểm của Row:**

1. **Mỗi row là một entity độc lập**: User John khác với User Jane
2. **Có cùng cấu trúc**: Tất cả rows có cùng columns (nhưng values khác nhau)
3. **Có thể có NULL**: Một số columns có thể là NULL (nếu cho phép)
4. **Có thể có Primary Key**: Để định danh duy nhất row

### **Tại sao tồn tại?**

Row là đơn vị cơ bản để lưu trữ dữ liệu:

1. **Đại diện cho entity**: Mỗi row = một user, một order, một product
2. **Dễ thao tác**: INSERT (thêm row), UPDATE (sửa row), DELETE (xóa row)
3. **Dễ query**: SELECT trả về rows, WHERE filter rows
4. **Atomic operations**: Mỗi row là đơn vị nhỏ nhất cho transaction

### **Khi nào dùng trong production?**

Row được dùng trong **mọi** thao tác với database:

✅ **INSERT**: Thêm row mới (ví dụ: tạo user mới)
✅ **SELECT**: Đọc rows (ví dụ: lấy danh sách users)
✅ **UPDATE**: Sửa row (ví dụ: cập nhật email của user)
✅ **DELETE**: Xóa row (ví dụ: xóa user)

**Lưu ý production:**

- **Row-level locking**: Khi update một row, chỉ row đó bị lock, không lock toàn bộ table
- **Row size**: Một row không nên quá lớn (thường < 8KB) → ảnh hưởng performance
- **Row count**: Table có quá nhiều rows (hàng trăm triệu) → cần partitioning

### **Hậu quả nếu không hiểu row đúng cách?**

**Tình huống: Developer nghĩ "row = object"**

Developer mới nghĩ mỗi row phải chứa TẤT CẢ thông tin liên quan:

```sql
-- ❌ SAI: Cố gắng lưu tất cả trong một row
CREATE TABLE users (
  id INT,
  name VARCHAR(100),
  email VARCHAR(100),
  order_1_id INT,      -- Order đầu tiên
  order_1_date DATE,
  order_2_id INT,      -- Order thứ hai
  order_2_date DATE,
  -- ... 10 orders nữa?
);
```

**Vấn đề:**

1. **Không scale**: User có 11 orders thì sao? Phải thêm columns?
2. **Dữ liệu trùng lặp**: Nhiều users có cùng order pattern → duplicate
3. **Khó query**: "Tìm tất cả orders trong tháng này" → phải check 10 columns
4. **Vi phạm normalization**: Nên tách thành 2 tables: `users` và `orders`

**Cách đúng:**

```sql
-- ✅ ĐÚNG: Tách thành 2 tables
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,  -- Foreign Key
  order_date DATE
);
```

**Kết luận**: Mỗi row nên đại diện cho một entity đơn giản, không cố gắng lưu tất cả trong một row.

---

## 3️⃣ COLUMN (CỘT) LÀ GÌ?

### **Nó là gì?**

**Column** (Cột) là một **trường (field)** trong table, định nghĩa **loại dữ liệu** và **tên** của một attribute.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT,              -- Column: id, type: INTEGER
  name VARCHAR(100),   -- Column: name, type: VARCHAR(100)
  email VARCHAR(100),  -- Column: email, type: VARCHAR(100)
  age INT              -- Column: age, type: INTEGER
);
```

**Đặc điểm của Column:**

1. **Có tên (Column name)**: `id`, `name`, `email`
2. **Có data type**: `INT`, `VARCHAR(100)`, `DATE`
3. **Có thể có constraints**: `NOT NULL`, `UNIQUE`, `PRIMARY KEY`
4. **Có thể có default value**: `DEFAULT 'unknown'`

### **Tại sao tồn tại?**

Column định nghĩa **cấu trúc** và **ràng buộc** của dữ liệu:

1. **Type safety**: Đảm bảo chỉ lưu đúng loại dữ liệu (số, text, date)
2. **Storage optimization**: Database biết cần bao nhiêu bytes để lưu
3. **Query optimization**: Database biết cách index và query hiệu quả
4. **Data validation**: Constraints đảm bảo dữ liệu hợp lệ

### **Khi nào dùng trong production?**

Column được định nghĩa khi **tạo table** (CREATE TABLE):

✅ **Khi thiết kế schema**: Quyết định columns nào cần thiết
✅ **Khi thêm tính năng mới**: ALTER TABLE ADD COLUMN
✅ **Khi refactor**: ALTER TABLE MODIFY COLUMN (đổi type, size)

**Lưu ý production:**

- **Chọn đúng data type**: INTEGER cho số, VARCHAR cho text, DATE cho ngày
- **Chọn đúng size**: VARCHAR(50) đủ cho email, không cần VARCHAR(1000)
- **NOT NULL khi có thể**: Đảm bảo dữ liệu luôn có giá trị
- **Đặt tên rõ ràng**: `user_email` tốt hơn `email1`

### **Hậu quả nếu chọn sai column type?**

**Tình huống 1: Dùng VARCHAR cho số**

```sql
CREATE TABLE products (
  id VARCHAR(100),
  price VARCHAR(100)  -- ❌ SAI
);
```

**Hậu quả:**
- Không thể so sánh số: `WHERE price > 100` phải convert → chậm
- Không thể tính toán: `SUM(price)` phải convert → chậm và dễ lỗi
- Có thể lưu giá trị không hợp lệ: `price = "abc"` → không bị lỗi

**Tình huống 2: Dùng VARCHAR quá lớn**

```sql
CREATE TABLE users (
  email VARCHAR(1000)  -- ❌ SAI: Email chỉ cần ~50 ký tự
);
```

**Hậu quả:**
- Tốn storage: Mỗi row tốn thêm 950 bytes không cần thiết
- Index lớn hơn: Index trên VARCHAR(1000) tốn nhiều memory
- Query chậm hơn: So sánh strings dài hơn → chậm hơn

**Cách đúng:**

```sql
CREATE TABLE products (
  id INT,
  price DECIMAL(10, 2)  -- ✅ ĐÚNG: Số thập phân
);

CREATE TABLE users (
  email VARCHAR(100)  -- ✅ ĐÚNG: Đủ cho email
);
```

---

## 4️⃣ DATA TYPES CƠ BẢN

### **4.1. INTEGER (Số nguyên)**

**Nó là gì?**

INTEGER là kiểu dữ liệu cho **số nguyên** (không có phần thập phân).

**Các loại INTEGER:**

| Type | Size | Range |
|------|------|-------|
| `SMALLINT` | 2 bytes | -32,768 to 32,767 |
| `INT` hoặc `INTEGER` | 4 bytes | -2,147,483,648 to 2,147,483,647 |
| `BIGINT` | 8 bytes | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

**Khi nào dùng:**

✅ **ID**: Primary Key thường dùng INT hoặc BIGINT
✅ **Số lượng**: Quantity, count, age
✅ **Flags**: 0/1 cho boolean (nhưng nên dùng BOOLEAN nếu có)

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,      -- User ID
  age INT,                 -- Tuổi
  login_count INT          -- Số lần đăng nhập
);
```

**Lưu ý production:**

- **Chọn đúng size**: Nếu chỉ cần 0-1000 → dùng SMALLINT tiết kiệm storage
- **BIGINT cho ID**: Nếu có thể có > 2 tỷ records → dùng BIGINT
- **Unsigned**: Một số databases hỗ trợ UNSIGNED (chỉ số dương) → range lớn hơn

---

### **4.2. VARCHAR (Variable Character) - Chuỗi ký tự có độ dài thay đổi**

**Nó là gì?**

VARCHAR là kiểu dữ liệu cho **chuỗi ký tự** có độ dài **thay đổi** (variable length).

**Cú pháp:**

```sql
VARCHAR(n)  -- n là độ dài tối đa
```

**Ví dụ:**

```sql
CREATE TABLE users (
  name VARCHAR(100),    -- Tên, tối đa 100 ký tự
  email VARCHAR(100),   -- Email, tối đa 100 ký tự
  bio VARCHAR(500)      -- Tiểu sử, tối đa 500 ký tự
);
```

**Khi nào dùng:**

✅ **Text có độ dài thay đổi**: Name, email, description
✅ **Không biết chính xác độ dài**: Nhưng biết giới hạn tối đa

**So sánh với CHAR:**

| Type | Độ dài | Storage | Khi nào dùng |
|------|--------|---------|--------------|
| `VARCHAR(n)` | Thay đổi (0 đến n) | Chỉ lưu ký tự thực tế | Text có độ dài thay đổi |
| `CHAR(n)` | Cố định (luôn n) | Luôn lưu n ký tự | Text có độ dài cố định (ví dụ: country code = 2 ký tự) |

**Ví dụ:**

```sql
-- ✅ VARCHAR: Độ dài thay đổi
name VARCHAR(100)  -- "John" = 4 bytes, "Christopher" = 11 bytes

-- ✅ CHAR: Độ dài cố định
country_code CHAR(2)  -- "US" = 2 bytes, "VN" = 2 bytes (luôn 2)
```

**Lưu ý production:**

- **Chọn đúng size**: VARCHAR(50) đủ cho email, không cần VARCHAR(1000)
- **UTF-8 encoding**: Một số ký tự (như emoji) tốn nhiều bytes hơn
- **Index trên VARCHAR**: Có thể chậm hơn index trên số

---

### **4.3. DATE - Ngày tháng**

**Nó là gì?**

DATE là kiểu dữ liệu cho **ngày tháng** (không có giờ phút giây).

**Format:**

```
YYYY-MM-DD
```

**Ví dụ:**

```sql
CREATE TABLE users (
  birth_date DATE,        -- Ngày sinh: 1990-05-15
  hire_date DATE          -- Ngày vào làm: 2024-01-01
);
```

**Khi nào dùng:**

✅ **Chỉ cần ngày**: Birth date, hire date, expiration date
✅ **Không cần giờ phút**: Sự kiện trong ngày

**So sánh với TIMESTAMP:**

| Type | Lưu gì | Khi nào dùng |
|------|--------|--------------|
| `DATE` | Chỉ ngày (YYYY-MM-DD) | Birth date, hire date |
| `TIMESTAMP` | Ngày + giờ (YYYY-MM-DD HH:MM:SS) | Created at, updated at |
| `TIMESTAMPTZ` | Ngày + giờ + timezone | Logs, events với timezone |

**Ví dụ:**

```sql
-- ✅ DATE: Chỉ ngày
birth_date DATE  -- 1990-05-15

-- ✅ TIMESTAMP: Ngày + giờ
created_at TIMESTAMP  -- 2024-01-15 10:30:45

-- ✅ TIMESTAMPTZ: Ngày + giờ + timezone
event_time TIMESTAMPTZ  -- 2024-01-15 10:30:45+07:00
```

**Lưu ý production:**

- **Timezone**: DATE không có timezone, TIMESTAMPTZ có timezone
- **So sánh ngày**: `WHERE birth_date > '1990-01-01'` → đơn giản
- **Format**: Luôn dùng format chuẩn (YYYY-MM-DD), không dùng format tùy ý

---

### **4.4. TIMESTAMP - Ngày tháng giờ phút giây**

**Nó là gì?**

TIMESTAMP là kiểu dữ liệu cho **ngày tháng + giờ phút giây**.

**Format:**

```
YYYY-MM-DD HH:MM:SS
```

**Ví dụ:**

```sql
CREATE TABLE orders (
  id INT,
  created_at TIMESTAMP,    -- 2024-01-15 10:30:45
  updated_at TIMESTAMP     -- 2024-01-15 15:20:10
);
```

**Khi nào dùng:**

✅ **Cần biết thời gian chính xác**: Created at, updated at, event time
✅ **Audit trail**: Biết khi nào record được tạo/sửa

**TIMESTAMP vs TIMESTAMPTZ:**

| Type | Timezone | Khi nào dùng |
|------|----------|--------------|
| `TIMESTAMP` | Không có timezone | Local time, không cần timezone |
| `TIMESTAMPTZ` | Có timezone | Global app, cần timezone |

**Ví dụ:**

```sql
-- ❌ TIMESTAMP: Không có timezone
created_at TIMESTAMP  -- Lưu: 2024-01-15 10:30:45
-- Nếu server ở timezone khác → sai thời gian

-- ✅ TIMESTAMPTZ: Có timezone
created_at TIMESTAMPTZ  -- Lưu: 2024-01-15 10:30:45+07:00
-- Luôn đúng thời gian dù server ở đâu
```

**Lưu ý production:**

- **Nên dùng TIMESTAMPTZ**: Đảm bảo thời gian đúng dù server ở timezone nào
- **Default value**: `DEFAULT CURRENT_TIMESTAMP` cho created_at
- **Auto-update**: `ON UPDATE CURRENT_TIMESTAMP` cho updated_at (MySQL)

---

### **4.5. DECIMAL/NUMERIC - Số thập phân chính xác**

**Nó là gì?**

DECIMAL (hoặc NUMERIC) là kiểu dữ liệu cho **số thập phân chính xác** (không làm tròn).

**Cú pháp:**

```sql
DECIMAL(precision, scale)
-- precision: Tổng số chữ số
-- scale: Số chữ số sau dấu phẩy
```

**Ví dụ:**

```sql
CREATE TABLE products (
  id INT,
  price DECIMAL(10, 2)  -- 10 chữ số tổng cộng, 2 chữ số sau dấu phẩy
  -- Có thể lưu: 12345678.90
  -- Không thể lưu: 123456789.12 (vượt quá 10 chữ số)
);
```

**Khi nào dùng:**

✅ **Tiền tệ**: Price, amount, balance (cần chính xác, không làm tròn)
✅ **Đo lường chính xác**: Weight, length (cần chính xác)

**So sánh với FLOAT:**

| Type | Độ chính xác | Khi nào dùng |
|------|--------------|--------------|
| `DECIMAL` | Chính xác (không làm tròn) | Tiền tệ, số liệu quan trọng |
| `FLOAT` | Gần đúng (có thể làm tròn) | Khoa học, tính toán, không cần chính xác tuyệt đối |

**Ví dụ:**

```sql
-- ✅ DECIMAL: Chính xác
price DECIMAL(10, 2)  -- 10.50 + 20.30 = 30.80 (chính xác)

-- ❌ FLOAT: Có thể làm tròn
price FLOAT  -- 10.50 + 20.30 có thể = 30.799999 (làm tròn)
```

**Lưu ý production:**

- **Luôn dùng DECIMAL cho tiền**: FLOAT có thể làm tròn → sai số tiền
- **Chọn đúng precision**: DECIMAL(10, 2) đủ cho giá đến 99,999,999.99
- **Performance**: DECIMAL chậm hơn FLOAT một chút, nhưng đảm bảo chính xác

---

### **4.6. BOOLEAN - Giá trị đúng/sai**

**Nó là gì?**

BOOLEAN là kiểu dữ liệu cho **giá trị đúng/sai** (true/false).

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT,
  is_active BOOLEAN,        -- true/false
  is_verified BOOLEAN,       -- true/false
  is_premium BOOLEAN         -- true/false
);
```

**Khi nào dùng:**

✅ **Flags**: is_active, is_verified, is_deleted
✅ **Yes/No questions**: Có/không

**Lưu ý:**

- Một số databases không có BOOLEAN → dùng TINYINT(1) hoặc CHAR(1)
- PostgreSQL có BOOLEAN: `true`, `false`, `NULL`
- MySQL có BOOLEAN (alias của TINYINT(1)): `1` = true, `0` = false

---

## 5️⃣ NULL LÀ GÌ? TẠI SAO NULL QUAN TRỌNG?

### **Nó là gì?**

**NULL** là giá trị đặc biệt trong database, có nghĩa là **"không có giá trị"** hoặc **"chưa biết"**.

**NULL ≠ 0, NULL ≠ "", NULL ≠ false**

NULL là **không có gì cả** - không phải số 0, không phải chuỗi rỗng, không phải false.

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT,
  name VARCHAR(100),
  email VARCHAR(100),
  age INT,              -- Có thể NULL
  phone VARCHAR(20)     -- Có thể NULL
);

-- User chưa nhập age và phone
INSERT INTO users (id, name, email, age, phone) 
VALUES (1, 'John', 'john@ex.com', NULL, NULL);
```

**Trong table:**

```
┌────┬──────┬─────────────┬──────┬───────┐
│ id │ name │    email    │ age  │ phone │
├────┼──────┼─────────────┼──────┼───────┤
│  1 │ John │ john@ex.com │ NULL │ NULL  │
└────┴──────┴─────────────┴──────┴───────┘
```

### **Tại sao tồn tại?**

NULL tồn tại vì trong thực tế, **không phải lúc nào cũng có giá trị**:

1. **Dữ liệu chưa có**: User chưa nhập phone number
2. **Dữ liệu không áp dụng**: Middle name (không phải ai cũng có)
3. **Dữ liệu chưa biết**: Age (user chưa cung cấp)
4. **Dữ liệu bị xóa**: Deleted_at = NULL nghĩa là chưa bị xóa

**So sánh với các giá trị khác:**

| Giá trị | Ý nghĩa | Khi nào dùng |
|---------|---------|--------------|
| `NULL` | Không có giá trị / Chưa biết | Dữ liệu chưa có hoặc không áp dụng |
| `0` | Số không | Có giá trị, nhưng bằng 0 |
| `''` (empty string) | Chuỗi rỗng | Có giá trị, nhưng rỗng |
| `false` | Sai | Có giá trị boolean, nhưng là false |

**Ví dụ:**

```sql
-- User chưa có phone
phone = NULL  -- Chưa biết, chưa có

-- User có phone nhưng không muốn cung cấp
phone = ''  -- Có giá trị (rỗng), nhưng không phải NULL

-- User có 0 orders
order_count = 0  -- Có giá trị (0), không phải NULL
```

### **Khi nào dùng trong production?**

NULL được dùng khi:

✅ **Optional fields**: Phone, middle_name, bio (không bắt buộc)
✅ **Chưa có giá trị**: created_at có thể NULL nếu chưa tạo
✅ **Soft delete**: deleted_at = NULL nghĩa là chưa bị xóa
✅ **Default chưa set**: Nếu không có DEFAULT, column có thể NULL (nếu cho phép)

**KHÔNG nên dùng NULL khi:**

❌ **Required fields**: Primary Key, Foreign Key (thường không được NULL)
❌ **Business logic quan trọng**: Nếu NULL gây confusion → dùng default value
❌ **Performance-critical**: NULL có thể làm chậm queries (cần check IS NULL)

**Best practices:**

1. **Dùng NOT NULL khi có thể**: Đảm bảo dữ liệu luôn có giá trị
2. **Dùng DEFAULT thay vì NULL**: `DEFAULT ''` cho string, `DEFAULT 0` cho số
3. **Document NULL meaning**: Ghi rõ NULL có nghĩa là gì (chưa có? không áp dụng?)

### **Hậu quả nếu không xử lý NULL đúng cách?**

**Tình huống thực tế: Lỗi do NULL trong hệ thống thanh toán**

**Context:**

Hệ thống thanh toán có table `payments`:

```sql
CREATE TABLE payments (
  id INT PRIMARY KEY,
  user_id INT,
  amount DECIMAL(10, 2),
  status VARCHAR(20),
  processed_at TIMESTAMP
);
```

**Vấn đề:**

Developer viết query tính tổng tiền đã thanh toán:

```sql
-- ❌ SAI: Không xử lý NULL
SELECT SUM(amount) as total_paid
FROM payments
WHERE status = 'completed';
```

**Tình huống xảy ra:**

1. Một số payments có `amount = NULL` (do bug khi insert)
2. Query chạy và trả về `total_paid = NULL` (vì SUM với NULL → NULL)
3. Application code không check NULL → hiển thị "NULL" hoặc crash
4. **Hậu quả**: User thấy "NULL" thay vì số tiền, gây confusion

**Cách fix:**

```sql
-- ✅ ĐÚNG: Xử lý NULL
SELECT COALESCE(SUM(amount), 0) as total_paid
FROM payments
WHERE status = 'completed';
-- COALESCE trả về 0 nếu SUM = NULL
```

**Hoặc tốt hơn: Đảm bảo amount không được NULL**

```sql
-- ✅ TỐT HƠN: Constraint đảm bảo amount không NULL
CREATE TABLE payments (
  id INT PRIMARY KEY,
  user_id INT,
  amount DECIMAL(10, 2) NOT NULL,  -- Không được NULL
  status VARCHAR(20),
  processed_at TIMESTAMP
);
```

**Các lỗi NULL thường gặp:**

1. **NULL trong WHERE clause:**

```sql
-- ❌ SAI: NULL không bằng bất cứ gì (kể cả NULL)
SELECT * FROM users WHERE age = NULL;  -- Không trả về gì cả!

-- ✅ ĐÚNG: Phải dùng IS NULL
SELECT * FROM users WHERE age IS NULL;
```

2. **NULL trong aggregate:**

```sql
-- SUM, AVG, COUNT bỏ qua NULL
SELECT SUM(age) FROM users;  -- Nếu có NULL → bỏ qua, chỉ tính số không NULL
SELECT AVG(age) FROM users;  -- Chỉ tính trung bình của số không NULL
SELECT COUNT(age) FROM users;  -- Chỉ đếm rows có age không NULL
SELECT COUNT(*) FROM users;  -- Đếm TẤT CẢ rows (kể cả NULL)
```

3. **NULL trong JOIN:**

```sql
-- NULL không match với bất cứ gì
SELECT * FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.user_id = NULL;  -- ❌ SAI!

-- ✅ ĐÚNG
WHERE o.user_id IS NULL;  -- Tìm users không có orders
```

**Lesson learned:**

- **Luôn xử lý NULL**: Dùng COALESCE, IS NULL, NOT NULL constraint
- **Test với NULL**: Đảm bảo queries hoạt động đúng khi có NULL
- **Document NULL meaning**: Ghi rõ NULL có nghĩa là gì trong business logic

---

## 6️⃣ PRODUCTION STORY: LỖI DO NULL GÂY RA TRONG HỆ THỐNG THANH TOÁN

### **Context**

Startup fintech có hệ thống thanh toán. Table `transactions` lưu các giao dịch:

```sql
CREATE TABLE transactions (
  id INT PRIMARY KEY,
  user_id INT,
  amount DECIMAL(10, 2),
  fee DECIMAL(10, 2),        -- Phí giao dịch
  net_amount DECIMAL(10, 2),  -- Số tiền thực nhận (amount - fee)
  status VARCHAR(20),
  created_at TIMESTAMP
);
```

### **Vấn đề xuất hiện**

**Ngày 1: Bug khi insert**

Developer mới viết code insert transaction, nhưng quên set `fee`:

```sql
INSERT INTO transactions (user_id, amount, status)
VALUES (123, 100.00, 'pending');
-- fee và net_amount = NULL (do không set)
```

**Ngày 2: Query tính toán sai**

Query tính tổng tiền thực nhận:

```sql
-- ❌ SAI: Không xử lý NULL
SELECT SUM(net_amount) as total_net
FROM transactions
WHERE status = 'completed';
```

Kết quả: `total_net = NULL` (vì có NULL trong SUM)

**Ngày 3: Application crash**

Application code:

```python
total = result['total_net']  # = None (NULL)
display_amount = total * 1.1  # None * 1.1 → TypeError!
```

**Hậu quả:**
- Application crash khi hiển thị dashboard
- Users không thể xem số tiền
- Support nhận nhiều complaints

### **Investigation**

**Bước 1: Check data**

```sql
SELECT COUNT(*) as total,
       COUNT(net_amount) as with_net,
       COUNT(*) - COUNT(net_amount) as null_count
FROM transactions
WHERE status = 'completed';
```

Kết quả:
- `total = 10,000`
- `with_net = 9,950`
- `null_count = 50` (có 50 transactions có net_amount = NULL)

**Bước 2: Tìm root cause**

```sql
SELECT * FROM transactions
WHERE net_amount IS NULL
LIMIT 10;
```

Phát hiện: Tất cả đều có `fee = NULL` → `net_amount` không được tính (do code không set)

**Root cause:**
1. Code insert không set `fee` → `fee = NULL`
2. Code không tính `net_amount = amount - fee` → `net_amount = NULL`
3. Query không xử lý NULL → `SUM(net_amount) = NULL`
4. Application không check NULL → crash

### **Fix**

**Fix 1: Update data hiện tại**

```sql
-- Fix transactions có NULL
UPDATE transactions
SET fee = 0,
    net_amount = amount
WHERE fee IS NULL;
```

**Fix 2: Thêm constraints**

```sql
ALTER TABLE transactions
ALTER COLUMN fee SET DEFAULT 0,
ALTER COLUMN fee SET NOT NULL,
ALTER COLUMN net_amount SET NOT NULL;
```

**Fix 3: Fix code insert**

```sql
-- ✅ ĐÚNG: Luôn set fee và net_amount
INSERT INTO transactions (user_id, amount, fee, net_amount, status)
VALUES (123, 100.00, 2.50, 97.50, 'pending');
-- Hoặc dùng DEFAULT và tính toán
```

**Fix 4: Fix query**

```sql
-- ✅ ĐÚNG: Xử lý NULL
SELECT COALESCE(SUM(net_amount), 0) as total_net
FROM transactions
WHERE status = 'completed';
```

### **Kết quả**

✅ **Data fixed**: Tất cả transactions đã có fee và net_amount
✅ **Constraints added**: Đảm bảo không có NULL trong tương lai
✅ **Query fixed**: Luôn trả về số (0 nếu không có data)
✅ **Application fixed**: Check NULL trước khi tính toán

### **Lesson Learned**

1. **Luôn dùng NOT NULL cho required fields**: Đảm bảo dữ liệu luôn có giá trị
2. **Xử lý NULL trong queries**: Dùng COALESCE, IS NULL, CASE WHEN
3. **Test với NULL**: Đảm bảo queries hoạt động đúng khi có NULL
4. **Default values**: Dùng DEFAULT thay vì để NULL khi có thể
5. **Document NULL meaning**: Ghi rõ NULL có nghĩa là gì trong business logic

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Table** là cấu trúc cơ bản để lưu trữ dữ liệu có cấu trúc
2. **Row** là một bản ghi (record), đại diện cho một entity
3. **Column** là một trường (field), định nghĩa loại dữ liệu
4. **Data types** quan trọng: Chọn đúng type → performance tốt, data integrity
5. **NULL** là "không có giá trị" - khác với 0, "", false
6. **Xử lý NULL đúng cách**: Dùng IS NULL, COALESCE, NOT NULL constraint

### **Best Practices**

✅ **Chọn đúng data type**: INTEGER cho số, VARCHAR cho text, DECIMAL cho tiền
✅ **Chọn đúng size**: VARCHAR(50) đủ cho email, không cần VARCHAR(1000)
✅ **Dùng NOT NULL khi có thể**: Đảm bảo dữ liệu luôn có giá trị
✅ **Xử lý NULL trong queries**: COALESCE, IS NULL, CASE WHEN
✅ **Document NULL meaning**: Ghi rõ NULL có nghĩa là gì

### **Câu hỏi tự kiểm tra**

1. Table, Row, Column khác nhau như thế nào?
2. Khi nào dùng VARCHAR vs CHAR?
3. Khi nào dùng DATE vs TIMESTAMP?
4. Tại sao nên dùng DECIMAL cho tiền thay vì FLOAT?
5. NULL khác với 0, "", false như thế nào?
6. Làm thế nào để xử lý NULL trong queries?

---

**Chuẩn bị cho Day-003: Primary Key - Định danh duy nhất** 🚀

