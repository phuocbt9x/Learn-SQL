# Day-069: Solutions - MVCC

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: MVCC

**MVCC:** Multi-version concurrency control, giữ nhiều versions.

**Versioning:** Mỗi row có versions (xmin, xmax).

**MVCC vs Locking:** MVCC tốt hơn cho concurrency.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Hiểu MVCC Behavior

**Test:**
```sql
-- Transaction 1
BEGIN;
  SELECT * FROM accounts WHERE id = 1;  -- Snapshot

-- Transaction 2
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  COMMIT;

-- Transaction 1 vẫn thấy old value (snapshot)
  SELECT * FROM accounts WHERE id = 1;  -- Old value
COMMIT;
```

---

**Chúc mừng hoàn thành Day-069!** 🎉
