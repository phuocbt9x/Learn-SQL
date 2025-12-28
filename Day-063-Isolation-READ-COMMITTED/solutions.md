# Day-063: Solutions - Isolation Levels - READ COMMITTED

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: READ COMMITTED

**READ COMMITTED:** Chỉ đọc committed data.

**Non-repeatable Read:** Đọc giá trị khác nhau trong cùng transaction.

**Khi nào dùng:** Mặc định, performance tốt.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Test Non-repeatable Read

**Test:**
```sql
-- Transaction 1
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;  -- 1000
  -- Transaction 2 commit update
  SELECT balance FROM accounts WHERE id = 1;  -- 900 (khác!)
COMMIT;
```

---

**Chúc mừng hoàn thành Day-063!** 🎉
