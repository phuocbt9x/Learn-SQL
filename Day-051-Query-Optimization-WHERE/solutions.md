# Day-051: Solutions - Query Optimization - WHERE clause

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: WHERE Optimization

**Sargable:** Có thể dùng index.

**Non-sargable:** Không thể dùng index (function trên column).

**Function trong WHERE:** Tránh function trên column.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize WHERE Queries

**a) Non-sargable:**
```sql
-- ❌ SAI
WHERE UPPER(email) = 'JOHN@EXAMPLE.COM'
```

**b) Sargable:**
```sql
-- ✅ ĐÚNG
WHERE email = UPPER('john@example.com')
```

---

**Chúc mừng hoàn thành Day-051!** 🎉
