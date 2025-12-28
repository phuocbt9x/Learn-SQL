# Day-032: UNION, INTERSECT, EXCEPT

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- UNION là gì?
- UNION vs UNION ALL
- INTERSECT, EXCEPT
- Performance comparison

---

## 1️⃣ UNION LÀ GÌ?

**UNION** kết hợp kết quả từ **nhiều queries**:

```sql
SELECT name FROM users
UNION
SELECT name FROM admins;
```

**Đặc điểm:**
- Loại bỏ duplicates
- Cùng structure (columns, types)

---

## 2️⃣ UNION VS UNION ALL

**UNION:**
- Loại bỏ duplicates
- Có thể chậm hơn (phải sort để tìm duplicates)

**UNION ALL:**
- Giữ tất cả rows (kể cả duplicates)
- Nhanh hơn (không cần sort)

---

## 3️⃣ INTERSECT, EXCEPT

**INTERSECT:**
- Rows có trong cả 2 queries

**EXCEPT:**
- Rows có trong query 1 nhưng không có trong query 2

---

## 4️⃣ PRODUCTION STORY: UNION ALL NHANH HƠN UNION 5X

**Context:**
Query dùng UNION → chậm 5s.

**Fix:**
Đổi UNION → UNION ALL → nhanh 1s (nếu không cần loại bỏ duplicates).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. UNION: Kết hợp queries, loại bỏ duplicates
2. UNION ALL: Kết hợp queries, giữ duplicates
3. INTERSECT/EXCEPT: Set operations
4. Performance: UNION ALL nhanh hơn UNION

---






**Chuẩn bị cho [Day-033: CASE-Expression](../Day-033-CASE-Expression/theory.md)** 🚀
