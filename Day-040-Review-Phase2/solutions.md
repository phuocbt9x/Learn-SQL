# Day-040: Solutions - Review Phase 2

## 🎯 BÀI TẬP 1: TỔNG HỢP KIẾN THỨC

### Câu 1.1: SQL Query Language

**Basic SQL:**
- SELECT: Lấy dữ liệu
- WHERE: Lọc dữ liệu
- ORDER BY: Sắp xếp
- LIMIT: Giới hạn

**Aggregations:**
- COUNT, SUM, AVG, MIN, MAX
- GROUP BY: Nhóm dữ liệu
- HAVING: Lọc sau GROUP BY

**JOINs:**
- INNER, LEFT, RIGHT, FULL OUTER
- Multiple tables

**Subqueries:**
- Scalar, EXISTS, IN, Correlated

**Window Functions:**
- RANK, Aggregate, LAG/LEAD

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Complex Query

**Ví dụ:**
```sql
WITH user_stats AS (
  SELECT user_id, 
         COUNT(*) as order_count,
         SUM(total_amount) as total_spent
  FROM orders
  GROUP BY user_id
)
SELECT u.name,
       us.order_count,
       us.total_spent,
       RANK() OVER(ORDER BY us.total_spent DESC) as rank
FROM users u
INNER JOIN user_stats us ON u.id = us.user_id
ORDER BY us.total_spent DESC
LIMIT 10;
```

---

**Chúc mừng hoàn thành Phase 2!** 🎉

**Chuẩn bị cho Phase 3: Advanced SQL & Performance** 🚀
