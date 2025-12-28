# Day-016: Bài Tập - SELECT - Câu lệnh cơ bản nhất

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về SELECT. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: SELECT là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- SELECT là gì?
- Tại sao SELECT quan trọng?
- SELECT * vs SELECT column_list - khi nào dùng gì?

---

### Câu 1.2: FROM Clause

**Câu hỏi:**

a) FROM clause là gì?

b) Tại sao FROM clause quan trọng?

c) Có thể query nhiều tables không?

---

### Câu 1.3: WHERE Clause

**Câu hỏi:**

a) WHERE clause là gì?

b) Tại sao WHERE clause quan trọng?

c) Khi nào nên dùng WHERE clause?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: SELECT * trong Production

**Tình huống:**

Code hiện tại (SAI):

```sql
-- ❌ SAI: Dùng SELECT * trong production
SELECT * FROM orders WHERE user_id = 12345;
```

**Table orders có 20 columns, mỗi row ~5KB.**

**Câu hỏi:**

a) Phân tích vấn đề với query này.

b) Hậu quả nếu dùng query này trong production?

c) Viết lại query đúng (chỉ SELECT columns cần thiết).

---

### Câu 2.2: SELECT không có WHERE

**Tình huống:**

Query không có WHERE clause:

```sql
SELECT id, email, name FROM users;
-- ❌ Không có WHERE → lấy tất cả rows
```

**Table users có 10 triệu rows.**

**Câu hỏi:**

a) Phân tích vấn đề.

b) Hậu quả nếu dùng query này?

c) Làm thế nào fix? (thêm WHERE hoặc LIMIT)

---

### Câu 2.3: SELECT columns không tồn tại

**Tình huống:**

Query SELECT columns không tồn tại:

```sql
SELECT id, email, full_name FROM users;
-- ❌ Column 'full_name' không tồn tại (column đúng là 'name')
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Database sẽ trả về gì?

c) Làm thế nào fix?

---

## 🧠 BÀI TẬP 3: THỰC HÀNH

### Câu 3.1: Viết SELECT Queries

**Yêu cầu:**

Table `users` có columns: `id`, `email`, `name`, `created_at`, `updated_at`, `status`.

**Câu hỏi:**

a) Viết query lấy tất cả users.

b) Viết query lấy chỉ `id`, `email`, `name`.

c) Viết query lấy users có `status = 'active'`.

---

### Câu 3.2: SELECT với WHERE

**Yêu cầu:**

Table `orders` có columns: `id`, `user_id`, `total_amount`, `created_at`, `status`.

**Câu hỏi:**

a) Viết query lấy orders của user_id = 123.

b) Viết query lấy chỉ `id`, `total_amount`, `status` của orders có `total_amount > 100`.

c) Viết query lấy orders được tạo trong tháng này.

---

### Câu 3.3: SELECT Best Practices

**Yêu cầu:**

Review các queries sau và cải thiện:

**Query 1:**
```sql
SELECT * FROM products;
```

**Query 2:**
```sql
SELECT * FROM orders WHERE user_id = 12345;
```

**Query 3:**
```sql
SELECT id, email, name, created_at, updated_at, status, metadata, notes
FROM users
WHERE id = 1;
-- Nhưng chỉ cần id, email, name
```

**Câu hỏi:**

a) Phân tích các queries.

b) Cải thiện các queries.

c) Giải thích improvements.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: SELECT * vs SELECT column_list

**Tình huống:**

Bạn có 2 options:

**Option A: SELECT ***
- Đơn giản, nhanh viết
- Nhưng có thể chậm

**Option B: SELECT column_list**
- Rõ ràng, performance tốt
- Nhưng phải liệt kê columns

**Câu hỏi:**

a) So sánh 2 options về:
   - Performance
   - Maintainability
   - Network usage
   - Memory usage

b) Khi nào nên dùng Option A? Khi nào nên dùng Option B?

c) Best practices?

---

### Câu 4.2: SELECT Performance

**Câu hỏi:**

a) SELECT ảnh hưởng đến performance như thế nào?

b) Làm thế nào optimize SELECT queries?

c) Best practices cho SELECT performance?

---

### Câu 4.3: SELECT và Schema Changes

**Câu hỏi:**

a) SELECT * ảnh hưởng đến schema changes như thế nào?

b) SELECT column_list ảnh hưởng đến schema changes như thế nào?

c) Best practices khi schema thay đổi?

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. SELECT là gì? Tại sao quan trọng?

2. SELECT * vs SELECT column_list - khi nào dùng gì?

3. FROM clause là gì?

4. WHERE clause là gì?

5. Tại sao SELECT * có thể làm query chậm?

---

### Câu 5.2: Áp dụng thực tế

Tưởng tượng bạn đang viết API endpoint để lấy user information:

**Yêu cầu:**

a) Viết SELECT query (chỉ lấy id, email, name).

b) Thêm WHERE clause để filter.

c) Giải thích tại sao không dùng SELECT *.

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- SELECT là câu lệnh cơ bản nhất, nhưng rất quan trọng
- Senior SQL Engineer luôn dùng SELECT column_list trong production

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

