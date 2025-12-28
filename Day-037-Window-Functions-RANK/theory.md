# Day-037: Window Functions - ROW_NUMBER, RANK, DENSE_RANK

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- ROW_NUMBER() là gì?
- RANK() vs DENSE_RANK()
- PARTITION BY trong Window Functions
- Khi nào dùng mỗi function?

---

## 1️⃣ ROW_NUMBER()

**ROW_NUMBER()** đánh số rows:

```sql
SELECT name, 
       salary,
       ROW_NUMBER() OVER(ORDER BY salary DESC) as row_num
FROM employees;
```

**Đặc điểm:**
- Luôn unique (không có ties)
- 1, 2, 3, 4, ...

---

## 2️⃣ RANK() VS DENSE_RANK()

**RANK():**
- Có thể có ties (cùng rank)
- Skip numbers sau ties
- 1, 2, 2, 4, 5, ...

**DENSE_RANK():**
- Có thể có ties
- Không skip numbers
- 1, 2, 2, 3, 4, ...

---

## 3️⃣ PARTITION BY

**PARTITION BY** chia window thành partitions:

```sql
SELECT name, 
       department,
       salary,
       ROW_NUMBER() OVER(PARTITION BY department ORDER BY salary DESC) as rank
FROM employees;
```

**Kết quả:** Rank riêng cho mỗi department.

---

## 4️⃣ PRODUCTION STORY: TOP N PER GROUP VỚI ROW_NUMBER()

**Context:**
Cần top 3 employees mỗi department.

**Solution:**
Dùng ROW_NUMBER() với PARTITION BY → đơn giản, hiệu quả.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. ROW_NUMBER(): Đánh số unique
2. RANK(): Có ties, skip numbers
3. DENSE_RANK(): Có ties, không skip
4. PARTITION BY: Chia window

---



**Chuẩn bị cho [Day-038: Window-Functions-Aggregate](Day-038-Window-Functions-Aggregate/theory.md)** 🚀
