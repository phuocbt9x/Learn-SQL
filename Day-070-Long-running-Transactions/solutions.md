# Day-070: Solutions - Long-running Transactions

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Long-running Transactions

**Vấn đề:** Giữ locks lâu, block others, tốn resources.

**Lock duration:** Locks giữ trong suốt transaction.

**Cách tránh:** Short transactions, no external calls.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize Long Transactions

**a) Long transaction:**
```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  -- Wait 10 phút
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

**b) Short transactions:**
```sql
-- Transaction 1: Get data
SELECT * FROM accounts WHERE id = 1;

-- External operations (không trong transaction)

-- Transaction 2: Update
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

---

**Chúc mừng hoàn thành Day-070!** 🎉

**Chuẩn bị cho Phase 4.3!** 🚀
