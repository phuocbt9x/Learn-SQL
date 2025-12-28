# Day-052: Solutions - Query Optimization - Subquery to JOIN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Subquery to JOIN

**Rewrite khi:** Subquery có thể convert thành JOIN.

**Correlated subquery:** Có thể chậm, optimize bằng JOIN.

**Performance:** JOIN thường nhanh hơn subquery.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Rewrite Subquery to JOIN

**a) Subquery:**
```sql
SELECT * FROM users 
WHERE id IN (SELECT user_id FROM orders);
```

**b) JOIN:**
```sql
SELECT DISTINCT u.* FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

---

**Chúc mừng hoàn thành Day-052!** 🎉
