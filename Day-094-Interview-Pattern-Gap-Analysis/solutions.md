# Day-094: Solutions - Interview Pattern - Gap Analysis

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Gap Analysis

**Gap analysis:** Tìm khoảng trống trong dữ liệu.

**LAG/LEAD:** Lấy giá trị trước/sau với Window Functions.

**Khi nào dùng:** Tìm missing values, gaps trong sequences.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tìm Missing Dates

**Solution:**

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

-- Dùng LAG để tìm gaps
SELECT 
  date,
  LAG(date) OVER (ORDER BY date) AS prev_date,
  date - LAG(date) OVER (ORDER BY date) AS gap_days
FROM sales
WHERE date - LAG(date) OVER (ORDER BY date) > 1;
```

---

**Chúc mừng hoàn thành Day-094!** 🎉
