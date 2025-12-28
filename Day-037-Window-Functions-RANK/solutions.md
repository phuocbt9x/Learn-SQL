# Day-037: Solutions - Window Functions - RANK

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: RANK Functions

**ROW_NUMBER():** Đánh số unique.

**RANK() vs DENSE_RANK():** RANK skip numbers, DENSE_RANK không.

**PARTITION BY:** Chia window thành partitions.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết RANK Queries

**a)**
```sql
WITH ranked AS (
  SELECT name, 
         department,
         salary,
         ROW_NUMBER() OVER(PARTITION BY department ORDER BY salary DESC) as rank
  FROM employees
)
SELECT * FROM ranked WHERE rank <= 3;
```

**b)**
```sql
SELECT id, 
       total_amount,
       RANK() OVER(ORDER BY total_amount DESC) as rank
FROM orders;
```

---

**Chúc mừng hoàn thành Day-037!** 🎉
