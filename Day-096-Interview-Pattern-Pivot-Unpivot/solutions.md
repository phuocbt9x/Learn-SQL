# Day-096: Solutions - Interview Pattern - Pivot/Unpivot

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Pivot/Unpivot

**Pivot:** Chuyển rows thành columns.

**Unpivot:** Chuyển columns thành rows.

**Khi nào dùng:** Pivot cho reports, Unpivot cho normalization.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Pivot Sales Data

**Solution:**

```sql
-- Pivot
SELECT 
  category,
  SUM(CASE WHEN month = 'Jan' THEN sales ELSE 0 END) AS jan_sales,
  SUM(CASE WHEN month = 'Feb' THEN sales ELSE 0 END) AS feb_sales,
  SUM(CASE WHEN month = 'Mar' THEN sales ELSE 0 END) AS mar_sales
FROM sales_data
GROUP BY category;

-- Unpivot
SELECT category, 'Jan' AS month, jan_sales AS sales FROM sales_pivot
UNION ALL
SELECT category, 'Feb' AS month, feb_sales AS sales FROM sales_pivot
UNION ALL
SELECT category, 'Mar' AS month, mar_sales AS sales FROM sales_pivot;
```

---

**Chúc mừng hoàn thành Day-096!** 🎉

**Chuẩn bị cho Day-097: Interview Pattern - Recursive Queries** 🚀

