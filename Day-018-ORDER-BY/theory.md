# Day-018: ORDER BY - Sắp xếp kết quả

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- ORDER BY là gì?
- ASC vs DESC
- Multi-column sorting
- NULLS FIRST vs NULLS LAST
- Performance impact của ORDER BY

---

## 1️⃣ ORDER BY LÀ GÌ?

**ORDER BY** dùng để **sắp xếp kết quả** theo columns:

```sql
SELECT * FROM users ORDER BY name;
```

**ASC vs DESC:**
- `ASC`: Tăng dần (mặc định)
- `DESC`: Giảm dần

**Ví dụ:**
```sql
-- Tăng dần
SELECT * FROM users ORDER BY created_at ASC;

-- Giảm dần
SELECT * FROM users ORDER BY created_at DESC;
```

---

## 2️⃣ MULTI-COLUMN SORTING

**Sắp xếp nhiều columns:**

```sql
SELECT * FROM users 
ORDER BY status ASC, created_at DESC;
```

---

## 3️⃣ NULLS FIRST vs NULLS LAST

**Xử lý NULL khi sort:**

```sql
-- NULL ở đầu
SELECT * FROM users ORDER BY email NULLS FIRST;

-- NULL ở cuối
SELECT * FROM users ORDER BY email NULLS LAST;
```

---

## 4️⃣ PERFORMANCE IMPACT

**ORDER BY có thể chậm nếu:**
- Không có index trên column sort
- Sort nhiều rows
- Multi-column sort

**Best practice:**
- Tạo index trên columns sort
- Dùng LIMIT để giảm số rows sort

---

## 5️⃣ PRODUCTION STORY: QUERY TIMEOUT DO ORDER BY KHÔNG CÓ INDEX

**Context:**
Query sort 10 triệu rows không có index → timeout.

**Fix:**
Tạo index trên column sort → query nhanh hơn 1000x.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. ORDER BY: Sắp xếp kết quả
2. ASC/DESC: Tăng/giảm dần
3. Multi-column: Sort nhiều columns
4. NULLS FIRST/LAST: Xử lý NULL
5. Performance: Cần index cho ORDER BY

---



**Chuẩn bị cho [Day-019: LIMIT-OFFSET](../Day-019-LIMIT-OFFSET/theory.md)** 🚀
