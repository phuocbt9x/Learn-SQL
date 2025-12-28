# Day-008: Data Types & Storage - Hiểu sâu về lưu trữ

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- INTEGER types (SMALLINT, INT, BIGINT) - khi nào dùng gì
- VARCHAR vs CHAR vs TEXT - trade-offs và use cases
- DATE vs TIMESTAMP vs TIMESTAMPTZ - timezone handling
- NUMERIC vs FLOAT vs DOUBLE - precision và accuracy
- Storage size và performance impact của mỗi data type
- Cách chọn data type phù hợp cho production

---

## 1️⃣ INTEGER TYPES - SMALLINT, INT, BIGINT

### **SMALLINT (2 bytes)**

**Nó là gì?**

SMALLINT là kiểu số nguyên nhỏ, chiếm **2 bytes** (16 bits).

**Range:**
- **Signed**: -32,768 to 32,767
- **Unsigned** (nếu hỗ trợ): 0 to 65,535

**Khi nào dùng:**

✅ **Giá trị nhỏ, có giới hạn rõ ràng**:
   - Age (0-150)
   - Quantity (0-1000)
   - Status codes (0-255)
   - Flags (0/1)

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  age SMALLINT,              -- 0-150, đủ dùng
  login_count SMALLINT       -- 0-32767, đủ cho hầu hết cases
);
```

**Lưu ý production:**

- **Tiết kiệm storage**: 2 bytes vs 4 bytes (INT) → tiết kiệm 50%
- **Với 1 triệu rows**: Tiết kiệm 2 MB storage
- **Performance**: Tương đương INT (integer operations nhanh)

---

### **INT hoặc INTEGER (4 bytes)**

**Nó là gì?**

INT là kiểu số nguyên phổ biến nhất, chiếm **4 bytes** (32 bits).

**Range:**
- **Signed**: -2,147,483,648 to 2,147,483,647 (khoảng ±2 tỷ)
- **Unsigned** (nếu hỗ trợ): 0 to 4,294,967,295 (khoảng 4 tỷ)

**Khi nào dùng:**

✅ **ID columns**: Primary Key, Foreign Key (phổ biến nhất)
✅ **Counters**: Số lượng, đếm
✅ **Giá trị không biết chính xác range**: Dùng INT an toàn

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,        -- INT cho ID (phổ biến nhất)
  age INT,                   -- Có thể dùng SMALLINT nếu chắc chắn < 32767
  order_count INT            -- Có thể > 32767
);
```

**Lưu ý production:**

- **Default choice**: INT là lựa chọn mặc định cho hầu hết cases
- **Performance**: Nhanh, index hiệu quả
- **Storage**: 4 bytes - hợp lý cho hầu hết applications

---

### **BIGINT (8 bytes)**

**Nó là gì?**

BIGINT là kiểu số nguyên lớn, chiếm **8 bytes** (64 bits).

**Range:**
- **Signed**: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (khoảng ±9 tỷ tỷ)
- **Unsigned** (nếu hỗ trợ): 0 to 18,446,744,073,709,551,615 (khoảng 18 tỷ tỷ)

**Khi nào dùng:**

✅ **ID columns cho large-scale systems**: Có thể có > 2 tỷ records
✅ **Timestamps**: Unix timestamp (milliseconds)
✅ **Large counters**: Số lượng rất lớn
✅ **UUID numeric representation**: Nếu cần convert UUID sang số

**Ví dụ:**

```sql
CREATE TABLE events (
  id BIGINT PRIMARY KEY,           -- Có thể có > 2 tỷ events
  user_id BIGINT,                  -- Có thể có > 2 tỷ users
  timestamp_ms BIGINT,             -- Unix timestamp milliseconds
  view_count BIGINT                -- Có thể > 2 tỷ views
);
```

**Lưu ý production:**

