# Day-032: Solutions - UNION, INTERSECT, EXCEPT

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: UNION là gì?

**UNION:** Kết hợp queries, loại bỏ duplicates.

**UNION vs UNION ALL:** UNION ALL nhanh hơn, giữ duplicates.

**INTERSECT/EXCEPT:** Set operations.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết UNION Queries

**a) UNION:**
```sql
SELECT name FROM users
UNION
SELECT name FROM admins;
```

**b) UNION ALL:**
```sql
SELECT name FROM users
UNION ALL
SELECT name FROM admins;
```

---

**Chúc mừng hoàn thành Day-032!** 🎉
