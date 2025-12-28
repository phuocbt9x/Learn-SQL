# Day-033: CASE Expression - Logic điều kiện

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- CASE WHEN là gì?
- Simple CASE vs Searched CASE
- CASE trong SELECT, WHERE, ORDER BY
- Khi nào dùng CASE?

---

## 1️⃣ CASE WHEN LÀ GÌ?

**CASE** là **conditional expression**:

```sql
SELECT name,
       CASE 
         WHEN age < 18 THEN 'Minor'
         WHEN age < 65 THEN 'Adult'
         ELSE 'Senior'
       END as age_group
FROM users;
```

---

## 2️⃣ SIMPLE CASE VS SEARCHED CASE

**Simple CASE:**
```sql
CASE status
  WHEN 'active' THEN 'Active User'
  WHEN 'inactive' THEN 'Inactive User'
  ELSE 'Unknown'
END
```

**Searched CASE:**
```sql
CASE
  WHEN age < 18 THEN 'Minor'
  WHEN age >= 18 AND age < 65 THEN 'Adult'
  ELSE 'Senior'
END
```

---

## 3️⃣ CASE TRONG SELECT, WHERE, ORDER BY

**Trong SELECT:**
```sql
SELECT name, CASE WHEN age >= 18 THEN 'Adult' ELSE 'Minor' END
FROM users;
```

**Trong WHERE:**
```sql
SELECT * FROM users
WHERE CASE WHEN status = 'active' THEN 1 ELSE 0 END = 1;
```

**Trong ORDER BY:**
```sql
SELECT * FROM users
ORDER BY CASE WHEN status = 'active' THEN 1 ELSE 2 END;
```

---

## 4️⃣ PRODUCTION STORY: LOGIC PHỨC TẠP ĐƯỢC XỬ LÝ BẰNG CASE

**Context:**
Logic phức tạp trong application code → khó maintain.

**Fix:**
Dùng CASE trong SQL → logic rõ ràng, dễ maintain.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. CASE: Conditional expression
2. Simple vs Searched: Khác nhau
3. Dùng trong: SELECT, WHERE, ORDER BY
4. Best practice: Logic rõ ràng

---

**Chuẩn bị cho Day-034: String Functions** 🚀
