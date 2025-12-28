# Day-006: Normalization - 2NF (Second Normal Form)

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- 2NF (Second Normal Form) là gì và tại sao cần 2NF
- Partial dependency là gì và cách nhận biết
- Khi nào cần 2NF và khi nào có thể bỏ qua
- Trade-off giữa Normalization và Denormalization
- Cách sửa vi phạm 2NF

---

## 1️⃣ 2NF (SECOND NORMAL FORM) LÀ GÌ?

### **Nó là gì?**

**2NF (Second Normal Form)** yêu cầu:

1. **Đã tuân thủ 1NF** (First Normal Form)
2. **Không có Partial Dependency** (Phụ thuộc một phần)

**Partial Dependency là gì?**

**Partial Dependency** xảy ra khi một non-key column phụ thuộc vào **một phần** của Composite Primary Key, thay vì phụ thuộc vào **toàn bộ** Primary Key.

**Nói cách khác:**
- Nếu Primary Key là Composite Key (nhiều columns)
- Và có column phụ thuộc vào chỉ một phần của Composite Key
- → Vi phạm 2NF

### **Tại sao tồn tại?**

2NF tồn tại để giải quyết vấn đề **Partial Dependency**:

1. **Data redundancy**: Dữ liệu trùng lặp không cần thiết
2. **Update anomalies**: Sửa một chỗ, phải sửa nhiều chỗ
3. **Insert anomalies**: Khó insert một số dữ liệu
4. **Delete anomalies**: Xóa một record → mất dữ liệu khác

**Ví dụ đơn giản:**

```
❌ VI PHẠM 2NF:
order_items table:
order_id | product_id | product_name | quantity | price
---------|------------|--------------|----------|-------
1        | 101        | Laptop       | 2        | 1000
1        | 102        | Mouse        | 5        | 20
2        | 101        | Laptop       | 1        | 1000  ← product_name duplicate!

Vấn đề:
- product_name phụ thuộc vào product_id (chỉ một phần của PK)
- product_name bị duplicate (Laptop xuất hiện 2 lần)
- Nếu đổi tên "Laptop" → "Laptop Pro" → phải sửa 2 rows

✅ TUÂN THỦ 2NF:
products table:
product_id | product_name | price
-----------|--------------|-------
101        | Laptop       | 1000
102        | Mouse        | 20

order_items table:
order_id | product_id | quantity
---------|------------|----------
1        | 101        | 2
1        | 102        | 5
2        | 101        | 1

Ưu điểm:
- product_name chỉ lưu một lần
- Đổi tên → chỉ sửa 1 chỗ
- Không duplicate
```

### **Khi nào cần trong production?**

**2NF cần khi:**

✅ **Có Composite Primary Key**: Primary Key gồm nhiều columns
✅ **Có Partial Dependency**: Non-key column phụ thuộc vào một phần của PK
✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Frequent updates**: Dữ liệu thường xuyên thay đổi

**KHÔNG cần 2NF khi:**

❌ **Single Primary Key**: Primary Key chỉ có 1 column → tự động tuân thủ 2NF
❌ **Data warehouse**: Analytics, có thể denormalize để tăng performance
❌ **Read-only data**: Data không thay đổi → không có update anomalies

**Lưu ý:** Nếu Primary Key là Single Key (chỉ 1 column), table tự động tuân thủ 2NF (không có Partial Dependency).

---

## 2️⃣ PARTIAL DEPENDENCY LÀ GÌ?

### **Nó là gì?**

**Partial Dependency** (Phụ thuộc một phần) xảy ra khi:

1. **Primary Key là Composite Key** (nhiều columns)
2. **Có non-key column phụ thuộc vào chỉ một phần** của Composite Key

**Ví dụ:**

```sql
-- ❌ VI PHẠM 2NF
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),  -- Phụ thuộc vào product_id (một phần của PK)
  quantity INT,
  PRIMARY KEY (order_id, product_id)  -- Composite Key
);
```

**Phân tích:**

- **Primary Key**: `(order_id, product_id)` - Composite Key
- **product_name**: Phụ thuộc vào `product_id` (chỉ một phần của PK)
- **quantity**: Phụ thuộc vào cả `(order_id, product_id)` - OK

→ **product_name** có Partial Dependency → Vi phạm 2NF

### **Tại sao quan trọng?**

Partial Dependency gây ra:

1. **Data redundancy**: 
   - `product_name` bị duplicate trong nhiều rows
   - Tốn storage không cần thiết

2. **Update anomalies**:
   - Đổi tên product → phải sửa nhiều rows trong `order_items`
   - Dễ quên, dễ lỗi

3. **Insert anomalies**:
   - Không thể insert product mới nếu chưa có order
   - Phải tạo order giả → không hợp lý

