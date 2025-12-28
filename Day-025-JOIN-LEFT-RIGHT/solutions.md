# Day-025: Solutions - JOIN - LEFT/RIGHT JOIN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: LEFT JOIN là gì?

**LEFT JOIN:** Trả về tất cả rows từ table trái + matching rows.

**RIGHT JOIN:** Trả về tất cả rows từ table phải + matching rows.

**LEFT JOIN vs INNER JOIN:**
- INNER JOIN: Chỉ rows có match
- LEFT JOIN: Tất cả rows từ table trái

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết LEFT JOIN Queries

**a)**
```sql
SELECT u.name, o.total_amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

**b)**
```sql
SELECT u.name
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.id IS NULL;
```

---

**Chúc mừng hoàn thành Day-025!** 🎉

**Chuẩn bị cho Phase 2.3!** 🚀
