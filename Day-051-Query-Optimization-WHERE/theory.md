# Day-051: Query Optimization - WHERE clause optimization

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Sargable vs Non-sargable predicates
- Function trong WHERE clause
- Cách optimize WHERE clause
- Hậu quả nếu dùng function trong WHERE

---

## 1️⃣ SARGABLE VS NON-SARGABLE PREDICATES

**Sargable (Search ARGument ABLE):**
- Có thể dùng index
- Column ở một phía của operator

**Non-sargable:**
- Không thể dùng index
- Function trên column

**Ví dụ:**
```sql
-- ✅ Sargable: Có thể dùng index
WHERE email = 'john@example.com'
WHERE created_at > '2024-01-01'

-- ❌ Non-sargable: Không thể dùng index
WHERE UPPER(email) = 'JOHN@EXAMPLE.COM'
WHERE DATE(created_at) = '2024-01-01'
```

---

## 2️⃣ FUNCTION TRONG WHERE CLAUSE

**Function trên column:**
```sql
-- ❌ SAI: Function trên column → không dùng index
WHERE UPPER(name) = 'JOHN'

-- ✅ ĐÚNG: Function trên value
WHERE name = UPPER('john')
```

---

## 3️⃣ CÁCH OPTIMIZE WHERE CLAUSE

**Best practices:**
- Tránh function trên column
- Dùng sargable predicates
- Có index trên WHERE columns

---

## 4️⃣ PRODUCTION STORY: QUERY CHẬM DO FUNCTION TRONG WHERE

**Context:**
Query dùng `UPPER(email)` trong WHERE → không dùng index → chậm 10s.

**Fix:**
Đổi `UPPER(email)` → `email = UPPER('value')` → dùng index → nhanh 0.1s.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Sargable: Có thể dùng index
2. Non-sargable: Không thể dùng index
3. Function trong WHERE: Tránh function trên column
4. Best practice: Dùng sargable predicates

---

**Chuẩn bị cho Day-052: Query Optimization - Subquery to JOIN** 🚀
