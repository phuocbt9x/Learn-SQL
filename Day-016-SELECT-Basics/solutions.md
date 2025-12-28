# Day-016: Solutions - SELECT - Câu lệnh cơ bản nhất

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: SELECT là gì?

**SELECT là gì?**

**Đáp án:** SELECT là câu lệnh SQL cơ bản nhất, dùng để lấy dữ liệu từ database.

**Tại sao quan trọng?**

**Lý do:**
- SELECT là câu lệnh được dùng nhiều nhất
- Hầu hết mọi thứ với database đều bắt đầu từ SELECT
- Read-only operation - không thay đổi dữ liệu

**SELECT * vs SELECT column_list:**

- **SELECT ***: Lấy tất cả columns - đơn giản nhưng có thể chậm
- **SELECT column_list**: Chỉ lấy columns cần thiết - rõ ràng và performance tốt hơn

**Khi nào dùng:**
- **SELECT ***: Development/testing, quick queries
- **SELECT column_list**: Production (luôn luôn!)

---

### Câu 1.2: FROM Clause

**a) FROM clause là gì?**

**Đáp án:** FROM clause chỉ định table để lấy dữ liệu.

**b) Tại sao quan trọng?**

**Lý do:**
- Specify table: Chỉ định table cần query
- Multiple tables: Có thể query nhiều tables (JOIN)
- Aliases: Có thể đặt alias cho table

**c) Có thể query nhiều tables không?**

**Đáp án: CÓ** - Dùng JOIN (sẽ học ở Day-018)

---

### Câu 1.3: WHERE Clause

**a) WHERE clause là gì?**

**Đáp án:** WHERE clause dùng để lọc dữ liệu theo điều kiện.

**b) Tại sao quan trọng?**

**Lý do:**
- Filter data: Lọc dữ liệu theo điều kiện
- Performance: Giảm số rows cần xử lý
- Precision: Lấy đúng dữ liệu cần thiết

**c) Khi nào nên dùng?**

**Đáp án: LUÔN dùng** khi cần filter data.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: SELECT * trong Production

**a) Phân tích vấn đề:**

**Vấn đề:**
- SELECT * lấy tất cả 20 columns
- Mỗi row ~5KB
- 100 rows = 500KB data
- Tốn bandwidth và memory không cần thiết

**b) Hậu quả:**

- Query chậm (5-10 giây)
- Tốn bandwidth (500KB)
- Tốn memory
- Poor user experience

**c) Query đúng:**

```sql
-- ✅ ĐÚNG: Chỉ SELECT columns cần thiết
SELECT id, user_id, total_amount, created_at, status
FROM orders
WHERE user_id = 12345;
```

---

### Câu 2.2: SELECT không có WHERE

**a) Phân tích vấn đề:**

**Vấn đề:**
- Không có WHERE → lấy tất cả 10 triệu rows
- Tốn rất nhiều bandwidth và memory
- Có thể làm database crash

**b) Hậu quả:**

- Query rất chậm hoặc timeout
- Tốn rất nhiều bandwidth
- Có thể làm database crash
- Poor performance

**c) Fix:**

**Option 1: Thêm WHERE**
```sql
SELECT id, email, name FROM users WHERE status = 'active';
```

**Option 2: Thêm LIMIT**
```sql
SELECT id, email, name FROM users LIMIT 100;
```

---

### Câu 2.3: SELECT columns không tồn tại

**a) Phân tích vấn đề:**

**Vấn đề:**
- Column 'full_name' không tồn tại
- Column đúng là 'name'

**b) Database sẽ trả về gì?**

**Đáp án: ERROR** - Database sẽ báo lỗi: "column 'full_name' does not exist"

**c) Fix:**

```sql
SELECT id, email, name FROM users;
--                    ^^^^ column đúng
```

---

## 🧠 BÀI TẬP 3: THỰC HÀNH

### Câu 3.1: Viết SELECT Queries

**a) Lấy tất cả users:**

```sql
SELECT id, email, name, created_at, updated_at, status
FROM users;
```

**b) Chỉ lấy id, email, name:**

```sql
SELECT id, email, name
FROM users;
```

**c) Lấy users có status = 'active':**

```sql
SELECT id, email, name
FROM users
WHERE status = 'active';
```

---

### Câu 3.2: SELECT với WHERE

**a) Orders của user_id = 123:**

```sql
SELECT id, user_id, total_amount, created_at, status
FROM orders
WHERE user_id = 123;
```

**b) Chỉ id, total_amount, status với total_amount > 100:**

```sql
SELECT id, total_amount, status
FROM orders
WHERE total_amount > 100;
```

**c) Orders trong tháng này:**

```sql
SELECT id, user_id, total_amount, created_at, status
FROM orders
WHERE created_at >= DATE_TRUNC('month', CURRENT_DATE)
  AND created_at < DATE_TRUNC('month', CURRENT_DATE) + INTERVAL '1 month';
```

