# Day-008: Solutions - Data Types & Storage

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: INTEGER Types

**a) Sự khác biệt:**

| Type | Size | Signed Range | Unsigned Range |
|------|------|--------------|----------------|
| **SMALLINT** | 2 bytes | -32,768 to 32,767 | 0 to 65,535 |
| **INT** | 4 bytes | ±2 tỷ | 0 to 4 tỷ |
| **BIGINT** | 8 bytes | ±9 tỷ tỷ | 0 to 18 tỷ tỷ |

**b) Khi nào dùng:**

**SMALLINT:**
- ✅ Giá trị nhỏ, có giới hạn rõ ràng (age, quantity, status codes)

**INT:**
- ✅ Default choice, ID columns, counters

**BIGINT:**
- ✅ Large-scale systems (> 2 tỷ records), timestamps (milliseconds)

**c) Chênh lệch storage:**

**1 triệu rows:**
- INT: 1M × 4 bytes = 4 MB
- BIGINT: 1M × 8 bytes = 8 MB
- **Chênh lệch: 4 MB** (không đáng kể)

**1 tỷ rows:**
- INT: 1B × 4 bytes = 4 GB
- BIGINT: 1B × 8 bytes = 8 GB
- **Chênh lệch: 4 GB** (đáng kể!)

---

### Câu 1.2: VARCHAR vs CHAR vs TEXT

**a) Sự khác biệt:**

| Type | Size | Storage | Index |
|------|------|---------|-------|
| **VARCHAR(n)** | Variable (0-n) | Chỉ lưu ký tự thực tế | ✅ Có thể |
| **CHAR(n)** | Fixed (n) | Luôn n bytes | ✅ Có thể |
| **TEXT** | Variable (rất lớn) | Chỉ lưu ký tự thực tế | ❌ Khó index |

**b) Khi nào dùng:**

**VARCHAR:** Text có độ dài thay đổi (default choice)

**CHAR:** Text có độ dài cố định (country code, state code)

**TEXT:** Text rất dài (description, content)

**c) Storage với value = "John":**

**VARCHAR(100):**
- Storage: ~5 bytes (4 ký tự + 1-2 bytes overhead)

**VARCHAR(255):**
- Storage: ~5 bytes (4 ký tự + 1-2 bytes overhead)

**Kết luận:** Storage **KHÔNG khác nhau** (chỉ lưu ký tự thực tế). Nhưng **metadata và index có thể khác nhau**.

---

### Câu 1.3: DATE vs TIMESTAMP vs TIMESTAMPTZ

**a) Sự khác biệt:**

| Type | Lưu gì | Timezone | Storage |
|------|--------|----------|---------|
| **DATE** | Chỉ ngày | Không | 3-4 bytes |
| **TIMESTAMP** | Ngày + giờ | Không | 8 bytes |
| **TIMESTAMPTZ** | Ngày + giờ + TZ | Có | 8 bytes |

**b) Khi nào dùng:**

**DATE:** Chỉ cần ngày (birth date, hire date)

**TIMESTAMP:** Cần giờ, single timezone

**TIMESTAMPTZ:** Cần giờ, global app (best practice)

**c) Tại sao TIMESTAMPTZ cho global apps?**

**Lý do:**
- Users ở nhiều timezones → cần timezone
- TIMESTAMPTZ tự động convert → đảm bảo thời gian đúng
- TIMESTAMP không có timezone → có thể sai thời gian

---

### Câu 1.4: DECIMAL vs FLOAT

**a) Sự khác biệt:**

| Type | Precision | Storage | Performance |
|------|-----------|---------|-------------|
| **DECIMAL** | Chính xác | Variable | Chậm hơn |
| **FLOAT** | Gần đúng | 4 bytes | Nhanh hơn |

**b) Tại sao tiền phải dùng DECIMAL?**

**Lý do:**
- FLOAT sử dụng binary representation → không thể biểu diễn chính xác một số số thập phân
- Ví dụ: `10.50 + 20.30 = 30.799999999999997` (FLOAT) vs `30.80` (DECIMAL)
- Tiền cần chính xác → không được làm tròn

**c) Khi nào có thể dùng FLOAT?**

**Khi:**
- ✅ Khoa học, tính toán (không cần chính xác tuyệt đối)
- ✅ Statistics, analytics (sai số nhỏ chấp nhận được)
- ❌ KHÔNG dùng cho tiền tệ

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Chọn sai Data Type - ID

