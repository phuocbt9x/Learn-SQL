# Day-021: Solutions - Aggregate Functions

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Aggregate Functions

**Aggregate Functions:**
- COUNT: Đếm số rows
- SUM: Tổng
- AVG: Trung bình
- MIN: Giá trị nhỏ nhất
- MAX: Giá trị lớn nhất

**COUNT(*) vs COUNT(column) vs COUNT(DISTINCT column):**
- COUNT(*): Đếm tất cả rows
- COUNT(column): Đếm rows có column không NULL
- COUNT(DISTINCT column): Đếm số giá trị unique

**NULL handling:**
- COUNT(*): Đếm tất cả (kể cả NULL)
- COUNT(column): Bỏ qua NULL
- SUM, AVG, MIN, MAX: Bỏ qua NULL

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Aggregate Queries

**a)**
```sql
SELECT COUNT(*) FROM users;
```

**b)**
```sql
SELECT SUM(total_amount) FROM orders;
```

**c)**
```sql
SELECT AVG(price) FROM products;
```

**d)**
```sql
SELECT MIN(price), MAX(price) FROM products;
```

---

**Chúc mừng hoàn thành Day-021!** 🎉