- **Storage**: 8 bytes vs 4 bytes (INT) → tốn gấp đôi
- **Với 1 triệu rows**: Tốn thêm 4 MB storage
- **Performance**: Tương đương INT (nhưng tốn memory hơn)
- **Chỉ dùng khi cần**: Nếu chắc chắn < 2 tỷ → dùng INT

---

### **So sánh tổng hợp**

| Type | Size | Signed Range | Unsigned Range | Khi nào dùng |
|------|------|--------------|----------------|--------------|
| **SMALLINT** | 2 bytes | -32,768 to 32,767 | 0 to 65,535 | Giá trị nhỏ, có giới hạn |
| **INT** | 4 bytes | ±2 tỷ | 0 to 4 tỷ | Default choice, ID columns |
| **BIGINT** | 8 bytes | ±9 tỷ tỷ | 0 to 18 tỷ tỷ | Large-scale, > 2 tỷ records |

**Best practice:**

- **Default: INT** - Dùng cho hầu hết cases
- **SMALLINT**: Khi chắc chắn giá trị nhỏ
- **BIGINT**: Khi cần scale lớn hoặc chắc chắn > 2 tỷ

---

## 2️⃣ VARCHAR VS CHAR VS TEXT

### **2.1. VARCHAR (Variable Character)**

**Nó là gì?**

VARCHAR là kiểu chuỗi ký tự có độ dài **thay đổi** (variable length).

**Cú pháp:**

```sql
VARCHAR(n)  -- n là độ dài tối đa
```

**Storage:**

- **Actual storage**: Chỉ lưu số ký tự thực tế + 1-2 bytes overhead
- **Ví dụ**: `VARCHAR(100)` lưu "John" (4 ký tự) → chỉ tốn ~5-6 bytes

**Khi nào dùng:**

