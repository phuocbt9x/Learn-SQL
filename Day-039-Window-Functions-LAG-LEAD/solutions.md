# Day-039: Solutions - Window Functions - LAG/LEAD

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: LAG/LEAD

**LAG():** Lấy giá trị từ row trước.

**LEAD():** Lấy giá trị từ row sau.

**Khi nào dùng:** So sánh, growth rate, time series.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết LAG/LEAD Queries

**a)**
```sql
SELECT date, 
       amount,
       LAG(amount) OVER(ORDER BY date) as prev_amount
FROM transactions;
```

**b)**
```sql
SELECT date, 
       amount,
       LAG(amount) OVER(ORDER BY date) as prev_amount,
       amount - LAG(amount) OVER(ORDER BY date) as growth
FROM transactions;
```

---

**Chúc mừng hoàn thành Day-039!** 🎉
