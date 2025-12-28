# Day-031: Solutions - CTE - WITH clause

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: CTE là gì?

**CTE:** Temporary named result set.

**Tại sao dùng:** Readability, reusability, maintainability.

**CTE vs Subquery:** CTE dễ đọc hơn.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết CTE Queries

**a)**
```sql
WITH user_orders AS (
  SELECT user_id, COUNT(*) as order_count
  FROM orders
  GROUP BY user_id
)
SELECT u.name, uo.order_count
FROM users u
INNER JOIN user_orders uo ON u.id = uo.user_id;
```

**b)**
```sql
-- Subquery
SELECT u.name, 
       (SELECT COUNT(*) FROM orders WHERE user_id = u.id) as order_count
FROM users u;

-- CTE (tốt hơn)
WITH user_orders AS (
  SELECT user_id, COUNT(*) as order_count
  FROM orders
  GROUP BY user_id
)
SELECT u.name, uo.order_count
FROM users u
LEFT JOIN user_orders uo ON u.id = uo.user_id;
```

---

**Chúc mừng hoàn thành Day-031!** 🎉
