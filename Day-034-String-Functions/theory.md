# Day-034: String Functions

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- CONCAT, SUBSTRING, LENGTH, UPPER, LOWER
- LIKE, ILIKE, pattern matching
- Regular expressions (high-level)
- Performance impact của string functions

---

## 1️⃣ STRING FUNCTIONS CƠ BẢN

**CONCAT:**
```sql
SELECT CONCAT(first_name, ' ', last_name) as full_name FROM users;
```

**SUBSTRING:**
```sql
SELECT SUBSTRING(email, 1, 5) FROM users;
```

**LENGTH:**
```sql
SELECT LENGTH(name) FROM users;
```

**UPPER, LOWER:**
```sql
SELECT UPPER(name), LOWER(email) FROM users;
```

---

## 2️⃣ LIKE, ILIKE, PATTERN MATCHING

**LIKE:**
```sql
SELECT * FROM users WHERE name LIKE 'John%';
```

**ILIKE (case-insensitive):**
```sql
SELECT * FROM users WHERE name ILIKE 'john%';
```

**Pattern:**
- `%`: Bất kỳ ký tự nào
- `_`: Một ký tự

---

## 3️⃣ REGULAR EXPRESSIONS (HIGH-LEVEL)

**PostgreSQL:**
```sql
SELECT * FROM users WHERE email ~ '^[a-z]+@example\.com$';
```

---

## 4️⃣ PRODUCTION STORY: QUERY CHẬM DO LIKE '%PATTERN%'

**Context:**
Query dùng `LIKE '%pattern%'` → không dùng index → chậm.

**Fix:**
Dùng `LIKE 'pattern%'` → có thể dùng index → nhanh hơn.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. String functions: CONCAT, SUBSTRING, LENGTH, UPPER, LOWER
2. LIKE/ILIKE: Pattern matching
3. Regular expressions: Advanced pattern matching
4. Performance: LIKE '%pattern%' chậm, LIKE 'pattern%' nhanh hơn

---






**Chuẩn bị cho [Day-035: Date-Time-Functions](../Day-035-Date-Time-Functions/theory.md)** 🚀
