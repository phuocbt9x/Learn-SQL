# Day-030: Solutions - Subquery - Correlated Subquery

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Correlated Subquery là gì?

**Correlated subquery:** Subquery reference đến outer query.

**Execution flow:** N+1 queries (1 outer + N subqueries).

**Performance:** Có thể chậm với large datasets.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Correlated Subquery Queries

**a) Correlated subquery:**
```sql
SELECT name, 
       (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) as order_count
FROM users u;
```

**b) JOIN + GROUP BY (tốt hơn):**
```sql
SELECT u.name, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
```

---

**Chúc mừng hoàn thành Day-030!** 🎉

**Chuẩn bị cho Phase 2.4!** 🚀
