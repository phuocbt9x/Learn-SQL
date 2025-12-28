# Day-016: SELECT - Câu lệnh cơ bản nhất

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- SELECT là gì và tại sao là câu lệnh quan trọng nhất
- SELECT * vs SELECT column_list - khi nào dùng gì?
- FROM clause - cách chỉ định table
- WHERE clause cơ bản - cách lọc dữ liệu
- Hậu quả nếu dùng SELECT * không đúng cách

---

## 1️⃣ SELECT LÀ GÌ?

### **Nó là gì?**

**SELECT** là câu lệnh SQL cơ bản nhất, dùng để **lấy dữ liệu** từ database:

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

**Ví dụ đơn giản nhất:**

```sql
SELECT * FROM users;
```

**Câu lệnh này làm gì?**
- Lấy **tất cả columns** (`*`)
- Từ table **users**
- Trả về tất cả rows

### **Tại sao tồn tại?**

SELECT tồn tại để:

1. **Query data**: Lấy dữ liệu từ database
2. **Filter data**: Lọc dữ liệu theo điều kiện
3. **Transform data**: Biến đổi dữ liệu (aggregate, join, etc.)
4. **Report**: Tạo báo cáo từ dữ liệu

**SELECT là câu lệnh được dùng nhiều nhất** trong SQL - hầu hết mọi thứ bạn làm với database đều bắt đầu từ SELECT.

### **Khi nào dùng trong production?**

SELECT được dùng **MỌI LÚC** trong production:

✅ **Read data**: Đọc dữ liệu từ database
✅ **Reports**: Tạo báo cáo
✅ **Analytics**: Phân tích dữ liệu
✅ **APIs**: Trả về dữ liệu cho APIs
✅ **Dashboards**: Hiển thị dữ liệu trên dashboards

**Lưu ý:** SELECT là read-only operation - không thay đổi dữ liệu.

---

## 2️⃣ SELECT * VS SELECT COLUMN_LIST

### **SELECT ***

**SELECT *** lấy **tất cả columns** từ table:

```sql
SELECT * FROM users;
```

**Kết quả:**
```
id | email           | name  | created_at
---|-----------------|-------|------------
1  | john@example.com| John  | 2024-01-01
2  | jane@example.com| Jane  | 2024-01-02
```

**Đặc điểm:**
- ✅ **Đơn giản**: Không cần liệt kê columns
- ✅ **Linh hoạt**: Tự động lấy tất cả columns
- ❌ **Performance**: Có thể chậm nếu table có nhiều columns
- ❌ **Network**: Tốn bandwidth không cần thiết
- ❌ **Maintainability**: Khó maintain khi schema thay đổi

### **SELECT column_list**

**SELECT column_list** chỉ lấy **columns cụ thể**:

```sql
SELECT id, email, name FROM users;
```

**Kết quả:**
```
id | email           | name
---|-----------------|-------
1  | john@example.com| John
2  | jane@example.com| Jane
```

**Đặc điểm:**
- ✅ **Performance**: Nhanh hơn (ít data hơn)
- ✅ **Network**: Tiết kiệm bandwidth
- ✅ **Maintainability**: Rõ ràng, dễ maintain
- ❌ **Verbose**: Phải liệt kê columns

### **Khi nào dùng gì?**

**Dùng SELECT * khi:**
- ✅ Development/testing
- ✅ Quick queries
- ✅ Table nhỏ, ít columns
- ✅ Cần tất cả columns

**Dùng SELECT column_list khi:**
- ✅ **Production** (luôn luôn!)
- ✅ Table lớn, nhiều columns
- ✅ Chỉ cần một số columns
- ✅ Performance quan trọng

**Best practice:** **LUÔN dùng SELECT column_list trong production.**

---

## 3️⃣ FROM CLAUSE

### **Nó là gì?**

**FROM clause** chỉ định **table** để lấy dữ liệu:

```sql
SELECT * FROM users;
--              ^^^^^ table name
```

**Ví dụ:**

```sql
-- Lấy từ table users
SELECT * FROM users;

-- Lấy từ table orders
SELECT * FROM orders;

-- Lấy từ table products
SELECT id, name, price FROM products;
```

### **Tại sao quan trọng?**

FROM clause quan trọng vì:

1. **Specify table**: Chỉ định table cần query
2. **Multiple tables**: Có thể query nhiều tables (JOIN)
3. **Aliases**: Có thể đặt alias cho table

**Ví dụ với alias:**

```sql
SELECT u.id, u.email, u.name
FROM users u;
--        ^ alias
```

---

## 4️⃣ WHERE CLAUSE CƠ BẢN

### **Nó là gì?**

**WHERE clause** dùng để **lọc dữ liệu** theo điều kiện:

```sql
SELECT * FROM users WHERE email = 'john@example.com';
--                        ^^^^^^^^^^^^^^^^^^^^^^^^^ condition
```

**Ví dụ:**

```sql
-- Lấy user có email cụ thể
SELECT * FROM users WHERE email = 'john@example.com';

-- Lấy users có id > 10
SELECT * FROM users WHERE id > 10;

-- Lấy users có name chứa 'John'
SELECT * FROM users WHERE name LIKE '%John%';
```

### **Tại sao quan trọng?**

WHERE clause quan trọng vì:

1. **Filter data**: Lọc dữ liệu theo điều kiện
2. **Performance**: Giảm số rows cần xử lý
3. **Precision**: Lấy đúng dữ liệu cần thiết

