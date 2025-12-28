# Day-083: Solutions - Functions - User-defined Functions

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Function là gì?

**Function:** Code return giá trị, dùng trong SELECT.

**vs Stored Procedure:** Function cho calculations, Procedure cho business logic.

**Khi nào dùng:** Reusable logic, calculations, transformations.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Function

**Solution:**

```sql
-- Calculate discount
CREATE OR REPLACE FUNCTION calculate_discount(
  p_price DECIMAL(10, 2),
  p_discount_percent INTEGER
)
RETURNS DECIMAL(10, 2)
LANGUAGE plpgsql
AS $$
BEGIN
  RETURN p_price * (1 - p_discount_percent / 100.0);
END;
$$;

-- Format currency
CREATE OR REPLACE FUNCTION format_currency(
  p_amount DECIMAL(10, 2)
)
RETURNS TEXT
LANGUAGE plpgsql
AS $$
BEGIN
  RETURN '$' || TO_CHAR(p_amount, '999,999.99');
END;
$$;
```

---

**Chúc mừng hoàn thành Day-083!** 🎉
