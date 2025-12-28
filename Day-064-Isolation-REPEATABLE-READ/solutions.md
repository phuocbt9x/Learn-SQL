# Day-064: Solutions - Isolation Levels - REPEATABLE READ

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: REPEATABLE READ

**REPEATABLE READ:** Đọc cùng giá trị trong transaction.

**Phantom Read:** Đọc rows mới xuất hiện.

**Khi nào dùng:** Cần repeatable reads.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Test Phantom Read

**Test:**
```sql
-- Transaction 1
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN;
  SELECT COUNT(*) FROM orders WHERE user_id = 1;  -- 10
  -- Transaction 2 insert
  SELECT COUNT(*) FROM orders WHERE user_id = 1;  -- 10 (same)
  -- Nhưng có thể có phantom rows
COMMIT;
```

---

**Chúc mừng hoàn thành Day-064!** 🎉
