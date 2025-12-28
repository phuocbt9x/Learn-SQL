# Day-072: Solutions - Optimistic vs Pessimistic Locking

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Optimistic vs Pessimistic Locking

**Optimistic locking:** Giả định không conflict, check version.

**Pessimistic locking:** Lock ngay, giả định sẽ conflict.

**Khi nào dùng:** Tùy conflict rate.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Implement Optimistic và Pessimistic Locking

**a) Optimistic:**
```sql
SELECT id, balance, version FROM accounts WHERE id = 1;
UPDATE accounts 
SET balance = balance - 100, version = version + 1 
WHERE id = 1 AND version = 1;
```

**b) Pessimistic:**
```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

---

**Chúc mừng hoàn thành Day-072!** 🎉