✅ **Text có độ dài thay đổi**: Name, email, description
✅ **Không biết chính xác độ dài**: Nhưng biết giới hạn tối đa
✅ **Hầu hết các trường hợp**: VARCHAR là default choice cho text

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),        -- Tên có độ dài thay đổi
  email VARCHAR(255),       -- Email có độ dài thay đổi
  bio VARCHAR(500)          -- Bio có độ dài thay đổi
);
```

**Lưu ý production:**

- **Chọn đúng size**: VARCHAR(50) đủ cho email, không cần VARCHAR(1000)
- **UTF-8 encoding**: Một số ký tự (emoji) tốn nhiều bytes hơn
- **Index**: Có thể index, nhưng chậm hơn integer index

---

### **2.2. CHAR (Fixed Character)**

**Nó là gì?**

CHAR là kiểu chuỗi ký tự có độ dài **cố định** (fixed length).

**Cú pháp:**

```sql
CHAR(n)  -- n là độ dài cố định (luôn n ký tự)
```

**Storage:**

- **Fixed storage**: Luôn lưu n bytes (kể cả nếu value ngắn hơn)
- **Ví dụ**: `CHAR(10)` lưu "US" → vẫn tốn 10 bytes (8 bytes padding)

**Khi nào dùng:**

✅ **Text có độ dài cố định**: Country code (2 ký tự), State code (2 ký tự)
✅ **Performance-critical**: CHAR nhanh hơn VARCHAR một chút (không cần length prefix)

**Ví dụ:**

```sql
CREATE TABLE addresses (
  id INT PRIMARY KEY,
  country_code CHAR(2),     -- "US", "VN", "JP" - luôn 2 ký tự
  state_code CHAR(2),        -- "NY", "CA" - luôn 2 ký tự
  zip_code CHAR(10)          -- Có thể có format cố định
);
```

**Lưu ý production:**

- **Waste storage**: Nếu value ngắn hơn n → vẫn tốn n bytes
- **Ít dùng**: Hầu hết cases dùng VARCHAR
- **Chỉ dùng khi**: Độ dài thực sự cố định

---

### **2.3. TEXT**

**Nó là gì?**

TEXT là kiểu chuỗi ký tự **không giới hạn độ dài** (hoặc giới hạn rất lớn).

**Storage:**

- **Variable storage**: Lưu số ký tự thực tế
- **Có thể rất lớn**: Hàng MB cho một value

**Khi nào dùng:**

✅ **Text rất dài**: Description, content, comments
✅ **Không biết độ dài tối đa**: Có thể rất dài
✅ **Rarely queried**: Text ít được query/search

**Ví dụ:**

```sql
CREATE TABLE posts (
  id INT PRIMARY KEY,
  title VARCHAR(300),
  content TEXT,              -- Có thể rất dài (hàng nghìn ký tự)
  description TEXT           -- Có thể dài
);
```

**Lưu ý production:**

- **Không thể index hiệu quả**: Một số databases không cho index trên TEXT
- **Performance**: Chậm hơn VARCHAR (phải đọc nhiều data)
- **Full-text search**: Cần dùng full-text index (khác với regular index)

---

### **So sánh tổng hợp**

| Type | Size | Storage | Index | Khi nào dùng |
|------|------|---------|-------|--------------|
| **VARCHAR(n)** | Variable (0-n) | Chỉ lưu ký tự thực tế | ✅ Có thể | Text có độ dài thay đổi |
| **CHAR(n)** | Fixed (n) | Luôn n bytes | ✅ Có thể | Text có độ dài cố định |
| **TEXT** | Variable (rất lớn) | Chỉ lưu ký tự thực tế | ❌ Khó index | Text rất dài |

**Best practice:**

- **Default: VARCHAR(n)** - Dùng cho hầu hết text
- **CHAR(n)**: Chỉ khi độ dài thực sự cố định
- **TEXT**: Khi text rất dài hoặc không biết độ dài

---

## 3️⃣ DATE VS TIMESTAMP VS TIMESTAMPTZ

### **3.1. DATE**

**Nó là gì?**

DATE là kiểu dữ liệu cho **ngày tháng** (không có giờ phút giây).

**Format:**

```
YYYY-MM-DD
```

**Storage:**

- **3-4 bytes**: Tùy database

**Khi nào dùng:**

✅ **Chỉ cần ngày**: Birth date, hire date, expiration date
✅ **Không cần giờ phút**: Sự kiện trong ngày

**Ví dụ:**

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  birth_date DATE,          -- Chỉ cần ngày
  hire_date DATE            -- Chỉ cần ngày
);
```

**Lưu ý production:**

- **Đơn giản**: Không có timezone, không có time
- **Tiết kiệm storage**: Nhỏ hơn TIMESTAMP
- **Query đơn giản**: `WHERE birth_date > '1990-01-01'`

---

### **3.2. TIMESTAMP**

**Nó là gì?**

TIMESTAMP là kiểu dữ liệu cho **ngày tháng + giờ phút giây** (không có timezone).

**Format:**

```
YYYY-MM-DD HH:MM:SS
```

**Storage:**

- **8 bytes**: Thường lưu dạng số (Unix timestamp)

**Khi nào dùng:**

✅ **Cần biết giờ phút giây**: Created at, updated at, event time
✅ **Local time**: Chỉ dùng trong một timezone

