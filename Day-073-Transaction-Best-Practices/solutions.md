# Day-073: Solutions - Transaction Best Practices

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Transaction Best Practices

**Keep transactions short:** Giữ locks ngắn, giảm contention.

**Avoid locks:** Chỉ lock khi cần, lock ngắn nhất.

**Error handling:** Luôn có error handling, rollback khi lỗi.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Apply Best Practices

**a) Long transaction:**
```sql
BEGIN;
  -- Operations
  -- Wait 10 phút
COMMIT;
```

**b) Short transaction:**
```sql
BEGIN;
  -- Operations (ngắn)
COMMIT;
```

---

**Chúc mừng hoàn thành Day-073!** 🎉
