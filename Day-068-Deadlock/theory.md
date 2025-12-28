# Day-068: Deadlock - Hiểu và xử lý

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Deadlock là gì?
- Tại sao deadlock xảy ra?
- Cách tránh deadlock
- Cách detect và fix deadlock

---

## 1️⃣ DEADLOCK LÀ GÌ?

**Deadlock** là tình huống **2+ transactions chờ nhau**:

```
Transaction 1: Lock A, chờ B
Transaction 2: Lock B, chờ A
→ Deadlock!
```

**Kết quả:**
- Cả 2 transactions bị block
- Database tự động detect và rollback một transaction

---

## 2️⃣ TẠI SAO DEADLOCK XẢY RA?

**Nguyên nhân:**
- Locks theo thứ tự khác nhau
- Long-running transactions
- Multiple resources

**Ví dụ:**
```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- Transaction 2 (cùng lúc)
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 2;
  UPDATE accounts SET balance = balance + 100 WHERE id = 1;
-- → Deadlock nếu locks theo thứ tự khác nhau
```

---

## 3️⃣ CÁCH TRÁNH DEADLOCK

**Best practices:**
- **Lock order**: Luôn lock theo cùng thứ tự
- **Short transactions**: Giữ transactions ngắn
- **Lock only needed**: Chỉ lock khi cần
- **Timeout**: Set lock timeout

---

## 4️⃣ PRODUCTION STORY: DEADLOCK TRONG PRODUCTION VÀ CÁCH FIX

**Context:**
Deadlock xảy ra thường xuyên → transactions bị rollback.

**Fix:**
Đảm bảo lock order nhất quán → không còn deadlock.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Deadlock: Transactions chờ nhau
2. Nguyên nhân: Lock order khác nhau
3. Cách tránh: Lock order nhất quán, short transactions
4. Best practice: Luôn lock theo cùng thứ tự

---






**Chuẩn bị cho [Day-069: MVCC](../Day-069-MVCC/theory.md)** 🚀
