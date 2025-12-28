# Day-022: GROUP BY - Nhóm dữ liệu

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- GROUP BY là gì?
- Tại sao cần GROUP BY?
- GROUP BY với multiple columns
- GROUP BY execution flow

---

## 1️⃣ GROUP BY LÀ GÌ?

**GROUP BY** nhóm rows có cùng giá trị:

```sql
SELECT status, COUNT(*) 
FROM orders 
GROUP BY status;
```

**Kết quả:**
```
status    | count
----------|------
pending   | 100
completed | 500
cancelled | 50
```

---

## 2️⃣ TẠI SAO CẦN GROUP BY?

**GROUP BY** cần thiết khi:
- Tính toán aggregate theo nhóm
- Tạo reports theo category
- Phân tích dữ liệu theo nhóm

---

## 3️⃣ GROUP BY VỚI MULTIPLE COLUMNS

```sql
SELECT user_id, status, COUNT(*) 
FROM orders 
GROUP BY user_id, status;
```

---

## 4️⃣ GROUP BY EXECUTION FLOW

1. Scan rows
2. Group by columns
3. Apply aggregate functions
4. Return results

---

## 5️⃣ PRODUCTION STORY: QUERY CHẬM DO GROUP BY KHÔNG CÓ INDEX

**Context:**
GROUP BY trên 10 triệu rows không có index → query chậm.

**Fix:**
Tạo index trên columns GROUP BY → query nhanh hơn 100x.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. GROUP BY: Nhóm rows
2. Aggregate: Tính toán theo nhóm
3. Multiple columns: GROUP BY nhiều columns
4. Performance: Cần index cho GROUP BY

---



**Chuẩn bị cho [Day-023: HAVING](Day-023-HAVING/theory.md)** 🚀