**a) Phân tích vấn đề:**

1. **Storage**: VARCHAR(255) tốn nhiều hơn INT (255 bytes vs 4 bytes)
2. **Performance**: String comparison chậm hơn integer comparison
3. **Index**: String index lớn hơn, chậm hơn integer index
4. **Validation**: Có thể insert invalid IDs (không phải số)

**b) Schema đúng:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,  -- ✅ ĐÚNG
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**c) Chênh lệch storage:**

**1 triệu users:**
- VARCHAR(255): ~255 bytes/row × 1M = ~255 MB (nếu đầy)
- INT: 4 bytes/row × 1M = 4 MB
- **Chênh lệch: ~250 MB** (rất đáng kể!)

---

### Câu 2.2: Chọn sai Data Type - Price

**a) Phân tích vấn đề:**

1. **Không thể tính toán**: Phải convert string → số
2. **Query chậm**: `WHERE CAST(price AS DECIMAL) > 100` → phải convert mọi row
3. **Index không hiệu quả**: String index không phù hợp cho số
4. **Data validation**: Có thể insert invalid prices ("abc")

**b) Schema đúng:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2)  -- ✅ ĐÚNG
);
```

**c) Query:**

**Schema cũ:**
```sql
-- ❌ CHẬM: Phải convert
SELECT * FROM products 
WHERE CAST(price AS DECIMAL) > 100;
```

**Schema mới:**
```sql
-- ✅ NHANH: Không cần convert, có index
SELECT * FROM products 
WHERE price > 100;
```

---

### Câu 2.3: Chọn sai Size - VARCHAR quá lớn

**a) Phân tích vấn đề:**

1. **Storage metadata**: VARCHAR(1000) có metadata lớn hơn VARCHAR(100)
2. **Index size**: Index trên VARCHAR(1000) lớn hơn (ngay cả khi values nhỏ)
3. **Query performance**: String comparison trên VARCHAR(1000) có thể chậm hơn
4. **Memory**: Load vào memory tốn hơn

**b) Schema đúng:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(100)  -- ✅ ĐÚNG: Email chỉ cần ~100 ký tự
);
```

**c) Chênh lệch storage:**

**1 triệu users (email average = 30 ký tự):**
- VARCHAR(1000): ~30 bytes/row + metadata overhead = ~35 bytes/row × 1M = ~35 MB
- VARCHAR(100): ~30 bytes/row + metadata overhead = ~32 bytes/row × 1M = ~32 MB
- **Chênh lệch: ~3 MB** (không đáng kể cho storage, nhưng index có thể khác)

**Index size:**
- VARCHAR(1000): Index có thể lớn hơn (metadata)
- VARCHAR(100): Index nhỏ hơn

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: E-commerce Products

**a) CREATE TABLE:**

```sql
CREATE TABLE products (
  id BIGINT PRIMARY KEY,              -- > 1 tỷ products
  name VARCHAR(200),
  description TEXT,                    -- Có thể rất dài
  price DECIMAL(10, 2),                -- Tiền, cần chính xác
  stock_quantity INT,                   -- 0-100,000 → INT đủ
  category VARCHAR(100),
  created_at TIMESTAMPTZ                -- Global app, cần timezone
);
```

**b) Giải thích:**

- **`id BIGINT`**: > 1 tỷ products → cần BIGINT
- **`name VARCHAR(200)`**: Tên sản phẩm thường < 200 ký tự
- **`description TEXT`**: Có thể rất dài → TEXT
- **`price DECIMAL(10, 2)`**: Tiền cần chính xác → DECIMAL
- **`stock_quantity INT`**: 0-100,000 → INT đủ (không cần BIGINT)
- **`category VARCHAR(100)`**: Category thường < 100 ký tự
- **`created_at TIMESTAMPTZ`**: Global app → cần timezone

**c) Storage ước tính (1 triệu products):**

- `id`: 1M × 8 bytes = 8 MB
- `name`: 1M × 50 bytes (average) = 50 MB
- `description`: 1M × 200 bytes (average) = 200 MB
- `price`: 1M × 5 bytes = 5 MB
- `stock_quantity`: 1M × 4 bytes = 4 MB
- `category`: 1M × 20 bytes (average) = 20 MB
- `created_at`: 1M × 8 bytes = 8 MB
- **Tổng: ~295 MB** (ước tính)

