# Day-061: Solutions - Transaction - Deep Dive

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Transaction Deep Dive

**Transaction lifecycle:** BEGIN → Operations → COMMIT/ROLLBACK.

**Savepoints:** Rollback đến điểm cụ thể.

**Nested transactions:** Không phổ biến, một số databases hỗ trợ.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Sử dụng Savepoints

**a)**
```sql
BEGIN;
  INSERT INTO users (email) VALUES ('user1@example.com');
  SAVEPOINT sp1;
  INSERT INTO users (email) VALUES ('user2@example.com');
  ROLLBACK TO sp1;
  INSERT INTO users (email) VALUES ('user3@example.com');
COMMIT;
```

---

**Chúc mừng hoàn thành Day-061!** 🎉