**Ví dụ:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  created_at TIMESTAMP,     -- Ngày + giờ
  updated_at TIMESTAMP      -- Ngày + giờ
);
```

**Lưu ý production:**

- **Không có timezone**: Có thể gây confusion nếu server ở timezone khác
- **Nên dùng TIMESTAMPTZ**: Nếu app là global (nhiều timezones)

---

### **3.3. TIMESTAMPTZ (TIMESTAMP WITH TIME ZONE)**

**Nó là gì?**

TIMESTAMPTZ là kiểu dữ liệu cho **ngày tháng + giờ phút giây + timezone**.

**Format:**

```
YYYY-MM-DD HH:MM:SS+TZ
```

**Storage:**

- **8 bytes**: Tương tự TIMESTAMP (lưu UTC internally)

**Khi nào dùng:**

✅ **Global applications**: Users ở nhiều timezones
✅ **Cần timezone accuracy**: Đảm bảo thời gian đúng dù server ở đâu
✅ **Best practice**: Nên dùng TIMESTAMPTZ cho mọi timestamp

**Ví dụ:**

```sql
CREATE TABLE events (
  id INT PRIMARY KEY,
  event_time TIMESTAMPTZ,   -- Có timezone
  created_at TIMESTAMPTZ    -- Có timezone
);
```

**Lưu ý production:**

- **Auto-convert**: Database tự động convert về UTC khi lưu, về user timezone khi đọc
- **Best practice**: Luôn dùng TIMESTAMPTZ cho global apps
- **Performance**: Tương đương TIMESTAMP

---

### **So sánh tổng hợp**

| Type | Lưu gì | Timezone | Storage | Khi nào dùng |
|------|--------|----------|---------|--------------|
| **DATE** | Chỉ ngày | Không | 3-4 bytes | Birth date, hire date |
| **TIMESTAMP** | Ngày + giờ | Không | 8 bytes | Local time, single timezone |
| **TIMESTAMPTZ** | Ngày + giờ + TZ | Có | 8 bytes | Global apps, multiple timezones |

**Best practice:**

- **DATE**: Chỉ khi không cần giờ
- **TIMESTAMP**: Chỉ khi chắc chắn single timezone
- **TIMESTAMPTZ**: Default choice cho mọi timestamp (global apps)

---

## 4️⃣ NUMERIC VS FLOAT VS DOUBLE

### **4.1. NUMERIC / DECIMAL**

**Nó là gì?**

NUMERIC (hoặc DECIMAL) là kiểu số thập phân **chính xác** (không làm tròn).

**Cú pháp:**

```sql
NUMERIC(precision, scale)
DECIMAL(precision, scale)
-- precision: Tổng số chữ số
-- scale: Số chữ số sau dấu phẩy
```

**Storage:**

- **Variable**: Tùy precision và scale
- **Ví dụ**: `DECIMAL(10, 2)` → ~5-9 bytes

**Khi nào dùng:**

✅ **Tiền tệ**: Price, amount, balance (cần chính xác)
✅ **Đo lường chính xác**: Weight, length (cần chính xác)
✅ **Không được làm tròn**: Bất kỳ tính toán nào cần chính xác

**Ví dụ:**

```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  price DECIMAL(10, 2)      -- Tiền: 99.99, 1000.50
);