4. **Delete anomalies**:
   - Xóa order cuối cùng có product → mất thông tin product
   - Không hợp lý

### **Khi nào xảy ra?**

Partial Dependency xảy ra khi:

✅ **Có Composite Primary Key**
✅ **Có non-key column phụ thuộc vào một phần của PK**

**Ví dụ cụ thể:**

```sql
-- Composite Key: (student_id, course_id)
-- course_name phụ thuộc vào course_id (một phần của PK)
-- → Partial Dependency
```

---

## 3️⃣ CÁCH NHẬN BIẾT VÀ SỬA VI PHẠM 2NF

### **3.1. Cách nhận biết**

**Bước 1: Xác định Primary Key**
- Primary Key là Single Key hay Composite Key?

**Bước 2: Nếu là Composite Key**
- Có non-key column nào phụ thuộc vào chỉ một phần của PK không?

**Bước 3: Kiểm tra dependencies**
- Column X phụ thuộc vào gì?
- Nếu phụ thuộc vào chỉ một phần của PK → Partial Dependency

**Ví dụ:**

```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  course_name VARCHAR(100),  -- Phụ thuộc vào course_id
  student_name VARCHAR(100),  -- Phụ thuộc vào student_id
  grade VARCHAR(2),           -- Phụ thuộc vào (student_id, course_id)
  PRIMARY KEY (student_id, course_id)
);
```

**Phân tích:**
- **PK**: `(student_id, course_id)` - Composite Key
- **course_name**: Phụ thuộc vào `course_id` (một phần) → Partial Dependency
- **student_name**: Phụ thuộc vào `student_id` (một phần) → Partial Dependency
- **grade**: Phụ thuộc vào cả `(student_id, course_id)` → OK

→ **Vi phạm 2NF**

### **3.2. Cách sửa**

**Nguyên tắc:** Tách columns có Partial Dependency thành bảng riêng.

**Ví dụ:**

```sql
-- ❌ VI PHẠM 2NF
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),  -- Partial Dependency
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Cách sửa:**

```sql
-- ✅ TUÂN THỦ 2NF: Tách thành 2 tables
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100),
  price DECIMAL(10, 2)
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**Kết quả:**
- `product_name` chỉ lưu một lần trong `products`
- `order_items` chỉ lưu relationship và quantity
- Không có Partial Dependency

---

## 4️⃣ VÍ DỤ CỤ THỂ

### **4.1. Ví dụ: Order Items**

**Vi phạm 2NF:**

```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),  -- Partial Dependency
  product_price DECIMAL(10, 2),  -- Partial Dependency
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Vấn đề:**
- `product_name` và `product_price` phụ thuộc vào `product_id` (một phần của PK)
- Bị duplicate trong nhiều orders
- Update product → phải sửa nhiều rows

**Cách sửa:**

```sql
-- Tách thành 2 tables
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100),
  product_price DECIMAL(10, 2)
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

---

### **4.2. Ví dụ: Enrollments**

**Vi phạm 2NF:**

```sql
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  student_name VARCHAR(100),  -- Partial Dependency
  course_name VARCHAR(100),    -- Partial Dependency
  enrollment_date DATE,
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id)
);
```

**Vấn đề:**
- `student_name` phụ thuộc vào `student_id` (một phần)
- `course_name` phụ thuộc vào `course_id` (một phần)
- Bị duplicate

**Cách sửa:**

```sql
-- Tách thành 3 tables
CREATE TABLE students (
  student_id INT PRIMARY KEY,
  student_name VARCHAR(100)
);

CREATE TABLE courses (
  course_id INT PRIMARY KEY,
  course_name VARCHAR(100)
);

CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  enrollment_date DATE,
  grade VARCHAR(2),
  PRIMARY KEY (student_id, course_id),
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

---

### **4.3. Ví dụ: Single Primary Key (Tự động tuân thủ 2NF)**

**Nếu Primary Key là Single Key:**

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,  -- Single Key
  user_id INT,
  user_name VARCHAR(100),  -- Phụ thuộc vào user_id
  total_amount DECIMAL(10, 2)
);
```

**Phân tích:**
- **PK**: `id` - Single Key (chỉ 1 column)
- **user_name**: Phụ thuộc vào `user_id` (không phải PK)
- → **KHÔNG có Partial Dependency** (vì không có Composite Key)

**Tuy nhiên:** Có thể vi phạm **3NF** (Transitive Dependency) - sẽ học ở Day-007.

**Kết luận:** Table với Single Primary Key tự động tuân thủ 2NF.

---

## 5️⃣ TRADE-OFF: NORMALIZATION VS DENORMALIZATION

### **5.1. Normalized (Tuân thủ 2NF)**

