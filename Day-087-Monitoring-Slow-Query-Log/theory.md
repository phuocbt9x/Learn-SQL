# Day-087: Monitoring - Slow Query Log

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Slow query log là gì?
- Cách identify slow queries
- Cách analyze slow queries
- Tìm và fix top slow queries

---

## 1️⃣ SLOW QUERY LOG LÀ GÌ?

**Slow query log** là **log các queries chậm**:

```sql
-- PostgreSQL: Enable slow query log
SET log_min_duration_statement = 1000;  -- Log queries > 1 giây

-- MySQL: Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;  -- Log queries > 1 giây
```

**Đặc điểm:**
- Log queries vượt threshold
- Giúp identify performance issues
- Cần analyze để optimize

---

## 2️⃣ TẠI SAO TỒN TẠI SLOW QUERY LOG?

**Slow query log tồn tại để:**
- **Identify slow queries**: Tìm queries chậm
- **Performance monitoring**: Monitor performance
- **Optimization**: Tối ưu queries
- **Debugging**: Debug performance issues

**Nếu không có:**
- Khó biết queries nào chậm
- Khó optimize
- Performance issues không được phát hiện

---

## 3️⃣ CÁCH IDENTIFY SLOW QUERIES

**Steps:**
1. Enable slow query log
2. Monitor logs
3. Identify top slow queries
4. Analyze queries
5. Optimize queries

**Tools:**
- pg_stat_statements (PostgreSQL)
- Slow query log (MySQL)
- Query performance insights

---

## 4️⃣ PRODUCTION STORY: TÌM VÀ FIX TOP 10 SLOW QUERIES

**Context:**
Database chậm → users phàn nàn → cần tìm và fix slow queries.

**Problem:**
- Không biết queries nào chậm
- Performance tệ
- Users phàn nàn

**Investigation:**
- Enable slow query log
- Analyze logs
- Identify top 10 slow queries

**Root Cause:**
- Thiếu indexes
- Queries không optimize
- N+1 queries

**Fix:**
1. Add indexes
2. Optimize queries
3. Fix N+1 queries

**Result:**
- Performance cải thiện 10x
- Users hài lòng
- Database load giảm

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Slow query log**: Log queries chậm
2. **Identify**: Tìm top slow queries
3. **Analyze**: Phân tích queries
4. **Optimize**: Tối ưu queries

---



**Chuẩn bị cho [Day-088: Monitoring-Query-Metrics](Day-088-Monitoring-Query-Metrics/theory.md)** 🚀
