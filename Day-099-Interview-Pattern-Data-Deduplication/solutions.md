# Day-099: Solutions - Interview Pattern - Data Deduplication

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Data Deduplication

**Data deduplication:** Tìm và xóa duplicates.

**Cách tìm:** GROUP BY và HAVING COUNT(*) > 1.

**Cách xóa:** DELETE với subquery, giữ lại MIN(id).

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Cleanup Duplicates

**Solution:**

```sql
-- Tìm duplicates
SELECT email, COUNT(*) AS count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Xóa duplicates (giữ lại 1)
DELETE FROM users
WHERE id NOT IN (
  SELECT MIN(id) FROM users GROUP BY email
);
```

---

**Chúc mừng hoàn thành Day-099!** 🎉
