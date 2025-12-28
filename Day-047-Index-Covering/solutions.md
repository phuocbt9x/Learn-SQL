# Day-047: Solutions - Index - Covering Index

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Covering Index

**Covering index:** Index chứa tất cả columns cần thiết.

**Index Only Scan:** Chỉ đọc index, nhanh hơn Index Scan.

**Trade-off:** Larger index vs Faster queries.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Covering Index

**a)**
```sql
CREATE INDEX idx_orders_covering ON orders(user_id, created_at, total_amount);
```

**b)**
```sql
EXPLAIN SELECT user_id, created_at, total_amount 
FROM orders 
WHERE user_id = 123;
-- Kiểm tra Index Only Scan
```

---

**Chúc mừng hoàn thành Day-047!** 🎉
