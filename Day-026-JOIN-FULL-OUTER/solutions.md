# Day-026: Solutions - JOIN - FULL OUTER JOIN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: FULL OUTER JOIN là gì?

**FULL OUTER JOIN:** Trả về tất cả rows từ cả 2 tables.

**Khi nào dùng:** Data reconciliation, cần tất cả rows.

**FULL OUTER JOIN vs UNION:** Khác nhau về structure.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết FULL OUTER JOIN Queries

**a)**
```sql
SELECT u.name, o.total_amount
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;
```

**b)**
```sql
SELECT u.name, o.total_amount
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id
WHERE u.id IS NULL OR o.id IS NULL;
```

---

**Chúc mừng hoàn thành Day-026!** 🎉
