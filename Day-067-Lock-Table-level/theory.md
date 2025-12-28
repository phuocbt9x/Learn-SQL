# Day-067: Lock - Table-level Lock

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Table lock là gì?
- SHARE LOCK vs EXCLUSIVE LOCK
- Khi nào dùng table lock?
- Impact của table lock

---

## 1️⃣ TABLE LOCK LÀ GÌ?

**Table lock** lock **toàn bộ table**:

```sql
LOCK TABLE users IN SHARE MODE;
LOCK TABLE users IN EXCLUSIVE MODE;
```

**Đặc điểm:**
- Lock toàn bộ table
- Ảnh hưởng đến tất cả operations
- Granular hơn row-level lock

---

## 2️⃣ SHARE LOCK VS EXCLUSIVE LOCK

**SHARE LOCK:**
- Nhiều transactions có thể đọc
- Không có transaction nào có thể write

**EXCLUSIVE LOCK:**
- Chỉ một transaction có thể access
- Không có transaction nào khác có thể read/write

---

## 3️⃣ KHI NÀO DÙNG TABLE LOCK?

**Dùng khi:**
- DDL operations (ALTER TABLE, etc.)
- Bulk operations
- Maintenance

**KHÔNG nên dùng khi:**
- Normal operations
- High concurrency

---

## 4️⃣ PRODUCTION STORY: TABLE LOCK LÀM TOÀN BỘ APP CHẬM

**Context:**
Table lock → block tất cả queries → app chậm.

**Fix:**
Tránh table lock trong production, dùng row-level lock khi có thể.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Table lock: Lock toàn bộ table
2. SHARE vs EXCLUSIVE: Khác nhau về concurrency
3. Khi nào dùng: DDL, bulk operations
4. Best practice: Tránh table lock trong production

---



**Chuẩn bị cho [Day-068: Deadlock](Day-068-Deadlock/theory.md)** 🚀