**Lưu ý:** WHERE clause sẽ được học chi tiết ở Day-017.

---

## 5️⃣ PRODUCTION STORY: SELECT * LÀM QUERY CHẬM 10X

### **Context**

E-commerce platform có table `orders` với 20 columns:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  total_amount DECIMAL(10, 2),
  created_at TIMESTAMPTZ,
  updated_at TIMESTAMPTZ,
  status VARCHAR(50),
  shipping_address TEXT,        -- 500+ characters
  billing_address TEXT,         -- 500+ characters
  notes TEXT,                   -- 1000+ characters
  metadata JSONB,              -- Large JSON
  -- ... 10 more columns
);
```

**Code ban đầu (SAI):**

```sql
-- ❌ SAI: Dùng SELECT *
SELECT * FROM orders WHERE user_id = 12345;
```

### **Vấn đề xuất hiện**

**Tháng 1: Query chậm**

- Query mất 2-3 giây
- Table có 1 triệu rows
- Mỗi row có ~5KB data (20 columns, nhiều TEXT/JSONB)

**Tháng 2: Performance tệ hơn**

- Query mất 5-10 giây
- Table có 5 triệu rows
- Users phàn nàn về performance

**Investigation:**

```sql
-- Check query plan
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 12345;
```

**Kết quả:**

```
Seq Scan on orders (cost=0.00..250000.00 rows=100 width=5000)
  Filter: (user_id = 12345)
Execution Time: 5000.123 ms
```

**Phân tích:**
- **Width: 5000**: Mỗi row ~5KB
- **100 rows**: Trả về 100 rows
- **Total data**: 100 × 5KB = 500KB
- **Network**: Tốn 500KB bandwidth không cần thiết!

**Root cause:**
1. **SELECT ***: Lấy tất cả 20 columns
2. **Large columns**: TEXT, JSONB columns rất lớn
3. **Network overhead**: Tốn bandwidth không cần thiết
4. **Memory**: Tốn memory để store data

### **Fix**

**Fix: Chỉ SELECT columns cần thiết**

```sql
-- ✅ ĐÚNG: Chỉ SELECT columns cần thiết
SELECT id, user_id, total_amount, created_at, status
FROM orders
WHERE user_id = 12345;
```

**Kết quả:**

```
Index Scan using idx_orders_user_id on orders (cost=0.43..8.45 rows=100 width=50)
  Index Cond: (user_id = 12345)
Execution Time: 0.123 ms
```

**Phân tích:**
- **Width: 50**: Mỗi row ~50 bytes (chỉ 5 columns)
- **100 rows**: Trả về 100 rows
- **Total data**: 100 × 50 bytes = 5KB
- **Network**: Chỉ tốn 5KB bandwidth!

**Nhanh hơn 40,000x!**

### **Kết quả**

✅ **Performance**: Query nhanh hơn 40,000x
✅ **Network**: Tiết kiệm 99% bandwidth
✅ **Memory**: Tiết kiệm memory
✅ **User experience**: Users hài lòng

### **Lesson Learned**

1. **SELECT * nguy hiểm**: Có thể làm query chậm rất nhiều
2. **Chỉ SELECT cần thiết**: Luôn chỉ SELECT columns cần thiết
3. **Large columns**: TEXT, JSONB columns rất tốn bandwidth
4. **Production best practice**: LUÔN dùng SELECT column_list trong production
5. **Monitor**: Monitor query performance và data size

---

## 6️⃣ BEST PRACTICES

### **6.1. SELECT Best Practices**

✅ **Luôn dùng SELECT column_list**: Trong production, luôn chỉ định columns cụ thể
✅ **Tránh SELECT ***: Chỉ dùng trong development/testing
✅ **Optimize columns**: Chỉ SELECT columns thực sự cần
✅ **Monitor performance**: Monitor query performance

### **6.2. FROM Clause Best Practices**

✅ **Specify table clearly**: Rõ ràng table nào
✅ **Use aliases**: Dùng aliases khi cần (JOIN, subqueries)
✅ **Check table exists**: Đảm bảo table tồn tại

### **6.3. WHERE Clause Best Practices**

✅ **Use WHERE**: Luôn dùng WHERE để filter data
✅ **Index-friendly**: Dùng WHERE với indexed columns
✅ **Avoid functions**: Tránh dùng functions trong WHERE (sẽ học ở Day-017)

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **SELECT**: Câu lệnh cơ bản nhất để lấy dữ liệu
2. **SELECT * vs column_list**: SELECT * đơn giản nhưng chậm, SELECT column_list tốt hơn
3. **FROM clause**: Chỉ định table để query
4. **WHERE clause**: Lọc dữ liệu theo điều kiện
5. **Production best practice**: LUÔN dùng SELECT column_list trong production

### **Best Practices**

✅ **Luôn dùng SELECT column_list**: Trong production
✅ **Tránh SELECT ***: Chỉ dùng trong development/testing
✅ **Use WHERE**: Luôn filter data khi có thể
✅ **Monitor performance**: Monitor query performance

### **Câu hỏi tự kiểm tra**

1. SELECT là gì? Tại sao quan trọng?
2. SELECT * vs SELECT column_list - khi nào dùng gì?
3. FROM clause là gì?
4. WHERE clause là gì?
5. Tại sao SELECT * có thể làm query chậm?

---

**Chuẩn bị cho Day-017: WHERE - Điều kiện lọc dữ liệu** 🚀

