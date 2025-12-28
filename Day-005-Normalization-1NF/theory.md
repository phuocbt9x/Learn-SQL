# Day-005: Normalization - Chuẩn hóa dữ liệu (1NF)

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- 1NF (First Normal Form) là gì và tại sao cần 1NF
- Atomic values là gì và tại sao quan trọng
- Cách nhận biết vi phạm 1NF
- Cách sửa vi phạm 1NF
- Hậu quả nếu vi phạm 1NF trong production

---

## 1️⃣ NORMALIZATION LÀ GÌ?

### **Nó là gì?**

**Normalization** (Chuẩn hóa dữ liệu) là quá trình **tổ chức dữ liệu** trong database để:
- **Giảm redundancy** (trùng lặp dữ liệu)
- **Đảm bảo data integrity** (tính toàn vẹn dữ liệu)
- **Tránh anomalies** (bất thường khi insert/update/delete)

**Các dạng chuẩn hóa (Normal Forms):**

1. **1NF** (First Normal Form): Atomic values
2. **2NF** (Second Normal Form): No partial dependencies
3. **3NF** (Third Normal Form): No transitive dependencies
4. **BCNF** (Boyce-Codd Normal Form): Advanced 3NF
5. **4NF, 5NF**: Higher normal forms (ít dùng trong thực tế)

**Trong Day này, chúng ta học 1NF.**

### **Tại sao tồn tại?**

Normalization tồn tại để giải quyết các vấn đề của **denormalized data** (dữ liệu không chuẩn hóa):

1. **Data redundancy**: Dữ liệu trùng lặp → tốn storage, khó maintain
2. **Update anomalies**: Sửa một chỗ, phải sửa nhiều chỗ
3. **Insert anomalies**: Không thể insert một số dữ liệu
4. **Delete anomalies**: Xóa một record → mất dữ liệu khác

**Ví dụ đơn giản:**

```
❌ KHÔNG chuẩn hóa:
orders table:
id | user_name | user_email        | product_name | price
1  | John      | john@ex.com       | Laptop       | 1000
2  | John      | john@ex.com       | Mouse        | 20
3  | Jane      | jane@ex.com       | Laptop       | 1000

Vấn đề:
- user_name, user_email bị duplicate
- Nếu John đổi email → phải sửa 2 rows
- Nếu xóa order 1 → mất thông tin John (nếu chỉ có 1 order)

✅ CHUẨN HÓA:
users table:
id | name | email
1  | John | john@ex.com
2  | Jane | jane@ex.com

orders table:
id | user_id | product_name | price
1  | 1       | Laptop       | 1000
2  | 1       | Mouse        | 20
3  | 2       | Laptop       | 1000

Ưu điểm:
- Không duplicate user info
- Đổi email → chỉ sửa 1 chỗ
- Xóa order → không mất user info
```

### **Khi nào dùng trong production?**

Normalization nên dùng khi:

✅ **OLTP systems**: Transaction systems cần data integrity
✅ **Complex relationships**: Nhiều tables có relationships
✅ **Frequent updates**: Dữ liệu thường xuyên thay đổi
✅ **Data consistency critical**: Cần đảm bảo dữ liệu nhất quán

**KHÔNG nên normalize quá mức khi:**

❌ **Data warehouse**: Analytics, read-heavy → có thể denormalize để tăng performance
❌ **Simple applications**: Ứng dụng đơn giản, ít relationships
❌ **Performance-critical**: Cần query nhanh → có thể chấp nhận một số redundancy

**Trade-off:**
- **Normalized**: Data integrity tốt, nhưng có thể chậm hơn (nhiều JOINs)
- **Denormalized**: Query nhanh hơn, nhưng dễ có inconsistency

---

## 2️⃣ 1NF (FIRST NORMAL FORM) LÀ GÌ?

### **Nó là gì?**

**1NF (First Normal Form)** yêu cầu:

1. **Mỗi cell chỉ chứa một giá trị atomic** (không thể chia nhỏ)
2. **Không có repeating groups** (nhóm lặp lại)
3. **Mỗi row là unique** (có Primary Key)

