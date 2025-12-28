# Day-049: Solutions - Query Performance - Join Algorithms

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Join Algorithms

**Nested Loop:** Với mỗi row, scan table khác. Tốt cho small tables.

**Hash Join:** Build hash table, probe. Tốt cho large tables.

**Merge Join:** Merge sorted lists. Tốt cho sorted tables.

**Khi nào dùng:** Database tự chọn, nhưng hiểu để optimize.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Phân tích Join Algorithms

**Ví dụ:**
```sql
EXPLAIN SELECT * FROM users u JOIN orders o ON u.id = o.user_id;
```

**Phân tích:**
- Xác định join algorithm
- Đánh giá performance
- Tối ưu nếu cần (index, etc.)

---

**Chúc mừng hoàn thành Day-049!** 🎉
