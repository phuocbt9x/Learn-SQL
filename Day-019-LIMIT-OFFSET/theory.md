# Day-019: LIMIT & OFFSET - Giới hạn kết quả

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- LIMIT là gì?
- OFFSET là gì?
- Pagination pattern
- Performance của OFFSET lớn

---

## 1️⃣ LIMIT LÀ GÌ?

**LIMIT** giới hạn số rows trả về:

```sql
SELECT * FROM users LIMIT 10;
```

---

## 2️⃣ OFFSET LÀ GÌ?

**OFFSET** bỏ qua N rows đầu tiên:

```sql
SELECT * FROM users LIMIT 10 OFFSET 20;
```

---

## 3️⃣ PAGINATION PATTERN

**Pagination:**

```sql
-- Page 1
SELECT * FROM users LIMIT 10 OFFSET 0;

-- Page 2
SELECT * FROM users LIMIT 10 OFFSET 10;

-- Page 3
SELECT * FROM users LIMIT 10 OFFSET 20;
```

---

## 4️⃣ PERFORMANCE CỦA OFFSET LỚN

**Vấn đề:**
- OFFSET lớn → chậm
- Database phải scan và skip nhiều rows

**Best practice:**
- Dùng cursor-based pagination thay vì OFFSET lớn

---

## 5️⃣ PRODUCTION STORY: PAGINATION CHẬM VỚI OFFSET 10000+

**Context:**
Pagination với OFFSET 10000+ → query chậm 10 giây.

**Fix:**
Dùng cursor-based pagination → query nhanh < 100ms.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. LIMIT: Giới hạn số rows
2. OFFSET: Bỏ qua rows
3. Pagination: LIMIT + OFFSET
4. Performance: OFFSET lớn chậm
5. Best practice: Cursor-based pagination

---






**Chuẩn bị cho [Day-020: DISTINCT](../Day-020-DISTINCT/theory.md)** 🚀