**Nói cách khác:**
- Mỗi cell = một giá trị đơn (không phải list, array, multiple values)
- Không có columns như "phone1, phone2, phone3"
- Không có columns như "tags: 'sql,database,postgresql'"

### **Tại sao tồn tại?**

1NF tồn tại để đảm bảo:
- **Dữ liệu có cấu trúc rõ ràng**: Mỗi cell có một giá trị
- **Dễ query**: Không phải parse strings, arrays
- **Dễ maintain**: Update/delete đơn giản
- **Database có thể index**: Index trên atomic values hiệu quả

### **Khi nào dùng trong production?**

**1NF là BẮT BUỘC** trong mọi table production:

✅ **Mọi table đều phải tuân thủ 1NF**: Không có exception
✅ **Atomic values**: Mỗi cell = một giá trị
✅ **No repeating groups**: Không có columns lặp lại

**KHÔNG nên vi phạm 1NF** vì:
- ❌ Khó query
- ❌ Khó maintain
- ❌ Khó index
- ❌ Dễ có inconsistency

---

## 3️⃣ ATOMIC VALUES LÀ GÌ?

### **Nó là gì?**

**Atomic value** (Giá trị nguyên tử) là giá trị **không thể chia nhỏ** thành các phần nhỏ hơn có ý nghĩa.

**Ví dụ Atomic values:**

✅ **Atomic:**
- `name = "John Doe"` - Có thể chia thành "John" và "Doe", nhưng trong context này, "John Doe" là atomic (một tên đầy đủ)
- `age = 25` - Số nguyên, atomic
- `email = "john@example.com"` - Email là atomic (không nên tách thành username và domain)
- `price = 99.99` - Số, atomic

❌ **KHÔNG Atomic:**
- `phones = "123-456-7890, 987-654-3210"` - Nhiều số điện thoại trong một cell
- `tags = "sql,database,postgresql"` - Nhiều tags trong một cell
- `address = "123 Main St, New York, NY 10001"` - Có thể tách thành street, city, state, zip

**Lưu ý:** "Atomic" phụ thuộc vào **business context**:

- `name = "John Doe"`: 
  - Nếu chỉ cần full name → atomic
  - Nếu cần first name và last name riêng → không atomic (nên tách thành `first_name`, `last_name`)

- `address = "123 Main St, New York, NY 10001"`:
  - Nếu chỉ cần full address → atomic
  - Nếu cần query theo city, state → không atomic (nên tách thành `street`, `city`, `state`, `zip`)

### **Tại sao quan trọng?**

Atomic values quan trọng vì:

1. **Dễ query**: 
   ```sql
   -- ❌ KHÔNG atomic: Khó query
   SELECT * FROM products WHERE tags LIKE '%sql%';
   
   -- ✅ Atomic: Dễ query
   SELECT * FROM products p
   JOIN product_tags pt ON p.id = pt.product_id
   JOIN tags t ON pt.tag_id = t.id
   WHERE t.name = 'sql';
   ```

2. **Dễ index**: Index trên atomic values hiệu quả hơn
3. **Dễ update**: Update một giá trị không ảnh hưởng giá trị khác
4. **Data integrity**: Đảm bảo dữ liệu nhất quán

### **Khi nào dùng trong production?**

**Luôn dùng atomic values** trong production:

✅ **Mỗi cell = một giá trị atomic**
✅ **Tách non-atomic values** thành nhiều columns hoặc nhiều rows
✅ **Consider business context**: Quyết định atomic dựa trên use case

---

## 4️⃣ VÍ DỤ VI PHẠM 1NF VÀ CÁCH SỬA

### **4.1. Vi phạm: Multiple values trong một cell**

**Ví dụ:**

```sql
-- ❌ VI PHẠM 1NF
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  phones VARCHAR(200)  -- "123-456-7890, 987-654-3210"
);
```

**Vấn đề:**

1. **Khó query**: Không thể query "tất cả users có phone = '123-456-7890'"
2. **Khó update**: Update một phone → phải parse string
3. **Khó validate**: Khó đảm bảo format đúng
4. **Không thể index**: Index trên string không hiệu quả

**Cách sửa:**

