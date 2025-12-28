# Day-044: Solutions - Index Types - Composite Index

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Composite Index

**Composite index:** Index trên nhiều columns.

**Column order:** Quan trọng, ảnh hưởng đến usability.

**Left-prefix rule:** Chỉ dùng được từ trái sang phải.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Composite Index

**a)**
```sql
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
```

**b)**
```sql
-- ✅ Có thể dùng
EXPLAIN SELECT * FROM orders WHERE user_id = 123;

-- ❌ KHÔNG thể dùng
EXPLAIN SELECT * FROM orders WHERE created_at > '2024-01-01';
```

---

**Chúc mừng hoàn thành Day-044!** 🎉
