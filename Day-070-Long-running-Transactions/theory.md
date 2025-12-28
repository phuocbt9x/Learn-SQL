# Day-070: Long-running Transactions

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Vấn đề của long-running transaction
- Lock duration
- Cách tránh long-running transactions
- Best practices

---

## 1️⃣ VẤN ĐỀ CỦA LONG-RUNNING TRANSACTION

**Vấn đề:**
- **Locks**: Giữ locks lâu → block other transactions
- **Resources**: Tốn resources
- **Deadlocks**: Tăng nguy cơ deadlock
- **Performance**: Ảnh hưởng performance

---

## 2️⃣ LOCK DURATION

**Lock duration:**
- Locks được giữ trong suốt transaction
- Long transaction → locks lâu
- Block other transactions

**Ví dụ:**
```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;  -- Lock
  -- Wait 10 phút (user input, API call, etc.)
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;  -- Lock released
```

**Vấn đề:** Lock giữ 10 phút → block other transactions.

---

## 3️⃣ CÁCH TRÁNH LONG-RUNNING TRANSACTIONS

**Best practices:**
- **Short transactions**: Giữ transactions ngắn
- **No external calls**: Không gọi external APIs trong transaction
- **No user input**: Không chờ user input trong transaction
- **Batch operations**: Chia nhỏ operations

---

## 4️⃣ PRODUCTION STORY: TRANSACTION 10 PHÚT LÀM BLOCK TOÀN BỘ SYSTEM

**Context:**
Transaction 10 phút (chờ user input) → block toàn bộ system.

**Fix:**
Chia transaction → short transactions → không block.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Long-running transactions: Giữ locks lâu, block others
2. Lock duration: Locks giữ trong suốt transaction
3. Cách tránh: Short transactions, no external calls
4. Best practice: Giữ transactions < 1 giây (thường)

---

**Chuẩn bị cho Phase 4.3!** 🚀


**Chuẩn bị cho [Day-071: Lock-Contention](../Day-071-Lock-Contention/theory.md)** 🚀
