# Day-028: Subquery - Scalar Subquery

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Subquery là gì?
- Scalar subquery là gì?
- Subquery trong SELECT, WHERE
- Performance: Subquery vs JOIN

---

## 1️⃣ SUBQUERY LÀ GÌ?

**Subquery** là query **bên trong query khác**:

```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

---

## 2️⃣ SCALAR SUBQUERY LÀ GÌ?

**Scalar subquery** trả về **một giá trị**:

```sql
SELECT name, 
       (SELECT COUNT(*) FROM orders WHERE user_id = users.id) as order_count
FROM users;
```

---

## 3️⃣ SUBQUERY TRONG SELECT, WHERE

**Trong SELECT:**
```sql
SELECT name, (SELECT COUNT(*) FROM orders WHERE user_id = users.id) as count
FROM users;
```

**Trong WHERE:**
```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

---

## 4️⃣ PERFORMANCE: SUBQUERY VS JOIN

**Subquery:**
- Có thể chậm nếu không optimize
- N+1 problem với correlated subquery

**JOIN:**
- Thường nhanh hơn
- Database optimize tốt hơn

---

## 5️⃣ PRODUCTION STORY: N+1 QUERY PROBLEM DO SCALAR SUBQUERY

**Context:**
Scalar subquery trong SELECT → N+1 queries.

**Fix:**
Dùng JOIN thay vì subquery → 1 query.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. Subquery: Query trong query
2. Scalar subquery: Trả về một giá trị
3. Performance: Subquery có thể chậm
4. Best practice: Cân nhắc JOIN thay vì subquery

---



**Chuẩn bị cho [Day-029: Subquery-EXISTS-IN](Day-029-Subquery-EXISTS-IN/theory.md)** 🚀
