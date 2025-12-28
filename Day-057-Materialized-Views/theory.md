# Day-057: Materialized Views

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Materialized View là gì?
- Khi nào dùng Materialized View?
- Refresh strategies
- Trade-offs

---

## 1️⃣ MATERIALIZED VIEW LÀ GÌ?

**Materialized View** là view được **pre-computed và lưu kết quả**:

```sql
CREATE MATERIALIZED VIEW mv_user_stats AS
SELECT user_id, COUNT(*) as order_count, SUM(total_amount) as total_spent
FROM orders
GROUP BY user_id;
```

**Đặc điểm:**
- Pre-computed: Tính toán trước
- Stored: Lưu kết quả
- Fast: Query nhanh (không cần tính lại)

---

## 2️⃣ KHI NÀO DÙNG MATERIALIZED VIEW?

**Dùng khi:**
- Query phức tạp, chậm
- Data không thay đổi thường xuyên
- Cần performance tốt
- Reports, analytics

**KHÔNG nên dùng khi:**
- Data thay đổi thường xuyên
- Cần real-time data

---

## 3️⃣ REFRESH STRATEGIES

**Manual refresh:**
```sql
REFRESH MATERIALIZED VIEW mv_user_stats;
```

**Scheduled refresh:**
- Cron job
- Định kỳ (hourly, daily)

**Trade-off:**
- Refresh frequency vs Data freshness

---

## 4️⃣ PRODUCTION STORY: REPORT QUERY TỪ 30S → 0.5S VỚI MATERIALIZED VIEW

**Context:**
Report query phức tạp → chậm 30s.

**Fix:**
Tạo Materialized View → query nhanh 0.5s (nhanh hơn 60x).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Materialized View: Pre-computed view
2. Khi nào dùng: Query chậm, data không thay đổi thường xuyên
3. Refresh: Manual hoặc scheduled
4. Best practice: Balance refresh frequency vs data freshness

---



**Chuẩn bị cho [Day-058: Partitioning-Concept](../Day-058-Partitioning-Concept/theory.md)** 🚀
