# Day-017: WHERE - Điều kiện lọc dữ liệu

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- WHERE với operators (=, <>, >, <, >=, <=)
- AND, OR, NOT - logic operators
- Operator precedence - thứ tự ưu tiên
- NULL handling (IS NULL, IS NOT NULL)
- Hậu quả nếu xử lý NULL sai trong WHERE clause

---

## 1️⃣ WHERE VỚI OPERATORS

### **Comparison Operators**

**Các operators cơ bản:**

- `=` : Bằng
- `<>` hoặc `!=` : Khác
- `>` : Lớn hơn
- `<` : Nhỏ hơn
- `>=` : Lớn hơn hoặc bằng
- `<=` : Nhỏ hơn hoặc bằng

**Ví dụ:**

```sql
-- Bằng
SELECT * FROM users WHERE email = 'john@example.com';

-- Khác
SELECT * FROM users WHERE status <> 'deleted';

-- Lớn hơn
SELECT * FROM orders WHERE total_amount > 100;

-- Nhỏ hơn hoặc bằng
SELECT * FROM products WHERE price <= 50;
```

---

## 2️⃣ AND, OR, NOT

### **AND - Tất cả điều kiện đều đúng**

```sql
SELECT * FROM users 
WHERE status = 'active' AND age >= 18;
```

### **OR - Ít nhất một điều kiện đúng**

```sql
SELECT * FROM users 
WHERE status = 'active' OR status = 'pending';
```

### **NOT - Phủ định điều kiện**

```sql
SELECT * FROM users 
WHERE NOT status = 'deleted';
```

### **Kết hợp AND, OR, NOT**

```sql
SELECT * FROM users 
WHERE (status = 'active' OR status = 'pending')
  AND age >= 18
  AND NOT email IS NULL;
```

---

## 3️⃣ OPERATOR PRECEDENCE

**Thứ tự ưu tiên:**
1. `NOT`
2. `AND`
3. `OR`

**Ví dụ:**

```sql
-- Query này:
SELECT * FROM users 
WHERE status = 'active' OR status = 'pending' AND age >= 18;

-- Được hiểu là:
SELECT * FROM users 
WHERE status = 'active' OR (status = 'pending' AND age >= 18);
-- NOT: (status = 'active' OR status = 'pending') AND age >= 18

-- Nên dùng parentheses để rõ ràng:
SELECT * FROM users 
WHERE (status = 'active' OR status = 'pending') AND age >= 18;
```

---

## 4️⃣ NULL HANDLING

### **IS NULL - Kiểm tra NULL**

```sql
-- ❌ SAI: NULL không bằng bất cứ gì (kể cả NULL)
SELECT * FROM users WHERE email = NULL;  -- Không trả về gì!

-- ✅ ĐÚNG: Dùng IS NULL
SELECT * FROM users WHERE email IS NULL;
```

### **IS NOT NULL - Kiểm tra không phải NULL**

```sql
SELECT * FROM users WHERE email IS NOT NULL;
```

### **Tại sao không dùng = NULL?**

**Lý do:**
- `NULL = NULL` → `NULL` (không phải true)
- `NULL = 5` → `NULL` (không phải false)
- WHERE chỉ trả về rows có điều kiện = `true`
- `NULL` không phải `true` → không match

---

## 5️⃣ PRODUCTION STORY: BUG DO NULL TRONG WHERE CLAUSE

### **Context**

Payment system có query:

```sql
-- ❌ SAI: Không xử lý NULL
SELECT COUNT(*) as total_payments
FROM payments
WHERE amount > 0;
```

### **Vấn đề**

- Một số payments có `amount = NULL`
- Query không đếm payments có `amount = NULL`
- **Kết quả**: Số liệu không chính xác

### **Fix**

```sql
-- ✅ ĐÚNG: Xử lý NULL
SELECT COUNT(*) as total_payments
FROM payments
WHERE amount > 0 OR amount IS NULL;
```

---

## 6️⃣ BEST PRACTICES

✅ **Dùng parentheses**: Làm rõ thứ tự logic
✅ **Xử lý NULL**: Luôn dùng IS NULL/IS NOT NULL
✅ **Index-friendly**: Dùng WHERE với indexed columns
✅ **Test với NULL**: Test queries với NULL values

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Operators**: =, <>, >, <, >=, <=
2. **Logic**: AND, OR, NOT
3. **Precedence**: NOT > AND > OR
4. **NULL**: Luôn dùng IS NULL/IS NOT NULL
5. **Best practice**: Dùng parentheses để rõ ràng

---

**Chuẩn bị cho Day-018: ORDER BY - Sắp xếp kết quả** 🚀
