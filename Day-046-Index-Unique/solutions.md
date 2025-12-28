# Day-046: Solutions - Index Types - Unique Index

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Unique Index

**Unique index:** Đảm bảo không có duplicate values.

**Unique index vs Primary Key:** PK chỉ có một, unique index có thể có nhiều.

**Khi nào dùng:** Cần uniqueness, không phải PK.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Unique Index

**a)**
```sql
CREATE UNIQUE INDEX idx_users_email ON users(email);
```

**b)**
```sql
-- Test duplicate prevention
INSERT INTO users (email) VALUES ('test@example.com');
INSERT INTO users (email) VALUES ('test@example.com'); -- Error: duplicate
```

---

**Chúc mừng hoàn thành Day-046!** 🎉
