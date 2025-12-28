# Day-053: Solutions - Query Optimization - Avoid SELECT *

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Avoid SELECT *

**Tại sao tránh:** Tốn network, memory, không tận dụng index.

**Impact:** Network, memory, index usage.

**Khi nào dùng:** Development/testing only.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize SELECT Queries

**a) SELECT *:**
```sql
-- ❌ SAI
SELECT * FROM orders WHERE user_id = 123;
```

**b) SELECT column_list:**
```sql
-- ✅ ĐÚNG
SELECT id, user_id, total_amount, created_at 
FROM orders 
WHERE user_id = 123;
```

---

**Chúc mừng hoàn thành Day-053!** 🎉
