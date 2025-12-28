# Day-055: Statistics & Query Planner

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Statistics là gì?
- ANALYZE command
- Planner dùng statistics như thế nào?
- Khi nào cần update statistics?

---

## 1️⃣ STATISTICS LÀ GÌ?

**Statistics** là thông tin về data distribution:
- Số rows
- Data distribution
- Distinct values
- NULL percentage

**Planner dùng statistics để:**
- Estimate cost
- Chọn execution plan tốt nhất

---

## 2️⃣ ANALYZE COMMAND

**ANALYZE** update statistics:

```sql
ANALYZE users;
ANALYZE orders;
```

**Khi nào chạy ANALYZE:**
- Sau khi insert/update/delete nhiều data
- Sau khi bulk load
- Định kỳ (cron job)

---

## 3️⃣ PLANNER DÙNG STATISTICS NHƯ THẾ NÀO?

**Planner dùng statistics để:**
- Estimate số rows trả về
- Estimate cost của operations
- Chọn plan tốt nhất

**Nếu statistics sai:**
- Planner estimate sai
- Chọn plan sai
- Query chậm

---

## 4️⃣ KHI NÀO CẦN UPDATE STATISTICS?

**Cần update khi:**
- Data thay đổi nhiều
- Statistics cũ
- Planner estimate sai
- Query performance tệ

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Statistics: Thông tin về data distribution
2. ANALYZE: Update statistics
3. Planner: Dùng statistics để chọn plan
4. Best practice: Update statistics định kỳ

---

**Chuẩn bị cho Phase 3.4!** 🚀


**Chuẩn bị cho [Day-056: Query-Hints](../Day-056-Query-Hints/theory.md)** 🚀
