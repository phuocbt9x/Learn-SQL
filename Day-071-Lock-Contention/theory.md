# Day-071: Lock Contention

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Lock contention là gì?
- Cách detect lock contention
- Cách giảm lock contention
- Monitoring lock contention

---

## 1️⃣ LOCK CONTENTION LÀ GÌ?

**Lock contention** là tình huống **nhiều transactions chờ cùng một lock**:

- Transaction 1: Giữ lock
- Transaction 2, 3, 4...: Chờ lock
- **Kết quả**: Performance tệ, queries chậm

---

## 2️⃣ CÁCH DETECT LOCK CONTENTION

**Detect bằng:**
- Lock wait time
- Number of waiting transactions
- Lock queries

**PostgreSQL:**
```sql
SELECT * FROM pg_locks WHERE NOT granted;
```

---

## 3️⃣ CÁCH GIẢM LOCK CONTENTION

**Best practices:**
- **Short transactions**: Giữ transactions ngắn
- **Lock only needed**: Chỉ lock khi cần
- **Lock order**: Lock theo cùng thứ tự
- **Partitioning**: Chia data để giảm contention

---

## 4️⃣ PRODUCTION STORY: HIGH LOCK WAIT TIME TRONG PRODUCTION

**Context:**
High lock wait time → queries chậm → users phàn nàn.

**Fix:**
Short transactions, lock only needed → lock wait time giảm 90%.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Lock contention: Nhiều transactions chờ cùng lock
2. Detect: Lock wait time, waiting transactions
3. Giảm: Short transactions, lock only needed
4. Best practice: Monitor và optimize lock contention

---






**Chuẩn bị cho [Day-072: Optimistic-vs-Pessimistic-Locking](../Day-072-Optimistic-vs-Pessimistic-Locking/theory.md)** 🚀
