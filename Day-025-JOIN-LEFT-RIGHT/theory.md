# Day-025: JOIN - LEFT JOIN, RIGHT JOIN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- LEFT JOIN là gì?
- RIGHT JOIN là gì?
- LEFT JOIN vs INNER JOIN
- Khi nào dùng LEFT JOIN?

---

## 1️⃣ LEFT JOIN LÀ GÌ?

**LEFT JOIN** trả về **tất cả rows từ table bên trái**, và **matching rows từ table bên phải**:

```sql
SELECT u.name, o.total_amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

**Kết quả:** Tất cả users (kể cả không có orders).

---

## 2️⃣ RIGHT JOIN LÀ GÌ?

**RIGHT JOIN** trả về **tất cả rows từ table bên phải**, và **matching rows từ table bên trái**:

```sql
SELECT u.name, o.total_amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

**Kết quả:** Tất cả orders (kể cả không có user).

---

## 3️⃣ LEFT JOIN VS INNER JOIN

**INNER JOIN:** Chỉ rows có match.
**LEFT JOIN:** Tất cả rows từ table trái + matching rows.

---

## 4️⃣ KHI NÀO DÙNG LEFT JOIN?

**Dùng LEFT JOIN khi:**
- Cần tất cả rows từ table trái
- Tìm rows không có match (IS NULL)

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. LEFT JOIN: Tất cả rows từ table trái
2. RIGHT JOIN: Tất cả rows từ table phải
3. LEFT JOIN vs INNER JOIN: Khác nhau
4. Best practice: Dùng LEFT JOIN thay vì RIGHT JOIN

---

**Chuẩn bị cho Phase 2.3!** 🚀
