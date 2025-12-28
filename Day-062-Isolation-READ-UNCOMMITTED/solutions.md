# Day-062: Solutions - Isolation Levels - READ UNCOMMITTED

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: READ UNCOMMITTED

**Isolation Level:** Mức độ cô lập giữa transactions.

**READ UNCOMMITTED:** Đọc uncommitted data.

**Dirty Read:** Đọc data chưa commit.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Test Dirty Read

**Test:**
```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;

-- Transaction 2 (READ UNCOMMITTED)
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;
  -- Có thể đọc được uncommitted data
```

---

**Chúc mừng hoàn thành Day-062!** 🎉