**Option 1: Tách thành nhiều rows (TỐT HƠN)**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE user_phones (
  id INT PRIMARY KEY,
  user_id INT,
  phone VARCHAR(20),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Option 2: Tách thành nhiều columns (KHÔNG TỐT)**

```sql
-- ⚠️ Có thể dùng, nhưng không linh hoạt
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  phone1 VARCHAR(20),
  phone2 VARCHAR(20),
  phone3 VARCHAR(20)
);
-- Vấn đề: Giới hạn số lượng phones, không linh hoạt
```

**Recommendation:** Dùng Option 1 (tách thành bảng riêng).

---

### **4.2. Vi phạm: Repeating groups**

**Ví dụ:**

```sql
-- ❌ VI PHẠM 1NF
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  product1_name VARCHAR(100),
  product1_quantity INT,
  product2_name VARCHAR(100),
  product2_quantity INT,
  product3_name VARCHAR(100),
  product3_quantity INT
);
```

**Vấn đề:**

1. **Giới hạn số lượng**: Chỉ có thể có tối đa 3 products
2. **Khó query**: "Tìm tất cả orders có product X" → phải check 3 columns
3. **Waste storage**: Nếu order chỉ có 1 product → 2 columns trống
4. **Khó maintain**: Thêm product thứ 4 → phải thêm columns

**Cách sửa:**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_name VARCHAR(100),
  quantity INT,
  FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

**Ưu điểm:**
- Không giới hạn số lượng products
- Dễ query
- Không waste storage
- Dễ maintain

---

### **4.3. Vi phạm: Comma-separated values**

**Ví dụ:**

```sql
-- ❌ VI PHẠM 1NF
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  tags VARCHAR(500)  -- "sql,database,postgresql"
);
```

**Vấn đề:**

1. **Khó query**: 
   ```sql
   -- ❌ Khó query chính xác
   SELECT * FROM products WHERE tags LIKE '%sql%';
   -- Có thể match "mysql" (không đúng)
   ```

2. **Khó update**: Update một tag → phải parse và rebuild string
3. **Khó validate**: Khó đảm bảo format đúng
4. **Không thể index**: Index trên string không hiệu quả

**Cách sửa:**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng (many-to-many)
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200)
);

CREATE TABLE tags (
  id INT PRIMARY KEY,
  name VARCHAR(50) UNIQUE
);

CREATE TABLE product_tags (
  product_id INT,
  tag_id INT,
  PRIMARY KEY (product_id, tag_id),
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (tag_id) REFERENCES tags(id)
);
```

**Ưu điểm:**
- Dễ query: `WHERE tag.name = 'sql'`
- Có thể index trên tag.name
- Dễ update: Thêm/xóa tag đơn giản
- Không duplicate tags (normalize)

---

### **4.4. Vi phạm: JSON/Array trong cell**

**Ví dụ (PostgreSQL):**

```sql
-- ❌ VI PHẠM 1NF (nếu dùng không đúng)
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200),
  attributes JSONB  -- {"color": "red", "size": "L", "material": "cotton"}
);
```

**Vấn đề:**

1. **Khó query**: Phải dùng JSON functions
2. **Khó index**: Index trên JSON phức tạp
3. **Khó validate**: Không có schema enforcement
4. **Khó maintain**: Update một attribute → phải parse JSON

**Khi nào dùng JSON được?**

✅ **Schema linh hoạt**: Mỗi product có attributes khác nhau
✅ **Rarely queried**: Attributes ít được query
✅ **Document database pattern**: Phù hợp với document model

**Khi nào KHÔNG nên dùng JSON?**

❌ **Fixed schema**: Tất cả products có cùng attributes
❌ **Frequently queried**: Attributes thường được query
❌ **Need indexing**: Cần index trên attributes

**Cách sửa (nếu cần normalize):**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(200)
);

CREATE TABLE product_attributes (
  id INT PRIMARY KEY,
  product_id INT,
  attribute_name VARCHAR(50),
  attribute_value VARCHAR(200),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

---

## 5️⃣ PRODUCTION STORY: DỮ LIỆU BỊ DUPLICATE 10X DO VI PHẠM 1NF

### **Context**

Startup e-commerce có table `orders` vi phạm 1NF:

```sql
-- ❌ VI PHẠM 1NF
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  products VARCHAR(500),  -- "Laptop, Mouse, Keyboard"
  quantities VARCHAR(100),  -- "1, 2, 1"
  total_amount DECIMAL(10, 2)
);
```

**Business logic:** Mỗi order có thể có nhiều products.

### **Vấn đề xuất hiện**

**Tháng 1: Data entry errors**

Nhân viên nhập data thủ công:

```
Order 1:
products = "Laptop, Mouse"
quantities = "1, 2"

