# Day-039: Window Functions - LAG, LEAD

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- LAG() và LEAD() là gì?
- Khi nào dùng LAG/LEAD?
- So sánh với row trước/sau
- Production use cases

---

## 1️⃣ LAG() VÀ LEAD()

**LAG()** lấy giá trị từ **row trước**:

```sql
SELECT date, 
       amount,
       LAG(amount) OVER(ORDER BY date) as prev_amount
FROM transactions;
```

**LEAD()** lấy giá trị từ **row sau**:

```sql
SELECT date, 
       amount,
       LEAD(amount) OVER(ORDER BY date) as next_amount
FROM transactions;
```

---

## 2️⃣ KHI NÀO DÙNG LAG/LEAD?

**Dùng khi:**
- So sánh với row trước/sau
- Tính growth rate
- Tìm changes
- Time series analysis

---

## 3️⃣ SO SÁNH VỚI ROW TRƯỚC/SAU

**Growth rate:**
```sql
SELECT date, 
       amount,
       LAG(amount) OVER(ORDER BY date) as prev_amount,
       amount - LAG(amount) OVER(ORDER BY date) as growth
FROM transactions;
```

---

## 4️⃣ PRODUCTION STORY: SO SÁNH THÁNG NÀY VS THÁNG TRƯỚC

**Context:**
Cần so sánh revenue tháng này vs tháng trước.

**Solution:**
Dùng LAG() → đơn giản, hiệu quả.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. LAG(): Lấy giá trị từ row trước
2. LEAD(): Lấy giá trị từ row sau
3. Use cases: Growth rate, comparisons
4. Best practice: Dùng cho time series

---



**Chuẩn bị cho [Day-040: Review-Phase2](Day-040-Review-Phase2/theory.md)** 🚀
