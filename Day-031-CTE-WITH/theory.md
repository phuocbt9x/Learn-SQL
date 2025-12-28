# Day-031: CTE (Common Table Expression) - WITH clause

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- CTE là gì?
- Tại sao dùng CTE?
- CTE vs Subquery
- Recursive CTE (giới thiệu)

---

## 1️⃣ CTE LÀ GÌ?

**CTE (Common Table Expression)** là **temporary named result set**:

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

---

## 2️⃣ TẠI SAO DÙNG CTE?

**Lợi ích:**
- **Readability**: Query dễ đọc hơn
- **Reusability**: Có thể dùng nhiều lần
- **Maintainability**: Dễ maintain

---

## 3️⃣ CTE VS SUBQUERY

**CTE:**
- Dễ đọc hơn
- Có thể reference nhiều lần
- Tách logic rõ ràng

**Subquery:**
- Inline, ngắn gọn
- Khó đọc với nested subqueries

---

## 4️⃣ RECURSIVE CTE (GIỚI THIỆU)

**Recursive CTE** cho phép query **hierarchical data**:

```sql
WITH RECURSIVE category_tree AS (
  SELECT id, name, parent_id
  FROM categories
  WHERE parent_id IS NULL
  UNION ALL
  SELECT c.id, c.name, c.parent_id
  FROM categories c
  INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree;
```

---

## 5️⃣ PRODUCTION STORY: QUERY DỄ ĐỌC HƠN NHỜ CTE

**Context:**
Query phức tạp với nhiều nested subqueries → khó đọc.

**Fix:**
Dùng CTE → query dễ đọc, dễ maintain hơn.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. CTE: Temporary named result set
2. Lợi ích: Readability, reusability
3. CTE vs Subquery: CTE dễ đọc hơn
4. Recursive CTE: Cho hierarchical data

---

**Chuẩn bị cho Day-032: UNION, INTERSECT, EXCEPT** 🚀