Order 2:
products = "Laptop, Mouse, Keyboard"  -- Thêm Keyboard
quantities = "1, 2"  -- ❌ QUÊN thêm quantity cho Keyboard!
```

**Hậu quả:**
- Data không nhất quán
- Không biết order 2 có bao nhiêu Keyboards

**Tháng 2: Query errors**

Query tính tổng số lượng mỗi product:

```sql
-- ❌ Khó query
SELECT 
  SUBSTRING_INDEX(products, ',', 1) as product,
  SUM(CAST(SUBSTRING_INDEX(quantities, ',', 1) AS INT)) as total
FROM orders;
-- Chỉ lấy product đầu tiên, không đúng!
```

**Hậu quả:**
- Query không chính xác
- Không thể tính đúng số lượng

**Tháng 3: Data duplication**

Khi export data để báo cáo:

```sql
-- Export orders
SELECT * FROM orders;
```

**Vấn đề:**
- Mỗi order có nhiều products → phải parse và duplicate rows
- Export 1000 orders → thành 5000 rows (vì mỗi order có ~5 products)
- **Dữ liệu bị duplicate 5x!**

**Tháng 4: Update nightmare**

Cần update price của "Laptop":

```sql
-- ❌ Khó update
UPDATE orders 
SET products = REPLACE(products, 'Laptop', 'Laptop (New Price)')
WHERE products LIKE '%Laptop%';
-- Không chính xác, có thể update nhầm!
```

**Hậu quả:**
- Update không chính xác
- Phải update thủ công từng row

### **Investigation**

**Bước 1: Analyze data**

```sql
-- Tìm orders có products không nhất quán
SELECT id, products, quantities,
       LENGTH(products) - LENGTH(REPLACE(products, ',', '')) + 1 as product_count,
       LENGTH(quantities) - LENGTH(REPLACE(quantities, ',', '')) + 1 as quantity_count
FROM orders
WHERE (LENGTH(products) - LENGTH(REPLACE(products, ',', '')) + 1) != 
      (LENGTH(quantities) - LENGTH(REPLACE(quantities, ',', '')) + 1);
```

Kết quả: **200 orders** có products và quantities không khớp!

**Bước 2: Calculate duplication**

```sql
-- Tính số rows nếu normalize
SELECT 
  COUNT(*) as current_rows,
  SUM(LENGTH(products) - LENGTH(REPLACE(products, ',', '')) + 1) as normalized_rows,
  SUM(LENGTH(products) - LENGTH(REPLACE(products, ',', '')) + 1) / COUNT(*) as duplication_factor
FROM orders;
```

Kết quả:
- Current rows: 10,000
- Normalized rows: ~50,000
- **Duplication factor: 5x**

**Root cause:**
1. Vi phạm 1NF: Multiple values trong một cell
2. Khó maintain: Update/query phức tạp
3. Data inconsistency: Products và quantities không khớp

### **Fix**

**Fix 1: Normalize schema**

```sql
-- ✅ ĐÚNG: Tách thành bảng riêng
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMP
);

CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_name VARCHAR(100),
  quantity INT,
  price DECIMAL(10, 2),
  FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

**Fix 2: Migrate data**

```sql
-- Parse và migrate data
INSERT INTO orders (id, user_id, total_amount, created_at)
SELECT id, user_id, total_amount, created_at
FROM old_orders;

-- Parse products và quantities
INSERT INTO order_items (order_id, product_name, quantity, price)
SELECT 
  o.id as order_id,
  TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(o.products, ',', n.n), ',', -1)) as product_name,
  CAST(TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(o.quantities, ',', n.n), ',', -1)) AS INT) as quantity,
  0 as price  -- Will update later
FROM old_orders o
CROSS JOIN (
  SELECT 1 as n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5
) n
WHERE n.n <= (LENGTH(o.products) - LENGTH(REPLACE(o.products, ',', '')) + 1);
```

