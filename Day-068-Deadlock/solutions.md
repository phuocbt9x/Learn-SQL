# Day-068: Solutions - Deadlock

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Deadlock

**Deadlock:** Transactions chờ nhau.

**Nguyên nhân:** Lock order khác nhau.

**Cách tránh:** Lock order nhất quán, short transactions.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo và Fix Deadlock

**a) Deadlock scenario:**
```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- Transaction 2
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 2;
  UPDATE accounts SET balance = balance + 100 WHERE id = 1;
-- → Deadlock
```

**b) Fix:**
```sql
-- Luôn lock theo thứ tự id tăng dần
-- Transaction 1 & 2: Lock id = 1 trước, sau đó id = 2
```

---

**Chúc mừng hoàn thành Day-068!** 🎉
