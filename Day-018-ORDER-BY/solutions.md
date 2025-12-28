# Day-018: Solutions - ORDER BY

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: ORDER BY là gì?

**ORDER BY:** Sắp xếp kết quả theo columns.

**ASC vs DESC:**
- ASC: Tăng dần (mặc định)
- DESC: Giảm dần

**Khi nào dùng:** Khi cần sắp xếp kết quả.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết ORDER BY Queries

**a)**
```sql
SELECT * FROM users ORDER BY name ASC;
```

**b)**
```sql
SELECT * FROM orders ORDER BY created_at DESC;
```

**c)**
```sql
SELECT * FROM products ORDER BY price ASC, name DESC;
```

---

**Chúc mừng hoàn thành Day-018!** 🎉
