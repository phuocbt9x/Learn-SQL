# Day-071: Solutions - Lock Contention

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Lock Contention

**Lock contention:** Nhiều transactions chờ cùng lock.

**Detect:** Lock wait time, waiting transactions.

**Giảm:** Short transactions, lock only needed.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Detect và Fix Lock Contention

**a) Detect:**
```sql
SELECT * FROM pg_locks WHERE NOT granted;
```

**b) Fix:**
- Short transactions
- Lock only needed
- Lock order nhất quán

---

**Chúc mừng hoàn thành Day-071!** 🎉