CREATE TABLE measurements (
  id INT PRIMARY KEY,
  weight DECIMAL(8, 3)       -- Cân nặng: 75.500 kg
);
```

**Lưu ý production:**

- **Chính xác**: Không làm tròn → đảm bảo tính toán đúng
- **Performance**: Chậm hơn FLOAT một chút, nhưng đảm bảo chính xác
- **Storage**: Tốn hơn FLOAT, nhưng không đáng kể

---

### **4.2. FLOAT**

**Nó là gì?**

FLOAT là kiểu số thập phân **gần đúng** (có thể làm tròn).

**Storage:**

- **4 bytes**: Single precision
- **8 bytes**: Double precision (DOUBLE)

**Khi nào dùng:**

✅ **Khoa học, tính toán**: Không cần chính xác tuyệt đối
✅ **Statistics, analytics**: Sai số nhỏ chấp nhận được
✅ **KHÔNG dùng cho tiền**: FLOAT có thể làm tròn → sai số tiền

**Ví dụ:**

```sql
CREATE TABLE scientific_data (
  id INT PRIMARY KEY,
  temperature FLOAT,         -- Nhiệt độ: 25.123456789 (làm tròn OK)
  pressure FLOAT             -- Áp suất: 1013.25 (làm tròn OK)
);
```

**Lưu ý production:**

- **Có thể làm tròn**: Không đảm bảo chính xác
- **Performance**: Nhanh hơn DECIMAL
- **KHÔNG dùng cho tiền**: Có thể sai số

---

### **4.3. DOUBLE**

**Nó là gì?**

DOUBLE là kiểu số thập phân **gần đúng** với độ chính xác cao hơn FLOAT.

**Storage:**

- **8 bytes**: Double precision

**Khi nào dùng:**

✅ **Cần độ chính xác cao hơn FLOAT**: Nhưng vẫn chấp nhận làm tròn
✅ **Khoa học, engineering**: Tính toán phức tạp

**Lưu ý production:**

- **Vẫn có thể làm tròn**: Không đảm bảo chính xác như DECIMAL
- **Performance**: Tương đương FLOAT
- **KHÔNG dùng cho tiền**: Vẫn có thể sai số

---

### **So sánh tổng hợp**

| Type | Precision | Storage | Performance | Khi nào dùng |
|------|-----------|---------|-------------|--------------|
| **DECIMAL** | Chính xác | Variable | Chậm hơn | Tiền tệ, đo lường chính xác |
| **FLOAT** | Gần đúng | 4 bytes | Nhanh | Khoa học, statistics |
| **DOUBLE** | Gần đúng (cao hơn) | 8 bytes | Nhanh | Khoa học, engineering |

**Best practice:**

- **Tiền tệ**: LUÔN dùng DECIMAL
- **Khoa học**: Có thể dùng FLOAT/DOUBLE
- **Default**: DECIMAL cho business data, FLOAT cho scientific data

---

## 5️⃣ STORAGE SIZE VÀ PERFORMANCE IMPACT

### **5.1. Storage Size**

**Tác động của storage size:**

1. **Disk space**: Tốn storage → tốn tiền
2. **Memory**: Queries load data vào memory → tốn memory
3. **Cache**: Cache nhỏ hơn → cache hit rate thấp hơn
4. **Backup**: Backup lớn hơn → tốn thời gian

**Ví dụ:**

```sql
-- 1 triệu rows
-- Option A: INT (4 bytes) → 4 MB
-- Option B: BIGINT (8 bytes) → 8 MB
-- Chênh lệch: 4 MB (không đáng kể với modern systems)

