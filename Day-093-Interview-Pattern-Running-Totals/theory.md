# Day-093: Interview Pattern - Running Totals

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Running totals với Window Functions
- Performance optimization
- Khi nào dùng running totals?
- Production scenarios

---

## 1️⃣ RUNNING TOTALS LÀ GÌ?

**Running totals** là **tổng tích lũy** theo thời gian:

```sql
-- Running total với Window Functions
SELECT 
  date,
  amount,
  SUM(amount) OVER (ORDER BY date) AS running_total
FROM transactions
ORDER BY date;
```

**Đặc điểm:**
- Tổng tích lũy từ đầu đến hiện tại
- Dùng Window Functions
- ORDER BY để sort

---

## 2️⃣ TẠI SAO TỒN TẠI RUNNING TOTALS?

**Running totals tồn tại để:**
- **Financial reports**: Balance, cumulative revenue
- **Analytics**: Cumulative metrics
- **Progress tracking**: Track progress over time

**Nếu không có:**
- Phải tính toán ở application
- Nhiều queries
- Performance tệ

---

## 3️⃣ PERFORMANCE OPTIMIZATION

**Window Functions:**
- Efficient với indexes
- Single pass through data
- Better than subquery

**Best practices:**
- Index trên ORDER BY column
- Use appropriate window frame

---

## 4️⃣ PRODUCTION STORY: FINANCIAL REPORT VỚI RUNNING BALANCE

**Context:**
Cần financial report với running balance cho mỗi account.

**Problem:**
- Subquery chậm
- Nhiều queries

**Fix:**
- Dùng Window Functions
- Performance tốt hơn 10x
- Code đơn giản hơn

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Running totals**: Tổng tích lũy với Window Functions
2. **Performance**: Window Functions efficient hơn subquery
3. **Best practice**: Dùng Window Functions cho running totals

---



**Chuẩn bị cho [Day-094: Interview-Pattern-Gap-Analysis](../Day-094-Interview-Pattern-Gap-Analysis/theory.md)** 🚀
