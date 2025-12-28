# Day-093: Solutions - Interview Pattern - Running Totals

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Running Totals

**Running totals:** Tổng tích lũy theo thời gian.

**Window Functions:** SUM() OVER (ORDER BY date).

**Performance:** Window Functions efficient hơn subquery.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Running Balance

**Solution:**

```sql
-- Window Functions
SELECT 
  date,
  amount,
  SUM(amount) OVER (ORDER BY date) AS running_balance
FROM transactions
ORDER BY date;

-- So sánh với Subquery (chậm hơn)
SELECT 
  t1.date,
  t1.amount,
  (SELECT SUM(t2.amount) FROM transactions t2 WHERE t2.date <= t1.date) AS running_balance
FROM transactions t1
ORDER BY t1.date;
```

---

**Chúc mừng hoàn thành Day-093!** 🎉