-- Nhưng với 1 tỷ rows:
-- Option A: 4 GB
-- Option B: 8 GB
-- Chênh lệch: 4 GB (đáng kể!)
```

**Best practice:**

- **Chọn đúng size**: Không dùng BIGINT nếu không cần
- **Với large datasets**: Mỗi byte tiết kiệm → tiết kiệm nhiều GB

---

### **5.2. Performance Impact**

**Tác động của data type đến performance:**

1. **Index size**: Data type lớn → index lớn → chậm hơn
2. **Sort operations**: Data type lớn → sort chậm hơn
3. **JOIN operations**: Data type lớn → JOIN chậm hơn
4. **Memory usage**: Data type lớn → tốn memory hơn

**Ví dụ:**

```sql
-- Index trên VARCHAR(1000) vs VARCHAR(100)
-- VARCHAR(1000): Index lớn hơn 10x → chậm hơn 10x
-- VARCHAR(100): Index nhỏ → nhanh hơn
```

**Best practice:**

- **Chọn đúng size**: Không dùng VARCHAR(1000) nếu chỉ cần VARCHAR(100)
- **Index hiệu quả**: Integer index nhanh hơn string index

---

## 6️⃣ PRODUCTION STORY: QUERY CHẬM DO DÙNG VARCHAR(255) CHO MỌI CỘT

### **Context**

Startup e-commerce có table `products` với tất cả columns dùng VARCHAR(255):

```sql
-- ❌ VI PHẠM: Dùng VARCHAR(255) cho mọi cột
CREATE TABLE products (
  id VARCHAR(255) PRIMARY KEY,        -- ❌ SAI: ID nên là INT
  name VARCHAR(255),
  price VARCHAR(255),                 -- ❌ SAI: Price nên là DECIMAL
  stock_quantity VARCHAR(255),        -- ❌ SAI: Quantity nên là INT
  category VARCHAR(255),
  created_at VARCHAR(255)             -- ❌ SAI: Date nên là TIMESTAMP
);
```

**Business logic:** Developer mới không hiểu data types, dùng VARCHAR(255) cho mọi cột.

### **Vấn đề xuất hiện**

**Tháng 1: Storage bloat**

Với 100,000 products:
- Mỗi row tốn ~1,275 bytes (5 columns × 255 bytes)
- Tổng: ~127 MB
- **Nếu dùng đúng types**: ~50 MB (tiết kiệm 60%!)

**Tháng 2: Query chậm**

Query tìm products theo price:

```sql
-- ❌ CHẬM: Phải convert string → số
SELECT * FROM products 
WHERE CAST(price AS DECIMAL) > 100;
-- Mất 5 giây (phải convert 100,000 rows)
```

**Vấn đề:**
- Phải convert string → số cho mỗi row
- Không thể index hiệu quả trên price (string index)
- Query chậm

**Tháng 3: Index không hiệu quả**

Index trên `price`:

```sql
CREATE INDEX idx_price ON products(price);
-- Index trên VARCHAR(255) → rất lớn, không hiệu quả
```

**Vấn đề:**
- Index trên string lớn → index rất lớn
- String comparison chậm hơn number comparison
- Index không hiệu quả

**Tháng 4: Data inconsistency**

Có thể insert invalid data:

```sql
-- ❌ Có thể insert
INSERT INTO products (id, price) VALUES ('abc', 'xyz');
-- price = 'xyz' → không hợp lệ, nhưng không bị lỗi!
```

**Hậu quả:**
- Data không hợp lệ
- Queries fail khi convert
- Business logic sai

### **Investigation**

**Bước 1: Analyze storage**

```sql
-- Tính storage
SELECT 
  COUNT(*) as row_count,
  AVG(LENGTH(id) + LENGTH(name) + LENGTH(price) + LENGTH(stock_quantity) + LENGTH(category)) as avg_row_size
FROM products;
```

Kết quả:
- Row count: 100,000
- Avg row size: ~200 bytes (thực tế)
- **Nếu dùng đúng types**: ~50 bytes (tiết kiệm 75%!)

**Bước 2: Analyze performance**

```sql
-- Query với string conversion
EXPLAIN ANALYZE
SELECT * FROM products WHERE CAST(price AS DECIMAL) > 100;
-- Execution time: 5.2 seconds
-- Seq Scan on products (cost=0.00..2500.00 rows=50000)
```

**Root cause:**
1. Sai data types → phải convert
2. Index không hiệu quả → phải seq scan
3. Storage lớn → load chậm

### **Fix**

**Fix 1: Migrate to correct data types**

```sql
-- ✅ ĐÚNG: Dùng đúng data types
CREATE TABLE products_new (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10, 2),
  stock_quantity INT,
  category VARCHAR(100),
  created_at TIMESTAMP
);

-- Migrate data
INSERT INTO products_new (id, name, price, stock_quantity, category, created_at)
SELECT 
  CAST(id AS INT),
  name,
  CAST(price AS DECIMAL(10, 2)),
  CAST(stock_quantity AS INT),
  category,
  CAST(created_at AS TIMESTAMP)
FROM products
WHERE id ~ '^[0-9]+$'  -- Chỉ migrate valid IDs
  AND price ~ '^[0-9]+\.?[0-9]*$';  -- Chỉ migrate valid prices
