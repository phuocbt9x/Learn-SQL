# Day-092: Interview Pattern - Top N per Group

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Pattern: Lấy top N records mỗi group
- ROW_NUMBER() vs RANK()
- Khi nào dùng gì?
- Production scenarios

---

## 1️⃣ TOP N PER GROUP LÀ GÌ?

**Top N per Group** là pattern **lấy N records đầu tiên** trong mỗi group:

```sql
-- Top 3 products mỗi category
WITH ranked_products AS (
  SELECT 
    category_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY sales DESC) AS rn
  FROM product_sales
)
SELECT * FROM ranked_products WHERE rn <= 3;
```

**Đặc điểm:**
- Dùng Window Functions
- PARTITION BY để group
- ORDER BY để sort
- Filter top N

---

## 2️⃣ ROW_NUMBER() VS RANK()

**ROW_NUMBER():**
- Unique number cho mỗi row
- Không có ties (1, 2, 3, 4...)

**RANK():**
- Có thể có ties
- Skip numbers khi có ties (1, 2, 2, 4...)

**DENSE_RANK():**
- Có thể có ties
- Không skip numbers (1, 2, 2, 3...)

---

## 3️⃣ KHI NÀO DÙNG GÌ?

**ROW_NUMBER():**
- Cần unique ranking
- Top N chính xác (không ties)

**RANK():**
- Cho phép ties
- Cần skip numbers

**DENSE_RANK():**
- Cho phép ties
- Không cần skip numbers

---

## 4️⃣ PRODUCTION STORY: TOP 3 SẢN PHẨM BÁN CHẠY MỖI CATEGORY

**Context:**
Cần hiển thị top 3 products mỗi category trên homepage.

**Problem:**
- Query phức tạp
- Performance tệ với subquery

**Fix:**
- Dùng ROW_NUMBER() với Window Functions
- Performance tốt hơn
- Code đơn giản hơn

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Top N per Group**: Window Functions pattern
2. **ROW_NUMBER vs RANK**: Unique vs ties
3. **Best practice**: Dùng Window Functions cho performance

---

**Chuẩn bị cho Day-093: Interview Pattern - Running Totals** 🚀
