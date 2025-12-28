# Day-020: DISTINCT - Loại bỏ trùng lặp

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- DISTINCT là gì?
- DISTINCT vs GROUP BY
- Performance impact
- Khi nào dùng DISTINCT?

---

## 1️⃣ DISTINCT LÀ GÌ?

**DISTINCT** loại bỏ rows trùng lặp:

```sql
SELECT DISTINCT status FROM orders;
```

---

## 2️⃣ DISTINCT VS GROUP BY

**DISTINCT:**
- Loại bỏ duplicates
- Không aggregate

**GROUP BY:**
- Nhóm rows
- Có thể aggregate

---

## 3️⃣ PERFORMANCE IMPACT

**DISTINCT có thể chậm:**
- Phải sort để tìm duplicates
- Tốn memory

**Best practice:**
- Chỉ dùng khi cần
- Cân nhắc GROUP BY nếu cần aggregate

---

## 4️⃣ PRODUCTION STORY: DISTINCT LÀM QUERY CHẬM DO SORT

**Context:**
DISTINCT trên 10 triệu rows → query chậm do sort.

**Fix:**
Dùng GROUP BY hoặc optimize query.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. DISTINCT: Loại bỏ duplicates
2. DISTINCT vs GROUP BY: Khác nhau
3. Performance: DISTINCT có thể chậm
4. Best practice: Chỉ dùng khi cần

---

**Chuẩn bị cho Phase 2.2!** 🚀


**Chuẩn bị cho [Day-021: Aggregate-Functions](../Day-021-Aggregate-Functions/theory.md)** 🚀
