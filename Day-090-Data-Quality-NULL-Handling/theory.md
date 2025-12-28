# Day-090: Data Quality - NULL Handling

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- NULL best practices
- COALESCE, NULLIF
- Khi nào dùng NULL?
- Hậu quả nếu không xử lý NULL đúng

---

## 1️⃣ NULL LÀ GÌ? (REVIEW)

**NULL** là **giá trị không xác định**:

```sql
-- NULL trong WHERE
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;

-- NULL trong calculations
SELECT price * quantity FROM orders;  -- NULL nếu price hoặc quantity NULL
```

**Đặc điểm:**
- Không phải 0, không phải empty string
- Không thể so sánh với = hoặc !=
- Phải dùng IS NULL hoặc IS NOT NULL

---

## 2️⃣ NULL BEST PRACTICES

**Best practices:**
1. **Explicit NULL checks**: Luôn check NULL
2. **COALESCE**: Dùng giá trị mặc định
3. **NULLIF**: Convert giá trị thành NULL
4. **NOT NULL constraints**: Tránh NULL khi không cần

---

## 3️⃣ COALESCE VÀ NULLIF

**COALESCE** trả về giá trị đầu tiên không NULL:

```sql
SELECT COALESCE(price, 0) FROM products;  -- 0 nếu price NULL
SELECT COALESCE(name, email, 'Unknown') FROM users;  -- First non-NULL
```

**NULLIF** convert giá trị thành NULL:

```sql
SELECT NULLIF(price, 0) FROM products;  -- NULL nếu price = 0
```

---

## 4️⃣ PRODUCTION STORY: BUG DO NULL KHÔNG ĐƯỢC XỬ LÝ ĐÚNG

**Context:**
Query tính total không xử lý NULL → return NULL thay vì 0.

**Problem:**
- NULL trong calculations → NULL result
- Application crash
- Users không thể checkout

**Fix:**
- Dùng COALESCE → return 0 thay vì NULL
- Result: Application hoạt động bình thường

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **NULL**: Giá trị không xác định
2. **Best practices**: Explicit checks, COALESCE, NULLIF
3. **Hậu quả**: Bug nếu không xử lý đúng

---

**Chuẩn bị cho Phase 5.4!** 🚀


**Chuẩn bị cho [Day-091: Data-Quality-Data-Validation](../Day-091-Data-Quality-Data-Validation/theory.md)** 🚀
