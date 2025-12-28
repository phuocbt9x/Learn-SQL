# Day-063: Isolation Levels - READ COMMITTED

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- READ COMMITTED là gì?
- Non-repeatable Read là gì?
- Khi nào dùng READ COMMITTED?
- Trade-offs

---

## 1️⃣ READ COMMITTED LÀ GÌ?

**READ COMMITTED** chỉ đọc **committed data**:

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

**Đặc điểm:**
- Chỉ đọc committed data
- Không có Dirty Read
- Có thể có Non-repeatable Read

---

## 2️⃣ NON-REPEATABLE READ

**Non-repeatable Read** là đọc **giá trị khác nhau** trong cùng transaction:

```sql
-- Transaction 1
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;  -- Balance = 1000
  -- Transaction 2 commit update
  SELECT balance FROM accounts WHERE id = 1;  -- Balance = 900 (khác!)
COMMIT;
```

---

## 3️⃣ KHI NÀO DÙNG READ COMMITTED?

**Dùng khi:**
- Mặc định (PostgreSQL)
- Cần đọc committed data
- Chấp nhận Non-repeatable Read

**Trade-off:**
- Performance tốt
- Có thể có Non-repeatable Read

---

## 4️⃣ PRODUCTION STORY: INCONSISTENT DATA DO READ COMMITTED

**Context:**
READ COMMITTED → Non-repeatable Read → data không nhất quán.

**Fix:**
Đổi sang REPEATABLE READ → đảm bảo repeatable reads.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. READ COMMITTED: Chỉ đọc committed data
2. Non-repeatable Read: Đọc giá trị khác nhau
3. Khi nào dùng: Mặc định, performance tốt
4. Trade-off: Có thể có Non-repeatable Read

---

**Chuẩn bị cho Day-064: Isolation Levels - REPEATABLE READ** 🚀
