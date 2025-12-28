# Day-059: Common Performance Anti-patterns

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- N+1 queries
- Cartesian products
- Unnecessary DISTINCT
- Các anti-patterns khác
- Cách tránh anti-patterns

---

## 1️⃣ N+1 QUERIES

**N+1 queries:**
- 1 query lấy list
- N queries cho mỗi item
- Tổng: 1 + N queries

**Ví dụ:**
```sql
-- Query 1: Lấy users
SELECT * FROM users;

-- Query 2-N: Lấy orders cho mỗi user
SELECT * FROM orders WHERE user_id = 1;
SELECT * FROM orders WHERE user_id = 2;
-- ...
```

**Fix:**
```sql
-- 1 query với JOIN
SELECT u.*, o.* FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

---

## 2️⃣ CARTESIAN PRODUCTS

**Cartesian product:**
- JOIN không có condition
- Kết quả: n × m rows

**Ví dụ:**
```sql
-- ❌ SAI: Không có JOIN condition
SELECT * FROM users, orders;
-- Kết quả: 100 users × 1000 orders = 100,000 rows!
```

**Fix:**
```sql
-- ✅ ĐÚNG: Có JOIN condition
SELECT * FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

---

## 3️⃣ UNNECESSARY DISTINCT

**Unnecessary DISTINCT:**
- DISTINCT khi không cần
- Tốn resources để sort và remove duplicates

**Ví dụ:**
```sql
-- ❌ SAI: DISTINCT không cần
SELECT DISTINCT id, name FROM users WHERE id = 1;
-- id là PK → đã unique, không cần DISTINCT

-- ✅ ĐÚNG
SELECT id, name FROM users WHERE id = 1;
```

---

## 4️⃣ PRODUCTION STORY: TỔNG HỢP CÁC LỖI PERFORMANCE THƯỜNG GẶP

**Context:**
Nhiều anti-patterns → performance tệ.

**Fix:**
Identify và fix từng anti-pattern → performance tốt hơn.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. N+1 queries: Tránh bằng JOIN
2. Cartesian products: Luôn có JOIN condition
3. Unnecessary DISTINCT: Chỉ dùng khi cần
4. Best practice: Identify và fix anti-patterns

---



**Chuẩn bị cho [Day-060: Review-Phase3](Day-060-Review-Phase3/theory.md)** 🚀
