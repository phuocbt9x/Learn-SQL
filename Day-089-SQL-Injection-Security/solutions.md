# Day-089: Solutions - SQL Injection - Security

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: SQL Injection là gì?

**SQL Injection:** Lỗ hổng bảo mật cho phép inject SQL code.

**Nguy hiểm:** Attacker có thể lấy/delete data, security breach.

**Phòng tránh:** Parameterized queries, input validation.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Fix SQL Injection

**Solution:**

```sql
-- ❌ NGUY HIỂM
SELECT * FROM users WHERE email = '$email';

-- ✅ AN TOÀN
SELECT * FROM users WHERE email = ?;
-- Dùng parameterized query
```

---

**Chúc mừng hoàn thành Day-089!** 🎉
