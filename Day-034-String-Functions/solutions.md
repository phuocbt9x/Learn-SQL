# Day-034: Solutions - String Functions

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: String Functions

**String Functions:** CONCAT, SUBSTRING, LENGTH, UPPER, LOWER.

**LIKE vs ILIKE:** LIKE case-sensitive, ILIKE case-insensitive.

**Pattern matching:** %, _ wildcards.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết String Function Queries

**a)**
```sql
SELECT CONCAT(first_name, ' ', last_name) as full_name FROM users;
```

**b)**
```sql
SELECT * FROM users WHERE name LIKE 'John%';
```

**c)**
```sql
SELECT SUBSTRING(email FROM POSITION('@' IN email) + 1) as domain FROM users;
```

---

**Chúc mừng hoàn thành Day-034!** 🎉
