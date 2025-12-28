# Day-024: Solutions - JOIN - INNER JOIN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: JOIN là gì?

**JOIN:** Kết hợp rows từ nhiều tables.

**INNER JOIN:** Chỉ trả về rows có match ở cả 2 tables.

**Syntax:** Explicit tốt hơn implicit.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết INNER JOIN Queries

**a)**
```sql
SELECT u.name, o.total_amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

**b)**
```sql
SELECT p.name, c.name as category_name
FROM products p
INNER JOIN categories c ON p.category_id = c.id;
```

---

**Chúc mừng hoàn thành Day-024!** 🎉
