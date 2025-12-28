# Day-088: Monitoring - Query Metrics

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Execution time
- Rows examined vs rows returned
- Query efficiency
- Identify inefficient queries

---

## 1️⃣ QUERY METRICS LÀ GÌ?

**Query metrics** là **các chỉ số đo lường** performance của queries:

- **Execution time**: Thời gian thực thi
- **Rows examined**: Số rows đọc
- **Rows returned**: Số rows trả về
- **Index usage**: Sử dụng index

---

## 2️⃣ ROWS EXAMINED VS ROWS RETURNED

**Rows examined:**
- Số rows database đọc
- Càng ít càng tốt

**Rows returned:**
- Số rows trả về
- Có thể ít hơn rows examined

**Efficiency:**
- Rows examined / Rows returned → càng gần 1 càng tốt
- Nếu examined >> returned → query không efficient

---

## 3️⃣ PRODUCTION STORY: QUERY SCAN 1M ROWS NHƯNG CHỈ RETURN 10 ROWS

**Context:**
Query scan 1 triệu rows nhưng chỉ return 10 rows → chậm.

**Problem:**
- Query không efficient
- Scan quá nhiều rows
- Performance tệ

**Fix:**
- Add index
- Optimize WHERE clause
- Result: Scan 10 rows, return 10 rows

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Query metrics**: Đo lường performance
2. **Rows examined vs returned**: Efficiency indicator
3. **Optimize**: Giảm rows examined

---

**Chuẩn bị cho Day-089: SQL Injection - Security** 🚀
