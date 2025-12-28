# Day-042: EXPLAIN ANALYZE - Thực tế execution

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- EXPLAIN ANALYZE là gì?
- Actual vs Estimated rows
- Timing information
- Cách debug với EXPLAIN ANALYZE

---

## 1️⃣ EXPLAIN ANALYZE LÀ GÌ?

**EXPLAIN ANALYZE** thực thi query và hiển thị **actual execution statistics**:

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';
```

**Kết quả:**
```
Index Scan using idx_users_email on users (cost=0.43..8.45 rows=1 width=100) (actual time=0.123..0.123 rows=1 loops=1)
  Index Cond: (email = 'john@example.com')
Execution Time: 0.123 ms
```

---

## 2️⃣ ACTUAL VS ESTIMATED ROWS

**Estimated rows:**
- Planner estimate
- Có thể sai

**Actual rows:**
- Thực tế sau khi execute
- Chính xác

**So sánh:**
- Nếu actual >> estimated → Planner estimate sai → cần update statistics

---

## 3️⃣ TIMING INFORMATION

**Execution Time:**
- Thời gian thực tế execute query
- Quan trọng để đo performance

---

## 4️⃣ PRODUCTION STORY: PLANNER ESTIMATE SAI → QUERY CHẬM

**Context:**
Planner estimate sai → chọn plan sai → query chậm.

**Fix:**
Update statistics → Planner estimate đúng → chọn plan đúng → query nhanh.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. EXPLAIN ANALYZE: Thực thi và hiển thị statistics
2. Actual vs Estimated: So sánh để phát hiện vấn đề
3. Timing: Quan trọng để đo performance
4. Debug: Dùng EXPLAIN ANALYZE để debug

---



**Chuẩn bị cho [Day-043: Index-B-Tree](../Day-043-Index-B-Tree/theory.md)** 🚀
