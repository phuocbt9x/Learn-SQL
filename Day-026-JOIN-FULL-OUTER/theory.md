# Day-026: JOIN - FULL OUTER JOIN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- FULL OUTER JOIN là gì?
- Khi nào dùng FULL OUTER JOIN?
- FULL OUTER JOIN vs UNION
- Hậu quả nếu dùng sai JOIN type

---

## 1️⃣ FULL OUTER JOIN LÀ GÌ?

**FULL OUTER JOIN** trả về **tất cả rows từ cả 2 tables**:

```sql
SELECT u.name, o.total_amount
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;
```

**Kết quả:**
- Tất cả users (kể cả không có orders)
- Tất cả orders (kể cả không có user)
- Users có orders (matching rows)

---

## 2️⃣ KHI NÀO DÙNG FULL OUTER JOIN?

**Dùng FULL OUTER JOIN khi:**
- Cần tất cả rows từ cả 2 tables
- Data reconciliation
- Tìm rows không có match ở cả 2 phía

---

## 3️⃣ FULL OUTER JOIN VS UNION

**FULL OUTER JOIN:**
- Kết hợp columns từ 2 tables
- Một row cho mỗi match/non-match

**UNION:**
- Kết hợp rows từ 2 queries
- Cùng structure

---

## 4️⃣ PRODUCTION STORY: DATA RECONCILIATION VỚI FULL OUTER JOIN

**Context:**
Cần reconcile data giữa 2 systems.

**Solution:**
Dùng FULL OUTER JOIN để tìm differences.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. FULL OUTER JOIN: Tất cả rows từ cả 2 tables
2. Khi nào dùng: Data reconciliation
3. FULL OUTER JOIN vs UNION: Khác nhau
4. Best practice: Hiểu khi nào dùng

---



**Chuẩn bị cho [Day-027: JOIN-Multiple-Tables](../Day-027-JOIN-Multiple-Tables/theory.md)** 🚀