**Ưu điểm:**

✅ **Giảm redundancy**: Dữ liệu không trùng lặp
✅ **Dễ maintain**: Update một chỗ
✅ **Data integrity**: Đảm bảo dữ liệu nhất quán
✅ **Storage hiệu quả**: Tiết kiệm storage

**Nhược điểm:**

❌ **Nhiều JOINs**: Query cần JOIN nhiều tables
❌ **Có thể chậm**: Nhiều JOINs → performance có thể chậm hơn
❌ **Phức tạp hơn**: Nhiều tables, nhiều relationships

---

### **5.2. Denormalized (Vi phạm 2NF)**

**Ưu điểm:**

✅ **Query nhanh**: Ít JOINs → nhanh hơn
✅ **Đơn giản**: Ít tables, ít relationships
✅ **Read performance**: Tốt cho read-heavy workloads

**Nhược điểm:**

❌ **Data redundancy**: Dữ liệu trùng lặp
❌ **Khó maintain**: Update nhiều chỗ
❌ **Storage tốn**: Tốn storage hơn
❌ **Inconsistency risk**: Dễ có dữ liệu không nhất quán

---

### **5.3. Khi nào dùng gì?**

**Nên normalize (tuân thủ 2NF) khi:**

✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Frequent updates**: Dữ liệu thường xuyên thay đổi
✅ **Data integrity critical**: Cần đảm bảo dữ liệu nhất quán
✅ **Storage matters**: Storage quan trọng

**Có thể denormalize khi:**

✅ **Data warehouse**: Analytics, read-heavy
✅ **Performance-critical**: Cần query nhanh
✅ **Read-only data**: Data không thay đổi
✅ **Simple queries**: Queries đơn giản, ít phức tạp

**Best practice:**

- **OLTP**: Normalize (tuân thủ 2NF, 3NF)
- **Data Warehouse**: Có thể denormalize để tăng performance
- **Hybrid**: Normalize cho integrity, denormalize cho performance (materialized views)

---

## 6️⃣ PRODUCTION STORY: QUERY CHẬM DO THIẾU 2NF, FIX BẰNG CÁCH NORMALIZE

### **Context**

Startup e-commerce có table `order_items` vi phạm 2NF:

```sql
-- ❌ VI PHẠM 2NF
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  product_name VARCHAR(100),      -- Partial Dependency
  product_category VARCHAR(50),   -- Partial Dependency
  product_price DECIMAL(10, 2),   -- Partial Dependency
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Business logic:** Mỗi order có nhiều products, mỗi product có name, category, price.

### **Vấn đề xuất hiện**

**Tháng 1: Data redundancy**

Khi có 10,000 orders, mỗi order có 5 products:
- `product_name` bị duplicate 50,000 lần!
- `product_category` bị duplicate 50,000 lần!
- `product_price` bị duplicate 50,000 lần!

**Storage:**
- 50,000 rows × 3 columns × ~50 bytes = ~7.5 MB redundant data

**Tháng 2: Update nightmare**

Cần đổi tên product "Laptop" → "Laptop Pro":

```sql
-- ❌ Phải update 10,000 rows!
UPDATE order_items 
SET product_name = 'Laptop Pro'
WHERE product_name = 'Laptop';
-- Mất 30 giây, lock table
```

**Tháng 3: Query chậm**

Query tính tổng revenue theo category:

```sql
-- ❌ CHẬM: Phải scan 50,000 rows
SELECT 
  product_category,
  SUM(quantity * product_price) as revenue
FROM order_items
GROUP BY product_category;
-- Mất 5 giây
```

**Vấn đề:**
- Phải scan tất cả 50,000 rows
- Không thể index hiệu quả trên `product_category` (vì duplicate)
- Query chậm

**Tháng 4: Inconsistency**

Sau một số updates, có inconsistency:

```sql
-- ❌ Inconsistency: Cùng product_id nhưng product_name khác nhau
SELECT product_id, product_name, COUNT(*)
FROM order_items
GROUP BY product_id, product_name;
-- product_id = 101: "Laptop" (9,000 rows), "Laptop Pro" (1,000 rows)
```

**Hậu quả:**
- Data không nhất quán
- Reports sai
- Users thấy tên product khác nhau

### **Investigation**

**Bước 1: Analyze redundancy**

```sql
-- Tính redundancy
SELECT 
  COUNT(DISTINCT product_id) as unique_products,
  COUNT(*) as total_rows,
  COUNT(*) / COUNT(DISTINCT product_id) as redundancy_factor
