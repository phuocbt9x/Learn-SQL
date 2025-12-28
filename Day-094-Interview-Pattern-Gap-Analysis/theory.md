# Day-094: Interview Pattern - Gap Analysis

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Tìm gaps trong dữ liệu
- LAG/LEAD patterns
- Khi nào dùng gap analysis?
- Production scenarios

---

## 1️⃣ GAP ANALYSIS LÀ GÌ?

**Gap analysis** là **tìm khoảng trống** trong dữ liệu:

```sql
-- Tìm missing dates
WITH date_series AS (
  SELECT generate_series('2024-01-01'::date, '2024-12-31'::date, '1 day'::interval) AS date
),
existing_dates AS (
  SELECT DISTINCT date FROM sales
)
SELECT ds.date
FROM date_series ds
LEFT JOIN existing_dates ed ON ds.date = ed.date
WHERE ed.date IS NULL;
```

**Đặc điểm:**
- Tìm missing values
- Tìm gaps trong sequences
- Dùng LAG/LEAD

---

## 2️⃣ LAG/LEAD PATTERNS

**LAG** lấy giá trị trước đó:

```sql
SELECT 
  date,
  amount,
  LAG(amount) OVER (ORDER BY date) AS prev_amount
FROM transactions;
```

**LEAD** lấy giá trị tiếp theo:

```sql
SELECT 
  date,
  amount,
  LEAD(amount) OVER (ORDER BY date) AS next_amount
FROM transactions;
```

---

## 3️⃣ PRODUCTION STORY: TÌM MISSING DATES TRONG TIME SERIES

**Context:**
Cần tìm missing dates trong sales data → identify data quality issues.

**Problem:**
- Không biết dates nào thiếu
- Data quality issues

**Fix:**
- Dùng generate_series và LEFT JOIN
- Tìm missing dates
- Result: Identify và fix data quality issues

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Gap analysis**: Tìm gaps trong dữ liệu
2. **LAG/LEAD**: Lấy giá trị trước/sau
3. **Best practice**: Dùng Window Functions cho gap analysis

---



**Chuẩn bị cho [Day-095: Interview-Pattern-Self-JOIN](Day-095-Interview-Pattern-Self-JOIN/theory.md)** 🚀
