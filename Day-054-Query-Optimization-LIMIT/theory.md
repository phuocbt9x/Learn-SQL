# Day-054: Query Optimization - LIMIT optimization

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- LIMIT với ORDER BY
- Index cho LIMIT queries
- Pagination optimization
- Cursor-based pagination

---

## 1️⃣ LIMIT VỚI ORDER BY

**LIMIT với ORDER BY:**
- Cần sort trước khi LIMIT
- Có thể chậm nếu không có index

**Optimize:**
- Index trên ORDER BY columns
- Giảm số rows sort

---

## 2️⃣ INDEX CHO LIMIT QUERIES

**Index cho LIMIT:**
```sql
-- Query
SELECT * FROM orders 
ORDER BY created_at DESC 
LIMIT 10;

-- Index
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);
```

**Kết quả:** Query nhanh hơn với index.

---

## 3️⃣ PAGINATION OPTIMIZATION

**OFFSET pagination:**
- OFFSET lớn → chậm
- Database phải skip nhiều rows

**Cursor-based pagination:**
- Dùng WHERE với last value
- Nhanh hơn OFFSET

---

## 4️⃣ PRODUCTION STORY: PAGINATION NHANH HƠN VỚI INDEX PHÙ HỢP

**Context:**
Pagination với OFFSET 10000+ → chậm 10s.

**Fix:**
Cursor-based pagination + index → nhanh 0.1s.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. LIMIT với ORDER BY: Cần index
2. Index cho LIMIT: Tạo index trên ORDER BY columns
3. Pagination: Cursor-based tốt hơn OFFSET
4. Best practice: Index + cursor-based pagination

---






**Chuẩn bị cho [Day-055: Statistics-Query-Planner](../Day-055-Statistics-Query-Planner/theory.md)** 🚀