---

### Câu 3.2: Users với Addresses

**a) CREATE TABLE:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100),
  phone VARCHAR(20),
  birth_date DATE,                     -- Chỉ cần ngày
  created_at TIMESTAMPTZ,              -- Global app
  street VARCHAR(200),
  city VARCHAR(100),
  state CHAR(2),                       -- Luôn 2 ký tự
  zip CHAR(10)                         -- Có thể có format cố định
);
```

**b) Giải thích:**

- **`birth_date DATE`**: Chỉ cần ngày → DATE
- **`created_at TIMESTAMPTZ`**: Global app → cần timezone
- **`state CHAR(2)`**: Luôn 2 ký tự → CHAR
- **`zip CHAR(10)`**: Có thể có format cố định → CHAR

**c) Query "tất cả users ở state 'NY'":**

```sql
SELECT * FROM users WHERE state = 'NY';
```

**Data type quan trọng:**
- **`state CHAR(2)`**: Index trên CHAR(2) hiệu quả hơn VARCHAR
- **Exact match**: CHAR đảm bảo format đúng

---

### Câu 3.3: Financial Transactions

**a) CREATE TABLE:**

```sql
CREATE TABLE transactions (
  id BIGINT PRIMARY KEY,
  account_id BIGINT,
  amount DECIMAL(15, 2),               -- Tiền lớn, cần chính xác
  transaction_type VARCHAR(20),         -- 'deposit', 'withdrawal', 'transfer'
  transaction_date TIMESTAMPTZ,         -- Global app
  description TEXT                     -- Có thể dài
);
```

**b) Tại sao amount phải dùng DECIMAL?**

**Lý do:**
- FLOAT có thể làm tròn → sai số tiền
- Ví dụ: `1000.50 + 2000.30 = 3000.7999999999997` (FLOAT) vs `3000.80` (DECIMAL)
- Financial transactions cần chính xác tuyệt đối → DECIMAL

**c) Query "tất cả transactions > $1000":**

```sql
SELECT * FROM transactions WHERE amount > 1000.00;
```

**Data type quan trọng:**
- **`amount DECIMAL(15, 2)`**: Có thể index, query nhanh, chính xác

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Storage và Performance

**a) Chênh lệch storage (1 triệu users):**

- INT: 1M × 4 bytes = 4 MB
- BIGINT: 1M × 8 bytes = 8 MB
- **Chênh lệch: 4 MB**

**b) Chênh lệch storage (1 tỷ users):**

- INT: 1B × 4 bytes = 4 GB
- BIGINT: 1B × 8 bytes = 8 GB
- **Chênh lệch: 4 GB** (đáng kể!)

**c) Performance impact:**

**Index:**
- INT: Index nhỏ hơn → nhanh hơn
- BIGINT: Index lớn hơn → chậm hơn một chút

**JOIN:**
- INT: JOIN nhanh hơn (integer comparison)
- BIGINT: JOIN chậm hơn một chút (nhưng không đáng kể)

**Sort:**
- INT: Sort nhanh hơn
- BIGINT: Sort chậm hơn một chút

**d) Khi nào dùng BIGINT?**

**Dùng BIGINT khi:**
- ✅ Chắc chắn > 2 tỷ records
- ✅ Cần scale lớn
- ✅ Timestamps (milliseconds)

**Dùng INT khi:**
- ✅ Chắc chắn < 2 tỷ records
- ✅ Default choice

---

### Câu 4.2: VARCHAR Size và Index

**a) Storage có khác nhau không?**

**Đáp án: KHÔNG (cho actual data)**

- VARCHAR(100): "user@example.com" → ~20 bytes
- VARCHAR(1000): "user@example.com" → ~20 bytes
- Storage thực tế **KHÔNG khác nhau** (chỉ lưu ký tự thực tế)

**b) Index size có khác nhau không?**

**Đáp án: CÓ (một chút)**

- VARCHAR(100): Index có metadata nhỏ hơn
- VARCHAR(1000): Index có metadata lớn hơn (nhưng không đáng kể nếu values nhỏ)

**c) Query performance có khác nhau không?**

**Đáp án: CÓ (một chút)**

- VARCHAR(100): String comparison nhanh hơn một chút
- VARCHAR(1000): String comparison có thể chậm hơn (nhưng không đáng kể nếu values nhỏ)

**d) Best practice:**

**Chọn size đúng:**
- Email: VARCHAR(100) đủ (RFC 5321: max 254, nhưng thực tế thường < 100)
- Name: VARCHAR(100) đủ
- Description: VARCHAR(500) hoặc TEXT

**Không dùng quá lớn:**
- ❌ VARCHAR(1000) cho email → không cần thiết
- ✅ VARCHAR(100) đủ → tiết kiệm metadata, index nhỏ hơn

---

### Câu 4.3: DECIMAL Precision

**a) Storage có khác nhau không?**

**Đáp án: CÓ**

- DECIMAL(10, 2): ~5 bytes (tùy value)
- DECIMAL(20, 2): ~9 bytes (tùy value)
- DECIMAL(20, 2) tốn storage hơn

**b) Performance có khác nhau không?**

**Đáp án: CÓ (một chút)**

- DECIMAL(10, 2): Tính toán nhanh hơn một chút
- DECIMAL(20, 2): Tính toán chậm hơn một chút (nhưng không đáng kể)

**c) Khi nào cần DECIMAL(20, 2)?**

**Cần khi:**
- ✅ Giá trị rất lớn (> 99,999,999.99)
- ✅ Financial systems với số tiền rất lớn

**DECIMAL(10, 2) đủ khi:**
- ✅ Giá trị < 99,999,999.99
- ✅ Hầu hết e-commerce applications

**d) Best practice:**

**Chọn precision đúng:**
- E-commerce: DECIMAL(10, 2) đủ (99,999,999.99)
- Financial: Có thể cần DECIMAL(15, 2) hoặc DECIMAL(20, 2)
- **Không dùng quá lớn**: DECIMAL(20, 2) tốn storage và chậm hơn nếu không cần

---

### Câu 4.4: TIMESTAMP vs TIMESTAMPTZ

**a) Nên dùng TIMESTAMPTZ**

**Lý do:**
- Global app → users ở nhiều timezones
- TIMESTAMPTZ tự động convert → đảm bảo thời gian đúng
- TIMESTAMP không có timezone → có thể sai thời gian

**b) Vấn đề với TIMESTAMP:**

**Vấn đề:**
- Server ở UTC, user ở +07:00
- Insert: `'2024-01-15 10:00:00'` (local time?)
- User xem: Thấy thời gian sai (không biết timezone)

**c) Storage và performance:**

**Storage:**
- TIMESTAMP: 8 bytes
- TIMESTAMPTZ: 8 bytes
- **KHÔNG khác nhau** (lưu UTC internally)

**Performance:**
- TIMESTAMP: Tương đương
- TIMESTAMPTZ: Tương đương (convert tự động, rất nhanh)

**d) Best practice:**

**Luôn dùng TIMESTAMPTZ cho global apps:**
- ✅ Đảm bảo thời gian đúng
- ✅ Không tốn storage/performance thêm
- ✅ Tránh timezone bugs

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Chọn Data Type

**a) User ID (> 1 tỷ users):**

**Đáp án: BIGINT**

**Lý do:** > 1 tỷ → cần BIGINT (INT chỉ đến 2 tỷ)

---

**b) Product price (tiền tệ):**

**Đáp án: DECIMAL(10, 2)**

**Lý do:** Tiền cần chính xác → DECIMAL, không được FLOAT

---

**c) Product description (rất dài):**

**Đáp án: TEXT**

**Lý do:** Có thể rất dài → TEXT, không dùng VARCHAR

---

**d) Country code (luôn 2 ký tự):**

**Đáp án: CHAR(2)**

**Lý do:** Độ dài cố định → CHAR, không dùng VARCHAR

---

**e) Birth date (chỉ ngày):**

**Đáp án: DATE**

**Lý do:** Chỉ cần ngày → DATE, không cần TIMESTAMP

---

**f) Event timestamp (global app):**

**Đáp án: TIMESTAMPTZ**

**Lý do:** Global app → cần timezone → TIMESTAMPTZ

---

**g) Stock quantity (0-10,000):**

**Đáp án: SMALLINT hoặc INT**

**Lý do:** 0-10,000 → SMALLINT đủ (0-32,767), hoặc INT (an toàn hơn)

---

**h) Temperature (khoa học):**

**Đáp án: FLOAT hoặc DOUBLE**

**Lý do:** Khoa học, không cần chính xác tuyệt đối → FLOAT/DOUBLE OK

---

### Câu 5.2: Tính Storage

**a) Storage cho 1 row (ước tính):**

- `id`: 4 bytes
- `user_id`: 4 bytes
- `total_amount`: ~5 bytes (DECIMAL)
- `status`: ~20 bytes (VARCHAR average)
- `created_at`: 8 bytes
- **Tổng: ~41 bytes/row**

**b) Storage cho 1 triệu rows:**

- 1M × 41 bytes = **~41 MB**

**c) Nếu đổi `user_id` từ INT → BIGINT:**

- Tăng: 1M × 4 bytes = **4 MB**

**d) Nếu đổi `status` từ VARCHAR(20) → VARCHAR(100):**

- Storage thực tế: **KHÔNG tăng** (nếu average length = 20)
- Index metadata: Có thể tăng một chút (không đáng kể)

---

### Câu 5.3: Migrate Data Types

**a) Các bước:**

1. **Backup data**: Backup table trước khi migrate
2. **Validate data**: Đảm bảo tất cả prices là số hợp lệ
3. **Create new column**: Thêm column mới với type đúng
4. **Migrate data**: Copy và convert data
5. **Verify**: So sánh data cũ và mới
6. **Switch**: Đổi tên columns
7. **Drop old column**: Xóa column cũ

**b) Script migrate (pseudo-code):**

```sql
-- Bước 1: Backup
CREATE TABLE products_backup AS SELECT * FROM products;

