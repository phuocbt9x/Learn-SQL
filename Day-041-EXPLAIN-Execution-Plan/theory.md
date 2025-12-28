# Day-041: EXPLAIN - Đọc Execution Plan

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- EXPLAIN là gì?
- Execution Plan là gì?
- Cách đọc plan (Seq Scan, Index Scan, Hash Join, etc.)
- Cách debug query chậm bằng EXPLAIN

---

## 1️⃣ EXPLAIN LÀ GÌ?

**EXPLAIN** hiển thị **execution plan** mà database sẽ dùng để thực thi query:

```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

**Kết quả:**
```
Index Scan using idx_users_email on users
  Index Cond: (email = 'john@example.com')
```

---

## 2️⃣ EXECUTION PLAN LÀ GÌ?

**Execution Plan** là kế hoạch thực thi query do Planner tạo ra, mô tả:
- **Operations**: Seq Scan, Index Scan, Hash Join, etc.
- **Cost**: Ước tính chi phí
- **Rows**: Ước tính số rows

---

## 3️⃣ CÁCH ĐỌC PLAN

**Seq Scan:**
- Full Table Scan
- Chậm với large tables

**Index Scan:**
- Dùng index
- Nhanh hơn Seq Scan

**Hash Join:**
- JOIN strategy
- Tốt cho large datasets

---

## 4️⃣ PRODUCTION STORY: DEBUG QUERY CHẬM BẰNG EXPLAIN

**Context:**
Query chậm 10s → cần debug.

**Solution:**
Dùng EXPLAIN → phát hiện Seq Scan → tạo index → query nhanh 0.1s.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. EXPLAIN: Hiển thị execution plan
2. Execution Plan: Kế hoạch thực thi
3. Đọc plan: Seq Scan, Index Scan, Hash Join
4. Debug: Dùng EXPLAIN để debug query chậm

---

**Chuẩn bị cho Day-042: EXPLAIN ANALYZE** 🚀
