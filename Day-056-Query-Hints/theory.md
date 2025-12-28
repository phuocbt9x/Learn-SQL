# Day-056: Query Hints (nếu database hỗ trợ)

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Query hints là gì?
- Khi nào dùng hints?
- Trade-offs
- Best practices

---

## 1️⃣ QUERY HINTS LÀ GÌ?

**Query hints** là cách **force database** chọn execution plan cụ thể:

```sql
-- PostgreSQL: Không hỗ trợ hints trực tiếp
-- MySQL: Có thể force index
SELECT * FROM users FORCE INDEX (idx_users_email) WHERE email = 'john@example.com';
```

**Đặc điểm:**
- Override Planner decision
- Có thể tốt hoặc xấu
- Không phải database nào cũng hỗ trợ

---

## 2️⃣ KHI NÀO DÙNG HINTS?

**Dùng khi:**
- Planner chọn plan sai
- Đã thử optimize nhưng Planner vẫn chọn sai
- Edge cases

**KHÔNG nên dùng khi:**
- Chưa thử optimize (indexes, statistics)
- Có thể fix bằng cách khác

---

## 3️⃣ TRADE-OFFS

**Lợi ích:**
- Force plan tốt khi Planner chọn sai
- Control execution

**Rủi ro:**
- Plan có thể không tối ưu khi data thay đổi
- Khó maintain
- Database-specific

---

## 4️⃣ PRODUCTION STORY: FORCE INDEX KHI PLANNER CHỌN SAI

**Context:**
Planner chọn Seq Scan thay vì Index Scan → query chậm.

**Fix:**
Force index → query nhanh (temporary fix, cần fix root cause).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Query hints: Force execution plan
2. Khi nào dùng: Planner chọn sai, edge cases
3. Trade-offs: Có thể tốt hoặc xấu
4. Best practice: Chỉ dùng khi thực sự cần

---



**Chuẩn bị cho [Day-057: Materialized-Views](Day-057-Materialized-Views/theory.md)** 🚀
