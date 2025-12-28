# Day-019: Solutions - LIMIT & OFFSET

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: LIMIT & OFFSET

**LIMIT:** Giới hạn số rows.

**OFFSET:** Bỏ qua N rows.

**Pagination:** LIMIT + OFFSET.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Pagination Queries

**Page 1:**
```sql
SELECT * FROM users LIMIT 10 OFFSET 0;
```

**Page 2:**
```sql
SELECT * FROM users LIMIT 10 OFFSET 10;
```

**Page 3:**
```sql
SELECT * FROM users LIMIT 10 OFFSET 20;
```

---

**Chúc mừng hoàn thành Day-019!** 🎉
