# Day-059: Solutions - Common Performance Anti-patterns

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Anti-patterns

**N+1 queries:** 1 + N queries, fix bằng JOIN.

**Cartesian products:** JOIN không có condition, fix bằng JOIN condition.

**Unnecessary DISTINCT:** DISTINCT không cần, fix bằng remove DISTINCT.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Identify và Fix Anti-patterns

**a) N+1 queries:**
```sql
-- ❌ SAI
SELECT * FROM users;
-- Sau đó query orders cho mỗi user

-- ✅ ĐÚNG
SELECT u.*, o.* FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

**b) Cartesian products:**
```sql
-- ❌ SAI
SELECT * FROM users, orders;

-- ✅ ĐÚNG
SELECT * FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

---

**Chúc mừng hoàn thành Day-059!** 🎉