-- Bước 2: Validate
SELECT COUNT(*) FROM products WHERE price !~ '^[0-9]+\.?[0-9]*$';
-- Nếu > 0 → có invalid data, phải fix trước

-- Bước 3: Add new column
ALTER TABLE products ADD COLUMN price_new DECIMAL(10, 2);

-- Bước 4: Migrate
UPDATE products 
SET price_new = CAST(price AS DECIMAL(10, 2))
WHERE price ~ '^[0-9]+\.?[0-9]*$';

-- Bước 5: Verify
SELECT COUNT(*) FROM products WHERE price_new IS NULL;
-- Nếu > 0 → có data không migrate được

-- Bước 6: Switch
ALTER TABLE products DROP COLUMN price;
ALTER TABLE products RENAME COLUMN price_new TO price;

-- Bước 7: Cleanup
DROP TABLE products_backup;
```

**c) Đảm bảo không mất dữ liệu:**

1. **Backup trước**: Tạo backup table
2. **Validate data**: Đảm bảo tất cả data hợp lệ
3. **Test trên staging**: Test migration trên staging environment trước
4. **Verify sau migrate**: So sánh row count, sample data
5. **Keep backup**: Giữ backup trong vài ngày/tuần

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **INTEGER types:**
   - SMALLINT: Nhỏ (2 bytes)
   - INT: Default (4 bytes)
   - BIGINT: Lớn (8 bytes)

2. **Text types:**
   - VARCHAR: Variable (default)
   - CHAR: Fixed
   - TEXT: Rất dài

3. **Date/Time:**
   - DATE: Chỉ ngày
   - TIMESTAMP: Local time
   - TIMESTAMPTZ: Global (best practice)

4. **DECIMAL vs FLOAT:**
   - DECIMAL: Chính xác (cho tiền)
   - FLOAT: Gần đúng (cho khoa học)

5. **Storage & Performance:**
   - Data type lớn → storage lớn → performance chậm hơn
   - Chọn đúng size → tiết kiệm storage, tăng performance

---

### Câu 6.2: Hệ thống quản lý kho

**a) Schema với data types phù hợp:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  description TEXT
);

CREATE TABLE warehouses (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  location VARCHAR(200)
);

CREATE TABLE inventory (
  product_id INT,
  warehouse_id INT,
  quantity INT,                    -- 0-1,000,000 → INT đủ
  last_updated TIMESTAMPTZ,
  PRIMARY KEY (product_id, warehouse_id),
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (warehouse_id) REFERENCES warehouses(id)
);

CREATE TABLE transactions (
  id BIGINT PRIMARY KEY,           -- Có thể có nhiều transactions
  product_id INT,
  warehouse_id INT,
  transaction_type VARCHAR(20),    -- 'in', 'out'
  quantity INT,
  transaction_date TIMESTAMPTZ,
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (warehouse_id) REFERENCES warehouses(id)
);
```

