# Day-088: Solutions - Monitoring - Query Metrics

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Query Metrics là gì?

**Query metrics:** Các chỉ số đo lường performance.

**Rows examined vs returned:** Examined càng ít càng tốt, efficiency = examined/returned.

**Identify:** Analyze metrics, tìm queries có examined >> returned.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Analyze Query Metrics

**Solution:**

```sql
-- Analyze query
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

-- Check rows examined vs returned
-- Nếu examined >> returned → add index
```

---

**Chúc mừng hoàn thành Day-088!** 🎉