```

**Fix 2: Create proper indexes**

```sql
-- Index trên numeric columns
CREATE INDEX idx_price ON products_new(price);
CREATE INDEX idx_stock ON products_new(stock_quantity);
CREATE INDEX idx_category ON products_new(category);
```

**Fix 3: Update queries**

```sql
-- ✅ Query mới: Không cần convert
SELECT * FROM products_new 
WHERE price > 100;
-- Nhanh hơn: Có index, không cần convert
```

### **Kết quả**

✅ **Correct data types**: Mỗi column dùng đúng type
✅ **Storage giảm 75%**: Từ 127 MB → 50 MB
✅ **Query nhanh hơn 10x**: Từ 5 giây → 0.5 giây
✅ **Index hiệu quả**: Index trên numeric columns nhanh hơn
✅ **Data integrity**: Không thể insert invalid data

**Performance:**
- Query: Từ 5 giây → 0.5 giây (nhanh hơn 10x)
- Storage: Từ 127 MB → 50 MB (giảm 60%)
- Index: Từ 50 MB → 5 MB (giảm 90%)

### **Lesson Learned**

1. **LUÔN dùng đúng data type**: Không dùng VARCHAR cho mọi cột
2. **Chọn đúng size**: VARCHAR(100) đủ, không cần VARCHAR(255)
3. **Performance impact**: Sai data type → query chậm, index không hiệu quả
4. **Storage impact**: Sai data type → tốn storage không cần thiết
5. **Data integrity**: Đúng data type → đảm bảo data hợp lệ

---

## 7️⃣ BEST PRACTICES

### **7.1. Chọn Data Type**

**Quy tắc:**

1. **Số nguyên**: INTEGER (SMALLINT nếu nhỏ, BIGINT nếu lớn)
2. **Số thập phân**: DECIMAL cho tiền, FLOAT cho khoa học
3. **Text**: VARCHAR(n) cho hầu hết, TEXT cho rất dài
4. **Date/Time**: DATE cho ngày, TIMESTAMPTZ cho timestamp
5. **Chọn đúng size**: Không dùng quá lớn

### **7.2. Storage Optimization**

**Best practices:**

✅ **Chọn đúng size**: SMALLINT nếu < 32767, INT nếu < 2 tỷ
✅ **VARCHAR đúng size**: VARCHAR(100) đủ, không cần VARCHAR(1000)
✅ **Tránh TEXT khi có thể**: Dùng VARCHAR(n) nếu biết giới hạn
✅ **DECIMAL đúng precision**: DECIMAL(10, 2) đủ cho tiền

### **7.3. Performance Optimization**

**Best practices:**

✅ **Integer cho ID**: INT/BIGINT nhanh hơn VARCHAR
✅ **Index trên numeric**: Index trên số nhanh hơn string
✅ **Chọn đúng size**: Data type nhỏ → index nhỏ → nhanh hơn

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **INTEGER types**: SMALLINT (nhỏ), INT (default), BIGINT (lớn)
2. **Text types**: VARCHAR (variable), CHAR (fixed), TEXT (rất dài)
3. **Date/Time**: DATE (ngày), TIMESTAMP (local), TIMESTAMPTZ (global)
4. **Numeric**: DECIMAL (chính xác), FLOAT/DOUBLE (gần đúng)
5. **Storage & Performance**: Chọn đúng type → tiết kiệm storage, tăng performance

### **Best Practices**

✅ **Chọn đúng data type**: Không dùng VARCHAR cho mọi cột
✅ **Chọn đúng size**: VARCHAR(100) đủ, không cần VARCHAR(255)
✅ **INTEGER cho ID**: INT/BIGINT nhanh hơn VARCHAR
✅ **DECIMAL cho tiền**: FLOAT có thể làm tròn → sai số
✅ **TIMESTAMPTZ cho global apps**: Đảm bảo timezone đúng

### **Câu hỏi tự kiểm tra**

1. SMALLINT vs INT vs BIGINT - khi nào dùng gì?
2. VARCHAR vs CHAR vs TEXT - trade-offs?
3. DATE vs TIMESTAMP vs TIMESTAMPTZ - khi nào dùng gì?
4. DECIMAL vs FLOAT - tại sao tiền phải dùng DECIMAL?
5. Storage size ảnh hưởng đến performance như thế nào?

---




**Chuẩn bị cho [Day-009: Index-Basics](../Day-009-Index-Basics/theory.md)** 🚀
