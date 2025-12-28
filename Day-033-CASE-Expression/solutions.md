# Day-033: Solutions - CASE Expression

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: CASE là gì?

**CASE:** Conditional expression.

**Simple vs Searched:** Simple so sánh giá trị, Searched so sánh điều kiện.

**Dùng trong:** SELECT, WHERE, ORDER BY.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết CASE Queries

**a)**
```sql
SELECT name,
       CASE 
         WHEN age < 18 THEN 'Minor'
         WHEN age < 65 THEN 'Adult'
         ELSE 'Senior'
       END as age_group
FROM users;
```

**b)**
```sql
SELECT * FROM users
ORDER BY CASE WHEN status = 'active' THEN 1 ELSE 2 END;
```

---

**Chúc mừng hoàn thành Day-033!** 🎉