**b) Giải thích:**

- **`inventory.quantity INT`**: 0-1,000,000 → INT đủ
- **`transactions.id BIGINT`**: Có thể có nhiều transactions → BIGINT
- **`transaction_date TIMESTAMPTZ`**: Global app → cần timezone

**c) Storage ước tính:**

- Products (100K): ~50 MB
- Warehouses (10): ~1 KB
- Inventory (1M): ~20 MB
- Transactions (1M): ~50 MB
- **Tổng: ~120 MB** (ước tính)

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: Data Type và Compression

**a) Compression có ảnh hưởng không?**

**Đáp án: CÓ**

**Lý do:**
- Compression hiệu quả hơn với một số data types
- INTEGER compress tốt hơn VARCHAR
- Duplicate data compress tốt hơn unique data

**b) Data type nào compress tốt hơn?**

**INTEGER:**
- ✅ Compress tốt (repetitive patterns)
- ✅ Ratio cao (có thể 10:1)

**VARCHAR:**
- ❌ Compress kém hơn (ít patterns)
- ❌ Ratio thấp hơn (có thể 2:1)

**c) Trade-offs:**

**Compression:**
- ✅ Tiết kiệm storage
- ❌ CPU overhead (compress/decompress)
- ❌ Query có thể chậm hơn (phải decompress)

