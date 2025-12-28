# Day-038: Solutions - Window Functions - Aggregate

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Aggregate Window Functions

**SUM() OVER():** Tổng trên window.

**Running totals:** Tổng tích lũy theo thời gian.

**Moving averages:** Trung bình động.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Aggregate Window Function Queries

**a)**
```sql
SELECT date, 
       amount,
       SUM(amount) OVER(ORDER BY date) as running_total
FROM transactions;
```

**b)**
```sql
SELECT date, 
       amount,
       AVG(amount) OVER(ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as moving_avg_7d
FROM transactions;
```

---

**Chúc mừng hoàn thành Day-038!** 🎉
