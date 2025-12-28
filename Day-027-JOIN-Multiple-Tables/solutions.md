# Day-027: Solutions - JOIN - Multiple Tables

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: JOIN Multiple Tables

**JOIN 3+ tables:** JOIN từng table một với ON conditions.

**JOIN order:** Database tự optimize, nhưng có thể ảnh hưởng.

**JOIN conditions:** Equality (thường dùng) vs inequality.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Multiple JOIN Queries

**a)**
```sql
SELECT u.name, o.total_amount, p.name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id;
```

**b)**
```sql
SELECT u.name, o.total_amount, p.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN payments p ON o.id = p.order_id;
```

---

**Chúc mừng hoàn thành Day-027!** 🎉
