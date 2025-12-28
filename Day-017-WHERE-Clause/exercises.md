# Day-017: Bài Tập - WHERE - Điều kiện lọc dữ liệu

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về WHERE clause. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: WHERE Operators

**Câu hỏi:** Hãy giải thích các operators:
- =, <>, >, <, >=, <=
- Khi nào dùng mỗi operator?

---

### Câu 1.2: AND, OR, NOT

**Câu hỏi:**

a) AND là gì? OR là gì? NOT là gì?

b) Operator precedence là gì?

c) Tại sao nên dùng parentheses?

---

### Câu 1.3: NULL Handling

**Câu hỏi:**

a) Tại sao không dùng = NULL?

b) IS NULL vs = NULL - khác biệt?

c) IS NOT NULL vs <> NULL - khác biệt?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: NULL trong WHERE

**Tình huống:**

Query (SAI):

```sql
SELECT * FROM users WHERE email = NULL;
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Query đúng là gì?

---

### Câu 2.2: Operator Precedence

**Tình huống:**

Query không rõ ràng:

```sql
SELECT * FROM users 
WHERE status = 'active' OR status = 'pending' AND age >= 18;
```

**Câu hỏi:**

a) Query này được hiểu như thế nào?

b) Viết lại query rõ ràng hơn (dùng parentheses).

---

### Câu 2.3: Logic Error

**Tình huống:**

Query logic sai:

```sql
SELECT * FROM orders 
WHERE total_amount > 100 AND total_amount < 50;
-- ❌ Không thể > 100 và < 50 cùng lúc!
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Viết lại query đúng.

---

## 🧠 BÀI TẬP 3: THỰC HÀNH

### Câu 3.1: Viết WHERE Queries

**Yêu cầu:**

Table `users` có: `id`, `email`, `name`, `age`, `status`.

**Câu hỏi:**

a) Lấy users có status = 'active'.

b) Lấy users có age >= 18.

c) Lấy users có status = 'active' AND age >= 18.

---

### Câu 3.2: Complex WHERE

**Yêu cầu:**

Table `orders` có: `id`, `user_id`, `total_amount`, `status`, `created_at`.

**Câu hỏi:**

a) Lấy orders có total_amount > 100 AND status = 'completed'.

b) Lấy orders có status = 'pending' OR status = 'processing'.

c) Lấy orders có total_amount > 100 AND (status = 'pending' OR status = 'processing').

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: WHERE Performance

**Câu hỏi:**

a) WHERE ảnh hưởng đến performance như thế nào?

b) Làm thế nào optimize WHERE queries?

c) Best practices?

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời (không xem lại lý thuyết):

1. WHERE operators là gì?
2. AND, OR, NOT là gì?
3. Operator precedence?
4. NULL handling trong WHERE?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

