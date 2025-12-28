# Day-029: Solutions - Subquery - EXISTS vs IN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: EXISTS vs IN

**EXISTS:** Kiểm tra có tồn tại rows không.

**IN:** Kiểm tra giá trị có trong list không.

**EXISTS vs IN:** EXISTS thường nhanh hơn với large datasets.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết EXISTS và IN Queries

**a) EXISTS:**
```sql
SELECT * FROM users u
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
```

**b) IN:**
```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

**c) Performance:** EXISTS thường nhanh hơn.

---

**Chúc mừng hoàn thành Day-029!** 🎉
