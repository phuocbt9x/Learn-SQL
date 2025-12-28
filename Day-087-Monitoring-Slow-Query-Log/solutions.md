# Day-087: Solutions - Monitoring - Slow Query Log

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Slow Query Log là gì?

**Slow query log:** Log các queries chậm.

**Tại sao cần:** Identify slow queries, performance monitoring, optimization.

**Cách identify:** Enable log, monitor, analyze top queries.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Enable và Analyze Slow Query Log

**Solution:**

```sql
-- PostgreSQL
SET log_min_duration_statement = 1000;

-- Analyze top slow queries
SELECT query, calls, total_time, mean_time
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;
```

---

**Chúc mừng hoàn thành Day-087!** 🎉