---

### Câu 3.3: SELECT Best Practices

**Query 1:**

```sql
-- ❌ SAI: SELECT *
SELECT * FROM products;

-- ✅ ĐÚNG: Chỉ SELECT columns cần thiết
SELECT id, name, price FROM products;
```

**Query 2:**

```sql
-- ❌ SAI: SELECT *
SELECT * FROM orders WHERE user_id = 12345;

-- ✅ ĐÚNG: Chỉ SELECT columns cần thiết
SELECT id, user_id, total_amount, created_at, status
FROM orders
WHERE user_id = 12345;
```

**Query 3:**

```sql
-- ❌ SAI: SELECT quá nhiều columns không cần
SELECT id, email, name, created_at, updated_at, status, metadata, notes
FROM users
WHERE id = 1;

-- ✅ ĐÚNG: Chỉ SELECT columns cần thiết
SELECT id, email, name
FROM users
WHERE id = 1;
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: SELECT * vs SELECT column_list

**a) So sánh:**

| Tiêu chí | SELECT * | SELECT column_list |
|----------|----------|-------------------|
| **Performance** | Có thể chậm | Nhanh hơn |
| **Maintainability** | Khó maintain | Dễ maintain |
| **Network** | Tốn bandwidth | Tiết kiệm bandwidth |
| **Memory** | Tốn memory | Tiết kiệm memory |

**b) Khi nào dùng:**

**SELECT *:**
- Development/testing
- Quick queries
- Table nhỏ, ít columns

**SELECT column_list:**
- Production (luôn luôn!)
- Table lớn, nhiều columns
- Performance quan trọng

**c) Best practices:**

- **LUÔN dùng SELECT column_list trong production**
- Chỉ dùng SELECT * trong development/testing
- Monitor query performance

---

### Câu 4.2: SELECT Performance

**a) Ảnh hưởng đến performance:**

- **Data size**: SELECT nhiều columns → nhiều data → chậm hơn
- **Network**: Nhiều data → tốn bandwidth
- **Memory**: Nhiều data → tốn memory

**b) Optimize:**

- **Chỉ SELECT columns cần thiết**
- **Use WHERE**: Filter data
- **Use LIMIT**: Giới hạn số rows
- **Indexes**: Dùng indexes cho WHERE clause

**c) Best practices:**

- Luôn dùng SELECT column_list
- Luôn dùng WHERE khi có thể
- Monitor query performance

---

### Câu 4.3: SELECT và Schema Changes

**a) SELECT * ảnh hưởng:**

- **Schema changes**: Khi schema thay đổi, SELECT * tự động lấy columns mới
- **Breaking changes**: Có thể break application nếu không expect columns mới

**b) SELECT column_list ảnh hưởng:**

- **Explicit**: Rõ ràng columns nào được dùng
- **Breaking changes**: Nếu column bị xóa → error ngay lập tức (tốt hơn!)

**c) Best practices:**

- **SELECT column_list**: Explicit, dễ maintain
- **Review schema changes**: Review khi schema thay đổi
- **Test**: Test queries sau khi schema thay đổi

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **SELECT**: Câu lệnh cơ bản nhất để lấy dữ liệu
2. **SELECT * vs column_list**: SELECT * đơn giản nhưng chậm, SELECT column_list tốt hơn
3. **FROM clause**: Chỉ định table để query
4. **WHERE clause**: Lọc dữ liệu theo điều kiện
5. **SELECT * chậm**: Vì lấy nhiều data không cần thiết

---

### Câu 5.2: Áp dụng thực tế

**a) SELECT query:**

```sql
SELECT id, email, name
FROM users
WHERE id = 123;
```

**b) Thêm WHERE:**

```sql
SELECT id, email, name
FROM users
WHERE id = 123 AND status = 'active';
```

**c) Tại sao không dùng SELECT *:**

- **Performance**: Chỉ lấy 3 columns thay vì tất cả
- **Network**: Tiết kiệm bandwidth
- **Maintainability**: Rõ ràng columns nào được dùng

---

## 📝 TÓM TẮT

### Key Learnings

1. **SELECT**: Câu lệnh cơ bản nhất để lấy dữ liệu
2. **SELECT * vs column_list**: SELECT * đơn giản nhưng chậm, SELECT column_list tốt hơn
3. **FROM clause**: Chỉ định table để query
4. **WHERE clause**: Lọc dữ liệu theo điều kiện
5. **Production best practice**: LUÔN dùng SELECT column_list trong production

### Best Practices

✅ **Luôn dùng SELECT column_list**: Trong production
✅ **Tránh SELECT ***: Chỉ dùng trong development/testing
✅ **Use WHERE**: Luôn filter data khi có thể
✅ **Monitor performance**: Monitor query performance

---

**Chúc mừng hoàn thành Day-016!** 🎉

**Chuẩn bị cho Day-017: WHERE - Điều kiện lọc dữ liệu** 🚀

