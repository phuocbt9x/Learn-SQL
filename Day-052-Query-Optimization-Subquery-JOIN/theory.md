# Day-052: Query Optimization - Subquery to JOIN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Khi nào rewrite subquery thành JOIN?
- Correlated subquery optimization
- Subquery vs JOIN performance
- Best practices

---

## 1️⃣ KHI NÀO REWRITE SUBQUERY THÀNH JOIN?

**Rewrite khi:**
- Subquery có thể convert thành JOIN
- JOIN thường nhanh hơn
- Query đơn giản hơn

**Ví dụ:**
```sql
-- Subquery
SELECT * FROM users 
WHERE id IN (SELECT user_id FROM orders);

-- JOIN (tốt hơn)
SELECT DISTINCT u.* FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

---

## 2️⃣ CORRELATED SUBQUERY OPTIMIZATION

**Correlated subquery:**
- Execute nhiều lần (N+1 queries)
- Có thể chậm

**Optimize:**
- Convert thành JOIN
- Hoặc dùng Window Functions

---

## 3️⃣ SUBQUERY VS JOIN PERFORMANCE

**Subquery:**
- Có thể chậm (N+1 queries)
- Khó optimize

**JOIN:**
- Thường nhanh hơn
- Database optimize tốt hơn

---

## 4️⃣ PRODUCTION STORY: QUERY NHANH HƠN 20X SAU KHI REWRITE

**Context:**
Correlated subquery → chậm 20s.

**Fix:**
Rewrite thành JOIN → nhanh 1s (nhanh hơn 20x).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Rewrite subquery: Khi có thể convert thành JOIN
2. Correlated subquery: Có thể chậm, optimize bằng JOIN
3. Performance: JOIN thường nhanh hơn
4. Best practice: Prefer JOIN over subquery khi có thể

---






**Chuẩn bị cho [Day-053: Query-Optimization-Avoid-SELECT-Star](../Day-053-Query-Optimization-Avoid-SELECT-Star/theory.md)** 🚀
