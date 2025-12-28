# Day-043: Solutions - Index Types - B-Tree Index

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: B-Tree Index

**B-Tree index:** Cấu trúc cây, sorted, O(log n) lookup.

**Index Scan vs Index Only Scan:** Index Only Scan nhanh hơn (không đọc table).

**Khi nào dùng:** WHERE, JOIN, ORDER BY columns.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo và sử dụng B-Tree Index

**a)**
```sql
CREATE INDEX idx_users_email ON users(email);
```

**b)**
```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
-- Kiểm tra Index Scan
```

---

**Chúc mừng hoàn thành Day-043!** 🎉
