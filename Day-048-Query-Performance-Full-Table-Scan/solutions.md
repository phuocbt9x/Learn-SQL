# Day-048: Solutions - Query Performance - Full Table Scan

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Full Table Scan

**Khi nào xảy ra:** Không có index, query trả về nhiều rows.

**Khi nào tốt:** Table nhỏ, query trả về > 20-30% rows.

**Khi nào xấu:** Table lớn, query trả về ít rows.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Phân tích Full Table Scan

**Ví dụ:**
```sql
EXPLAIN SELECT * FROM users WHERE status = 'active';
-- Nếu Seq Scan → đánh giá có tốt không
```

**Phân tích:**
- Table size
- Selectivity
- Query frequency

---

**Chúc mừng hoàn thành Day-048!** 🎉