FROM order_items;
```

Kết quả:
- Unique products: 1,000
- Total rows: 50,000
- **Redundancy factor: 50x** (mỗi product xuất hiện 50 lần trung bình)

**Bước 2: Check inconsistencies**

```sql
-- Tìm inconsistencies
SELECT product_id, COUNT(DISTINCT product_name) as name_count
FROM order_items
GROUP BY product_id
HAVING COUNT(DISTINCT product_name) > 1;
```

Kết quả: **50 products** có tên không nhất quán!

**Root cause:**
1. Vi phạm 2NF: Partial Dependency
2. Data redundancy: 50x duplication
3. Update không đầy đủ → inconsistency

### **Fix**

**Fix 1: Normalize schema**

```sql
-- ✅ TUÂN THỦ 2NF: Tách thành 2 tables
CREATE TABLE products (
  product_id INT PRIMARY KEY,
  product_name VARCHAR(100),
  product_category VARCHAR(50),
  product_price DECIMAL(10, 2)
);

CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

**Fix 2: Migrate data**

```sql
-- Extract unique products
INSERT INTO products (product_id, product_name, product_category, product_price)
SELECT DISTINCT 
  product_id,
  MAX(product_name) as product_name,  -- Lấy tên mới nhất
  MAX(product_category) as product_category,
  MAX(product_price) as product_price
FROM old_order_items
GROUP BY product_id;

-- Migrate order_items (chỉ giữ order_id, product_id, quantity)
INSERT INTO order_items (order_id, product_id, quantity)
SELECT order_id, product_id, quantity
FROM old_order_items;
```

**Fix 3: Update queries**

```sql
-- ✅ Query mới: JOIN với products
SELECT 
  p.product_category,
  SUM(oi.quantity * p.product_price) as revenue
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_category;
-- Nhanh hơn: Có thể index trên products.product_category
```

### **Kết quả**

✅ **Normalized schema**: Không có Partial Dependency
✅ **No redundancy**: product_name chỉ lưu một lần
✅ **Fast updates**: Update product → chỉ sửa 1 row
✅ **Fast queries**: Có thể index trên products.category → query nhanh hơn 10x
✅ **Data consistency**: Không có inconsistency

**Performance:**
- Update: Từ 30 giây → 0.1 giây (chỉ update 1 row)
- Query: Từ 5 giây → 0.5 giây (có index, ít rows hơn)
- Storage: Giảm 50x (từ 50,000 rows → 1,000 products + 50,000 order_items)

### **Lesson Learned**

1. **LUÔN tuân thủ 2NF** trong OLTP systems
2. **Partial Dependency gây redundancy**: Tốn storage, chậm queries
3. **Normalize early**: Dễ hơn normalize sau khi có nhiều data
4. **Index trên normalized tables**: Hiệu quả hơn index trên denormalized data
5. **Trade-off**: Normalize cho integrity, denormalize cho performance (data warehouse)

---

## 7️⃣ BEST PRACTICES

### **7.1. Quy tắc 2NF**

1. **Đã tuân thủ 1NF**
2. **Không có Partial Dependency**
3. **Tách columns có Partial Dependency** thành bảng riêng

### **7.2. Khi nào cần 2NF?**

**Cần khi:**
- ✅ Có Composite Primary Key
- ✅ Có Partial Dependency
- ✅ OLTP systems

**KHÔNG cần khi:**
- ❌ Single Primary Key (tự động tuân thủ 2NF)
- ❌ Data warehouse (có thể denormalize)

### **7.3. Cách sửa vi phạm 2NF**

1. **Xác định Partial Dependencies**
2. **Tách columns có Partial Dependency** thành bảng riêng
3. **Tạo Foreign Key** để maintain relationships
4. **Migrate data** từ schema cũ sang schema mới

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **2NF yêu cầu**: Đã tuân thủ 1NF + không có Partial Dependency
2. **Partial Dependency**: Non-key column phụ thuộc vào một phần của Composite Primary Key
3. **Cách sửa**: Tách columns có Partial Dependency thành bảng riêng
4. **Single Primary Key**: Tự động tuân thủ 2NF
5. **Trade-off**: Normalize cho integrity, denormalize cho performance

### **Best Practices**

✅ **Luôn tuân thủ 2NF** trong OLTP systems
✅ **Tách Partial Dependencies** thành bảng riêng
✅ **Tạo Foreign Keys** để maintain relationships
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Consider denormalization** cho data warehouse nếu cần performance

### **Câu hỏi tự kiểm tra**

1. 2NF là gì? Yêu cầu gì?
2. Partial Dependency là gì? Cách nhận biết?
3. Tại sao cần 2NF?
4. Khi nào table tự động tuân thủ 2NF?
5. Cách sửa vi phạm 2NF?

---




**Chuẩn bị cho [Day-007: Normalization-3NF](../Day-007-Normalization-3NF/theory.md)** 🚀