---

### Câu A.2: Data Type và Partitioning

**a) Data type có ảnh hưởng không?**

**Đáp án: CÓ**

**Lý do:**
- Partitioning thường dựa trên một column
- Data type ảnh hưởng đến partition key choice

**b) Partitioning theo TIMESTAMP vs DATE:**

**TIMESTAMP:**
- ✅ Granular hơn (có thể partition theo giờ)
- ✅ Phù hợp cho time-series data

**DATE:**
- ✅ Đơn giản hơn (partition theo ngày)
- ✅ Phù hợp cho daily partitions

**c) Best practices:**

- **Time-series data**: Partition theo TIMESTAMP/TIMESTAMPTZ
- **Daily data**: Partition theo DATE
- **Integer ranges**: Partition theo INT ranges

---

### Câu A.3: Data Type Migration

**a) Làm thế nào migrate an toàn?**

**Strategy:**
1. **Backup**: Backup trước
2. **Validate**: Validate data
3. **Test**: Test trên staging
4. **Gradual**: Migrate từng batch (nếu có thể)
5. **Verify**: Verify sau migrate

**b) Zero-downtime migration:**

**Dual-write strategy:**
1. Add new column
2. Write to both old and new columns
3. Migrate historical data
4. Switch reads to new column
5. Stop writing to old column
6. Drop old column

**c) Rollback plan:**

1. **Keep backup**: Giữ backup trong vài ngày
2. **Keep old column**: Không drop ngay
3. **Test rollback**: Test rollback procedure
4. **Document**: Document rollback steps

---

## 📝 TÓM TẮT

### Key Learnings

1. **INTEGER types**: SMALLINT (nhỏ), INT (default), BIGINT (lớn)
2. **Text types**: VARCHAR (variable), CHAR (fixed), TEXT (rất dài)
3. **Date/Time**: DATE (ngày), TIMESTAMP (local), TIMESTAMPTZ (global)
4. **Numeric**: DECIMAL (chính xác), FLOAT/DOUBLE (gần đúng)
5. **Storage & Performance**: Chọn đúng type → tiết kiệm storage, tăng performance

### Best Practices

✅ **Chọn đúng data type**: Không dùng VARCHAR cho mọi cột
✅ **Chọn đúng size**: VARCHAR(100) đủ, không cần VARCHAR(255)
✅ **INTEGER cho ID**: INT/BIGINT nhanh hơn VARCHAR
✅ **DECIMAL cho tiền**: FLOAT có thể làm tròn → sai số
✅ **TIMESTAMPTZ cho global apps**: Đảm bảo timezone đúng

---

**Chúc mừng hoàn thành Day-008!** 🎉

**Chuẩn bị cho Day-009: Index - Cơ bản về chỉ mục** 🚀

