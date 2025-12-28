# Day-066: Solutions - Lock - Row-level Lock

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Row-level Lock

**Lock:** Ngăn chặn concurrent access.

**Row-level lock:** Lock từng row.

**SELECT FOR UPDATE:** Lock rows được select.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Sử dụng Row-level Lock

**a)**
```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

---

**Chúc mừng hoàn thành Day-066!** 🎉
