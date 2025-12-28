# Day-092: Solutions - Interview Pattern - Top N per Group

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Top N per Group

**Top N per Group:** Pattern lấy N records đầu tiên mỗi group.

**ROW_NUMBER():** Unique ranking, không ties.

**RANK():** Có thể ties, skip numbers.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Top 3 Products mỗi Category

**Solution:**

```sql
-- Dùng ROW_NUMBER()
WITH ranked_products AS (
  SELECT 
    category_id,
    product_id,
    sales,
    ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY sales DESC) AS rn
  FROM product_sales
)
SELECT * FROM ranked_products WHERE rn <= 3;

-- Dùng RANK() (cho phép ties)
WITH ranked_products AS (
  SELECT 
    category_id,
    product_id,
    sales,
    RANK() OVER (PARTITION BY category_id ORDER BY sales DESC) AS rn
  FROM product_sales
)
SELECT * FROM ranked_products WHERE rn <= 3;
```

---

**Chúc mừng hoàn thành Day-092!** 🎉
