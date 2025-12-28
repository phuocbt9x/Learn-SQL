# Day-075: Solutions - Review Phase 4

## 🎯 BÀI TẬP 1: TỔNG HỢP KIẾN THỨC

### Câu 1.1: Transactions & Concurrency

**Transactions:** ACID, lifecycle, savepoints.

**Isolation levels:** READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE.

**Locking:** Row-level, table-level, deadlocks.

**MVCC:** Multi-version concurrency control.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Complex Transaction Design

**Ví dụ:**
```sql
BEGIN;
  -- Lock order nhất quán
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  SELECT * FROM accounts WHERE id = 2 FOR UPDATE;
  
  -- Operations
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  
  -- Error handling
EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END;
```

---

**Chúc mừng hoàn thành Phase 4!** 🎉

**Chuẩn bị cho Phase 5: Production SQL & Interview Patterns** 🚀
