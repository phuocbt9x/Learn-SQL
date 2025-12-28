# Day-028: Solutions - Subquery - Scalar Subquery

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Subquery là gì?

**Subquery:** Query bên trong query khác.

**Scalar subquery:** Trả về một giá trị.

**Subquery vs JOIN:** JOIN thường nhanh hơn.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Subquery Queries

**a)**
```sql
SELECT name, 
       (SELECT COUNT(*) FROM orders WHERE user_id = users.id) as order_count
FROM users;
```

**b)**
```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

---

**Chúc mừng hoàn thành Day-028!** 🎉
