# Day-069: MVCC (Multi-Version Concurrency Control)

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- MVCC là gì?
- Versioning trong MVCC
- MVCC vs Locking
- Tại sao PostgreSQL không cần READ UNCOMMITTED

---

## 1️⃣ MVCC LÀ GÌ?

**MVCC (Multi-Version Concurrency Control)** là cơ chế **giữ nhiều versions** của data:

- Mỗi transaction thấy snapshot của data
- Không cần locks khi đọc
- Writes tạo version mới

---

## 2️⃣ VERSIONING TRONG MVCC

**Versioning:**
- Mỗi row có version (xmin, xmax)
- Transaction chỉ thấy versions visible
- Old versions được cleanup (VACUUM)

---

## 3️⃣ MVCC VS LOCKING

**MVCC:**
- Readers không block writers
- Writers không block readers
- Better concurrency

**Locking:**
- Readers có thể block writers
- Writers block readers
- Lower concurrency

---

## 4️⃣ TẠI SAO POSTGRESQL KHÔNG CẦN READ UNCOMMITTED?

**Lý do:**
- MVCC đảm bảo readers không thấy uncommitted data
- READ UNCOMMITTED không có ý nghĩa với MVCC
- READ COMMITTED là mức thấp nhất

---

## 5️⃣ PRODUCTION STORY: HIỂU TẠI SAO POSTGRESQL KHÔNG CẦN READ UNCOMMITTED

**Context:**
Developer cố set READ UNCOMMITTED → không có effect.

**Explanation:**
MVCC đảm bảo không có Dirty Read → READ UNCOMMITTED = READ COMMITTED.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. MVCC: Multi-version concurrency control
2. Versioning: Mỗi row có versions
3. MVCC vs Locking: MVCC tốt hơn cho concurrency
4. PostgreSQL: MVCC → không cần READ UNCOMMITTED

---






**Chuẩn bị cho [Day-070: Long-running-Transactions](../Day-070-Long-running-Transactions/theory.md)** 🚀
