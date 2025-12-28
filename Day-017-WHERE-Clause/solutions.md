# Day-017: Solutions - WHERE - Điều kiện lọc dữ liệu

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: WHERE Operators

**Operators:**
- `=` : Bằng
- `<>` hoặc `!=` : Khác
- `>` : Lớn hơn
- `<` : Nhỏ hơn
- `>=` : Lớn hơn hoặc bằng
- `<=` : Nhỏ hơn hoặc bằng

**Khi nào dùng:**
- `=` : So sánh bằng
- `<>` : So sánh khác
- `>`, `<`, `>=`, `<=` : So sánh số, date

---

### Câu 1.2: AND, OR, NOT

**a) AND, OR, NOT:**
- **AND**: Tất cả điều kiện đều đúng
- **OR**: Ít nhất một điều kiện đúng
- **NOT**: Phủ định điều kiện

**b) Operator precedence:**
- NOT > AND > OR

**c) Parentheses:**
- Làm rõ thứ tự logic
- Tránh nhầm lẫn

---

### Câu 1.3: NULL Handling

**a) Tại sao không dùng = NULL:**
- `NULL = NULL` → `NULL` (không phải true)
- WHERE chỉ match khi điều kiện = true

**b) IS NULL vs = NULL:**
- `IS NULL`: Đúng cách check NULL
- `= NULL`: Không hoạt động

**c) IS NOT NULL vs <> NULL:**
- `IS NOT NULL`: Đúng cách
- `<> NULL`: Không hoạt động

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: NULL trong WHERE

**a) Vấn đề:**
- `= NULL` không hoạt động
- Không trả về rows nào

**b) Query đúng:**
```sql
SELECT * FROM users WHERE email IS NULL;
```

---

### Câu 2.2: Operator Precedence

**a) Query được hiểu:**
```sql
status = 'active' OR (status = 'pending' AND age >= 18)
```

**b) Query rõ ràng:**
```sql
SELECT * FROM users 
WHERE (status = 'active' OR status = 'pending') AND age >= 18;
```

---

### Câu 2.3: Logic Error

**a) Vấn đề:**
- Không thể > 100 và < 50 cùng lúc
- Logic sai

**b) Query đúng:**
```sql
-- Nếu muốn > 100 hoặc < 50:
SELECT * FROM orders 
WHERE total_amount > 100 OR total_amount < 50;

-- Nếu muốn BETWEEN:
SELECT * FROM orders 
WHERE total_amount BETWEEN 50 AND 100;
```

---

## 🧠 BÀI TẬP 3: THỰC HÀNH

### Câu 3.1: Viết WHERE Queries

**a)**
```sql
SELECT * FROM users WHERE status = 'active';
```

**b)**
```sql
SELECT * FROM users WHERE age >= 18;
```

**c)**
```sql
SELECT * FROM users WHERE status = 'active' AND age >= 18;
```

---

### Câu 3.2: Complex WHERE

**a)**
```sql
SELECT * FROM orders 
WHERE total_amount > 100 AND status = 'completed';
```

**b)**
```sql
SELECT * FROM orders 
WHERE status = 'pending' OR status = 'processing';
```

**c)**
```sql
SELECT * FROM orders 
WHERE total_amount > 100 
  AND (status = 'pending' OR status = 'processing');
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: WHERE Performance

**a) Ảnh hưởng:**
- WHERE giảm số rows cần xử lý
- Indexes giúp WHERE nhanh hơn

**b) Optimize:**
- Dùng indexed columns trong WHERE
- Tránh functions trong WHERE

**c) Best practices:**
- Dùng WHERE với indexed columns
- Tránh functions trong WHERE
- Test performance

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

**Đáp án tham khảo:**

1. **Operators**: =, <>, >, <, >=, <=
2. **AND, OR, NOT**: Logic operators
3. **Precedence**: NOT > AND > OR
4. **NULL**: Dùng IS NULL/IS NOT NULL

---

**Chúc mừng hoàn thành Day-017!** 🎉

