# Day-023: Solutions - HAVING

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: HAVING là gì?

**HAVING:** Lọc kết quả sau GROUP BY.

**HAVING vs WHERE:**
- WHERE: Lọc rows trước GROUP BY
- HAVING: Lọc groups sau GROUP BY

**Khi nào dùng:** Khi cần lọc theo aggregate functions.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết HAVING Queries

**a)**
```sql
SELECT status, COUNT(*) 
FROM orders 
GROUP BY status
HAVING COUNT(*) > 100;
```

**b)**
```sql
SELECT user_id, SUM(total_amount) 
FROM orders 
GROUP BY user_id
HAVING SUM(total_amount) > 1000;
```

---

**Chúc mừng hoàn thành Day-023!** 🎉
