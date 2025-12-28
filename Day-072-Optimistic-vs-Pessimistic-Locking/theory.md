# Day-072: Optimistic vs Pessimistic Locking

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Optimistic locking là gì?
- Pessimistic locking là gì?
- Khi nào dùng gì?
- Trade-offs

---

## 1️⃣ OPTIMISTIC LOCKING LÀ GÌ?

**Optimistic locking** giả định **không có conflict**:

- Đọc data (không lock)
- Modify data
- Check version/timestamp
- Update nếu version match, retry nếu conflict

**Ví dụ:**
```sql
-- Read với version
SELECT id, balance, version FROM accounts WHERE id = 1;

-- Update với version check
UPDATE accounts 
SET balance = balance - 100, version = version + 1 
WHERE id = 1 AND version = 1;
-- Nếu version khác → conflict → retry
```

---

## 2️⃣ PESSIMISTIC LOCKING LÀ GÌ?

**Pessimistic locking** giả định **sẽ có conflict**:

- Lock ngay khi đọc
- Modify data
- Release lock

**Ví dụ:**
```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;  -- Lock
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;  -- Release lock
```

---

## 3️⃣ KHI NÀO DÙNG GÌ?

**Optimistic locking:**
- Low conflict rate
- Read-heavy workloads
- Better concurrency

**Pessimistic locking:**
- High conflict rate
- Write-heavy workloads
- Need guaranteed consistency

---

## 4️⃣ PRODUCTION STORY: CONFLICT RESOLUTION VỚI OPTIMISTIC LOCKING

**Context:**
Pessimistic locking → nhiều locks → contention.

**Fix:**
Optimistic locking → ít locks → better concurrency.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Optimistic locking: Giả định không conflict, check version
2. Pessimistic locking: Lock ngay, giả định sẽ conflict
3. Khi nào dùng: Tùy conflict rate
4. Best practice: Optimistic cho low conflict, Pessimistic cho high conflict

---



**Chuẩn bị cho [Day-073: Transaction-Best-Practices](Day-073-Transaction-Best-Practices/theory.md)** 🚀