**Fix 3: Update application code**

```python
# ✅ ĐÚNG: Insert vào normalized tables
def create_order(user_id, items, total_amount):
    # Create order
    order_id = db.execute(
        "INSERT INTO orders (user_id, total_amount) VALUES (%s, %s) RETURNING id",
        [user_id, total_amount]
    )
    
    # Insert order items
    for item in items:
        db.execute(
            "INSERT INTO order_items (order_id, product_name, quantity, price) VALUES (%s, %s, %s, %s)",
            [order_id, item['product_name'], item['quantity'], item['price']]
        )
```

### **Kết quả**

✅ **Normalized schema**: Dữ liệu có cấu trúc rõ ràng
✅ **Easy queries**: Query đơn giản, chính xác
✅ **No duplication**: Không duplicate data
✅ **Data integrity**: Products và quantities luôn khớp
✅ **Easy maintenance**: Update/delete đơn giản

**Performance:**
- Query nhanh hơn (có thể index trên product_name)
- Storage hiệu quả hơn (không duplicate)
- Export chính xác (không duplicate rows)

### **Lesson Learned**

1. **LUÔN tuân thủ 1NF**: Mỗi cell = một giá trị atomic
2. **Tách non-atomic values**: Thành nhiều rows hoặc nhiều columns
3. **Consider business context**: Quyết định atomic dựa trên use case
4. **Normalize early**: Dễ hơn normalize sau khi có nhiều data
5. **Test queries**: Đảm bảo có thể query dữ liệu dễ dàng

---

## 6️⃣ BEST PRACTICES

### **6.1. Quy tắc 1NF**

1. **Mỗi cell = một giá trị atomic**
2. **Không có repeating groups**
3. **Mỗi row có Primary Key**

### **6.2. Khi nào tách non-atomic values?**

**Tách thành nhiều rows khi:**
- ✅ Có thể có nhiều values (one-to-many)
- ✅ Số lượng values không cố định
- ✅ Cần query/search trên values

**Tách thành nhiều columns khi:**
- ✅ Số lượng values cố định và nhỏ (ví dụ: 2-3)
- ✅ Mỗi column có ý nghĩa riêng (ví dụ: `first_name`, `last_name`)

### **6.3. JSON/Array trong 1NF**

**Có thể dùng JSON/Array nếu:**
- ✅ Schema linh hoạt (mỗi row có structure khác nhau)
- ✅ Rarely queried
- ✅ Document database pattern

**KHÔNG nên dùng nếu:**
- ❌ Fixed schema
- ❌ Frequently queried
- ❌ Need indexing

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **1NF yêu cầu**: Mỗi cell = một giá trị atomic, không có repeating groups
2. **Atomic values**: Giá trị không thể chia nhỏ có ý nghĩa
3. **Vi phạm 1NF**: Multiple values, repeating groups, comma-separated values
4. **Cách sửa**: Tách thành nhiều rows (tốt hơn) hoặc nhiều columns
5. **1NF là BẮT BUỘC**: Mọi table production đều phải tuân thủ

### **Best Practices**

✅ **Luôn tuân thủ 1NF**: Mỗi cell = một giá trị atomic
✅ **Tách non-atomic values**: Thành nhiều rows hoặc nhiều columns
✅ **Consider business context**: Quyết định atomic dựa trên use case
✅ **Normalize early**: Dễ hơn normalize sau khi có nhiều data
✅ **Test queries**: Đảm bảo có thể query dữ liệu dễ dàng

### **Câu hỏi tự kiểm tra**

1. 1NF là gì? Yêu cầu gì?
2. Atomic value là gì? Cho ví dụ atomic và không atomic
3. Tại sao cần 1NF?
4. Làm thế nào nhận biết vi phạm 1NF?
5. Cách sửa vi phạm 1NF?

---




**Chuẩn bị cho [Day-006: Normalization-2NF](Day-006-Normalization-2NF/theory.md)** 🚀
