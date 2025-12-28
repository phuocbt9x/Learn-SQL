# Day-022: Solutions - GROUP BY

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: GROUP BY là gì?

**GROUP BY:** Nhóm rows có cùng giá trị.

**Tại sao cần:** Tính toán aggregate theo nhóm.

**Multiple columns:** GROUP BY nhiều columns.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết GROUP BY Queries

**a)**
```sql
SELECT status, COUNT(*) 
FROM orders 
GROUP BY status;
```

**b)**
```sql
SELECT user_id, SUM(total_amount) 
FROM orders 
GROUP BY user_id;
```

**c)**
```sql
SELECT category_id, AVG(price) 
FROM products 
GROUP BY category_id;
```

---

**Chúc mừng hoàn thành Day-022!** 🎉
