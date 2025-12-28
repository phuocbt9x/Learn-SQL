# Day-038: Window Functions - Aggregate Window Functions

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- SUM() OVER(), AVG() OVER()
- Running totals
- Moving averages
- Window frame (ROWS BETWEEN)

---

## 1️⃣ AGGREGATE WINDOW FUNCTIONS

**SUM() OVER():**
```sql
SELECT date, 
       amount,
       SUM(amount) OVER(ORDER BY date) as running_total
FROM transactions;
```

**AVG() OVER():**
```sql
SELECT date, 
       amount,
       AVG(amount) OVER(ORDER BY date) as running_avg
FROM transactions;
```

---

## 2️⃣ RUNNING TOTALS

**Running total:**
```sql
SELECT date, 
       amount,
       SUM(amount) OVER(ORDER BY date) as running_total
FROM transactions;
```

**Kết quả:** Tổng tích lũy theo thời gian.

---

## 3️⃣ MOVING AVERAGES

**Moving average (7 days):**
```sql
SELECT date, 
       amount,
       AVG(amount) OVER(ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as moving_avg_7d
FROM transactions;
```

---

## 4️⃣ WINDOW FRAME

**ROWS BETWEEN:**
- `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`: Từ đầu đến hiện tại
- `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW`: 7 rows (6 trước + hiện tại)

---

## 5️⃣ PRODUCTION STORY: ANALYTICS QUERY NHANH HƠN VỚI WINDOW FUNCTIONS

**Context:**
Analytics query với subqueries → chậm 10s.

**Fix:**
Dùng Window Functions → nhanh 0.5s.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. Aggregate Window Functions: SUM, AVG, COUNT, etc.
2. Running totals: Tổng tích lũy
3. Moving averages: Trung bình động
4. Window frame: ROWS BETWEEN

---



**Chuẩn bị cho [Day-039: Window-Functions-LAG-LEAD](../Day-039-Window-Functions-LAG-LEAD/theory.md)** 🚀
