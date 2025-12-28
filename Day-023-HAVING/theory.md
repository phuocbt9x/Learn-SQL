# Day-023: HAVING - Lọc sau GROUP BY

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- HAVING là gì?
- HAVING vs WHERE
- Khi nào dùng HAVING?

---

## 1️⃣ HAVING LÀ GÌ?

**HAVING** lọc kết quả **sau GROUP BY**:

```sql
SELECT status, COUNT(*) 
FROM orders 
GROUP BY status
HAVING COUNT(*) > 100;
```

---

## 2️⃣ HAVING VS WHERE

**WHERE:** Lọc rows **trước GROUP BY**
**HAVING:** Lọc groups **sau GROUP BY**

---

## 3️⃣ KHI NÀO DÙNG HAVING?

**Dùng HAVING khi:**
- Lọc theo aggregate functions
- Lọc sau GROUP BY

**Dùng WHERE khi:**
- Lọc rows trước GROUP BY

---

## 4️⃣ PRODUCTION STORY: BUG DO DÙNG WHERE THAY VÌ HAVING

**Context:**
Dùng WHERE với aggregate → error.

**Fix:**
Dùng HAVING với aggregate → đúng.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. HAVING: Lọc sau GROUP BY
2. HAVING vs WHERE: Khác nhau
3. Best practice: WHERE trước, HAVING sau

---



**Chuẩn bị cho [Day-024: JOIN-INNER](Day-024-JOIN-INNER/theory.md)** 🚀
