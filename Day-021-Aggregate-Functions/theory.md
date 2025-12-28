# Day-021: Aggregate Functions - Tổng hợp dữ liệu

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- COUNT, SUM, AVG, MIN, MAX là gì?
- COUNT(*) vs COUNT(column) vs COUNT(DISTINCT column)
- NULL handling trong aggregate functions
- Khi nào dùng aggregate functions?
- Hậu quả nếu xử lý NULL sai trong aggregate

---

## 1️⃣ AGGREGATE FUNCTIONS LÀ GÌ?

### **Nó là gì?**

**Aggregate Functions** (Hàm tổng hợp) là các hàm tính toán trên **nhiều rows** và trả về **một giá trị**:

- **COUNT**: Đếm số rows
- **SUM**: Tổng
- **AVG**: Trung bình
- **MIN**: Giá trị nhỏ nhất
- **MAX**: Giá trị lớn nhất

**Ví dụ:**

```sql
-- Đếm số users
SELECT COUNT(*) FROM users;

-- Tổng tiền orders
SELECT SUM(total_amount) FROM orders;

-- Trung bình giá products
SELECT AVG(price) FROM products;
```

### **Tại sao tồn tại?**

Aggregate functions tồn tại để:

1. **Tổng hợp dữ liệu**: Tính toán trên nhiều rows
2. **Statistics**: Thống kê (count, average, etc.)
3. **Reports**: Tạo báo cáo
4. **Analytics**: Phân tích dữ liệu

### **Khi nào dùng trong production?**

Aggregate functions được dùng khi:

✅ **Statistics**: Đếm, tổng, trung bình
✅ **Reports**: Báo cáo tổng hợp
✅ **Analytics**: Phân tích dữ liệu
✅ **Dashboards**: Hiển thị metrics

---

## 2️⃣ COUNT, SUM, AVG, MIN, MAX

### **COUNT - Đếm số rows**

```sql
-- Đếm tất cả rows
SELECT COUNT(*) FROM users;

-- Đếm rows có email không NULL
SELECT COUNT(email) FROM users;
```

### **SUM - Tổng**

```sql
-- Tổng tiền orders
SELECT SUM(total_amount) FROM orders;
```

### **AVG - Trung bình**

```sql
-- Trung bình giá products
SELECT AVG(price) FROM products;
```

### **MIN, MAX - Giá trị nhỏ nhất/lớn nhất**

```sql
-- Giá nhỏ nhất
SELECT MIN(price) FROM products;

-- Giá lớn nhất
SELECT MAX(price) FROM products;
```

---

## 3️⃣ COUNT(*) VS COUNT(COLUMN) VS COUNT(DISTINCT COLUMN)

### **COUNT(*)**

**COUNT(*)** đếm **tất cả rows** (kể cả NULL):

```sql
SELECT COUNT(*) FROM users;
-- Đếm tất cả rows trong table users
```

### **COUNT(column)**

**COUNT(column)** đếm **rows có column không NULL**:

```sql
SELECT COUNT(email) FROM users;
-- Chỉ đếm rows có email không NULL
```

### **COUNT(DISTINCT column)**

**COUNT(DISTINCT column)** đếm **số giá trị unique**:

```sql
SELECT COUNT(DISTINCT status) FROM orders;
-- Đếm số status unique
```

### **So sánh**

| Function | Đếm gì? |
|----------|---------|
| `COUNT(*)` | Tất cả rows (kể cả NULL) |
| `COUNT(column)` | Rows có column không NULL |
| `COUNT(DISTINCT column)` | Số giá trị unique |

---

## 4️⃣ NULL HANDLING TRONG AGGREGATE

### **NULL trong Aggregate Functions**

**Quy tắc:**
- **COUNT(*)**: Đếm tất cả rows (kể cả NULL)
- **COUNT(column)**: Bỏ qua NULL
- **SUM, AVG, MIN, MAX**: Bỏ qua NULL

**Ví dụ:**

```sql
-- Table users có 10 rows, 2 rows có age = NULL
SELECT COUNT(*) FROM users;        -- 10
SELECT COUNT(age) FROM users;      -- 8 (bỏ qua 2 NULL)
SELECT AVG(age) FROM users;        -- Trung bình của 8 rows (bỏ qua NULL)
```

---

## 5️⃣ PRODUCTION STORY: COUNT SAI DO NULL

### **Context**

Payment system có query:

```sql
-- ❌ SAI: COUNT(amount) bỏ qua NULL
SELECT COUNT(amount) as total_payments
FROM payments
WHERE status = 'completed';
```

### **Vấn đề**

- Một số payments có `amount = NULL`
- COUNT(amount) không đếm payments có amount = NULL
- **Kết quả**: Số liệu không chính xác

### **Fix**

```sql
-- ✅ ĐÚNG: COUNT(*) đếm tất cả rows
SELECT COUNT(*) as total_payments
FROM payments
WHERE status = 'completed';
```

---

## 6️⃣ BEST PRACTICES

✅ **COUNT(*)**: Dùng khi cần đếm tất cả rows
✅ **COUNT(column)**: Dùng khi chỉ đếm rows có giá trị
✅ **NULL handling**: Hiểu cách aggregate xử lý NULL
✅ **Test với NULL**: Test queries với NULL values

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Aggregate functions**: COUNT, SUM, AVG, MIN, MAX
2. **COUNT(*) vs COUNT(column)**: COUNT(*) đếm tất cả, COUNT(column) bỏ qua NULL
3. **NULL handling**: Aggregate functions bỏ qua NULL (trừ COUNT(*))
4. **Best practice**: Hiểu cách aggregate xử lý NULL

---




**Chuẩn bị cho [Day-022: GROUP-BY](Day-022-GROUP-BY/theory.md)** 🚀
