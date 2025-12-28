# Day-057: Solutions - Materialized Views

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Materialized Views

**Materialized View:** Pre-computed view, lưu kết quả.

**Khi nào dùng:** Query chậm, data không thay đổi thường xuyên.

**Refresh:** Manual hoặc scheduled.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Materialized View

**a)**
```sql
CREATE MATERIALIZED VIEW mv_user_stats AS
SELECT user_id, COUNT(*) as order_count, SUM(total_amount) as total_spent
FROM orders
GROUP BY user_id;
```

**b)**
```sql
REFRESH MATERIALIZED VIEW mv_user_stats;
```

---

**Chúc mừng hoàn thành Day-057!** 🎉
