# Day-041: Solutions - EXPLAIN - Đọc Execution Plan

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: EXPLAIN là gì?

**EXPLAIN:** Hiển thị execution plan.

**Execution Plan:** Kế hoạch thực thi query.

**Đọc plan:** Seq Scan (chậm), Index Scan (nhanh), Hash Join (JOIN strategy).

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Đọc Execution Plans

**Ví dụ:**
```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

**Phân tích:**
- Nếu Seq Scan → cần index
- Nếu Index Scan → tốt

---

**Chúc mừng hoàn thành Day-041!** 🎉
