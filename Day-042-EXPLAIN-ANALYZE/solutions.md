# Day-042: Solutions - EXPLAIN ANALYZE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: EXPLAIN ANALYZE là gì?

**EXPLAIN ANALYZE:** Thực thi query và hiển thị actual statistics.

**Actual vs Estimated:** Actual là thực tế, Estimated là ước tính.

**Timing:** Execution Time là thời gian thực tế.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Sử dụng EXPLAIN ANALYZE

**Ví dụ:**
```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';
```

**Phân tích:**
- So sánh actual vs estimated rows
- Nếu khác nhiều → update statistics

---

**Chúc mừng hoàn thành Day-042!** 🎉
