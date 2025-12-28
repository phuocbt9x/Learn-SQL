# Day-083: Functions - User-defined Functions

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Function là gì?
- Function vs Stored Procedure
- Khi nào dùng Function?
- Reusable logic với functions

---

## 1️⃣ FUNCTION LÀ GÌ?

**Function** là **code được lưu trữ trong database** và **return một giá trị**:

```sql
-- Tạo function
CREATE OR REPLACE FUNCTION calculate_total(
  p_price DECIMAL(10, 2),
  p_quantity INTEGER
)
RETURNS DECIMAL(10, 2)
LANGUAGE plpgsql
AS $$
BEGIN
  RETURN p_price * p_quantity;
END;
$$;

-- Gọi function
SELECT calculate_total(10.99, 3);
-- → 32.97
```

**Đặc điểm:**
- Return một giá trị
- Có thể dùng trong SELECT
- Có thể nhận parameters
- Có thể có side effects (nhưng không nên)

---

## 2️⃣ FUNCTION VS STORED PROCEDURE

**Function:**
- Return một giá trị
- Dùng trong SELECT
- Không có transaction control
- Pure function (nên)

**Stored Procedure:**
- Không return (hoặc return qua OUT parameters)
- Dùng CALL
- Có transaction control
- Có thể có side effects

**Khi nào dùng:**
- **Function**: Calculations, transformations, reusable logic
- **Stored Procedure**: Business logic, transactions, side effects

---

## 3️⃣ TẠI SAO TỒN TẠI FUNCTION?

**Function tồn tại để:**
- **Reusable logic**: Logic dùng lại nhiều nơi
- **Calculations**: Tính toán phức tạp
- **Data transformations**: Transform data
- **Consistency**: Đảm bảo logic nhất quán

**Nếu không có Function:**
- Logic phải duplicate ở nhiều nơi
- Khó maintain
- Không nhất quán

---

## 4️⃣ PRODUCTION STORY: REUSABLE LOGIC VỚI FUNCTIONS

**Context:**
Có logic tính discount được duplicate ở nhiều nơi → không nhất quán.

**Problem:**
- Logic tính discount khác nhau ở các nơi
- Bug khi update logic ở một nơi nhưng quên nơi khác

**Fix:**
Tạo function `calculate_discount` → tất cả dùng cùng function → nhất quán.

**Result:**
- Logic nhất quán
- Dễ maintain
- Dễ test

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Function**: Code return giá trị, dùng trong SELECT
2. **vs Stored Procedure**: Function cho calculations, Procedure cho business logic
3. **Reusable logic**: Function giúp logic nhất quán
4. **Best practice**: Pure functions, không side effects

---






**Chuẩn bị cho [Day-084: Triggers](../Day-084-Triggers/theory.md)** 🚀
