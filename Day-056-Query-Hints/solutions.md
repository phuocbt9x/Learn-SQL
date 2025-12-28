# Day-056: Solutions - Query Hints

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Query Hints

**Query hints:** Force execution plan.

**Khi nào dùng:** Planner chọn sai, edge cases.

**Trade-offs:** Có thể tốt hoặc xấu, khó maintain.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Sử dụng Query Hints

**MySQL:**
```sql
SELECT * FROM users FORCE INDEX (idx_users_email) WHERE email = 'john@example.com';
```

**Lưu ý:** PostgreSQL không hỗ trợ hints trực tiếp.

---

**Chúc mừng hoàn thành Day-056!** 🎉
