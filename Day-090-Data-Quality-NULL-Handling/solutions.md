# Day-090: Solutions - Data Quality - NULL Handling

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: NULL Handling

**Best practices:** Explicit checks, COALESCE, NULLIF, NOT NULL constraints.

**COALESCE:** Trả về giá trị đầu tiên không NULL.

**NULLIF:** Convert giá trị thành NULL.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Xử lý NULL

**Solution:**

```sql
-- Fix NULL trong calculations
SELECT COALESCE(price * quantity, 0) AS total FROM orders;

-- Dùng NULLIF
SELECT NULLIF(price, 0) FROM products;
```

---

**Chúc mừng hoàn thành Day-090!** 🎉

**Chuẩn bị cho Phase 5.4!** 🚀
