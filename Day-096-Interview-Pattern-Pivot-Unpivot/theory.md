# Day-096: Interview Pattern - Pivot/Unpivot

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Pivot data (rows → columns)
- Unpivot data (columns → rows)
- Khi nào dùng pivot/unpivot?
- Production scenarios

---

## 1️⃣ PIVOT LÀ GÌ?

**Pivot** là **chuyển rows thành columns**:

```sql
-- PostgreSQL: Dùng CASE và aggregation
SELECT 
  category,
  SUM(CASE WHEN month = 'Jan' THEN sales ELSE 0 END) AS jan_sales,
  SUM(CASE WHEN month = 'Feb' THEN sales ELSE 0 END) AS feb_sales,
  SUM(CASE WHEN month = 'Mar' THEN sales ELSE 0 END) AS mar_sales
FROM sales_data
GROUP BY category;
```

**Đặc điểm:**
- Rows → Columns
- Dùng CASE và aggregation
- Phù hợp cho reports

---

## 2️⃣ UNPIVOT LÀ GÌ?

**Unpivot** là **chuyển columns thành rows**:

```sql
-- PostgreSQL: Dùng UNION ALL
SELECT category, 'Jan' AS month, jan_sales AS sales FROM sales_pivot
UNION ALL
SELECT category, 'Feb' AS month, feb_sales AS sales FROM sales_pivot
UNION ALL
SELECT category, 'Mar' AS month, mar_sales AS sales FROM sales_pivot;
```

**Đặc điểm:**
- Columns → Rows
- Dùng UNION ALL hoặc VALUES
- Phù hợp cho normalization

---

## 3️⃣ TẠI SAO TỒN TẠI PIVOT/UNPIVOT?

**Pivot/Unpivot tồn tại để:**
- **Report format**: Pivot cho reports
- **Data normalization**: Unpivot cho normalization
- **Data transformation**: Transform data structure

**Nếu không có:**
- Khó format reports
- Khó normalize data
- Phải transform ở application

---

## 4️⃣ PRODUCTION STORY: REPORT FORMAT VỚI PIVOT

**Context:**
Cần sales report với months làm columns → dùng pivot.

**Problem:**
- Data ở dạng rows
- Cần format columns
- Application phải transform

**Fix:**
- Dùng pivot trong SQL
- Format trực tiếp trong query
- Result: Report format đúng, performance tốt

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Pivot**: Rows → Columns với CASE và aggregation
2. **Unpivot**: Columns → Rows với UNION ALL
3. **Best practice**: Dùng pivot cho reports, unpivot cho normalization

---

**Chuẩn bị cho Day-097: Interview Pattern - Recursive Queries** 🚀

