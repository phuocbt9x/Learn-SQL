# Day-089: SQL Injection - Security

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- SQL Injection là gì?
- Cách phòng tránh (parameterized queries)
- Hậu quả nếu không phòng tránh
- Best practices

---

## 1️⃣ SQL INJECTION LÀ GÌ?

**SQL Injection** là **lỗ hổng bảo mật** cho phép attacker inject SQL code:

```sql
-- ❌ NGUY HIỂM: String concatenation
SELECT * FROM users WHERE email = '$email' AND password = '$password';
-- Attacker có thể inject: email = "admin@example.com' OR '1'='1"

-- ✅ AN TOÀN: Parameterized query
SELECT * FROM users WHERE email = ? AND password = ?;
-- Parameters được escape tự động
```

**Đặc điểm:**
- Lỗ hổng bảo mật nghiêm trọng
- Cho phép attacker execute SQL
- Có thể lấy/delete data

---

## 2️⃣ TẠI SAO TỒN TẠI SQL INJECTION?

**SQL Injection tồn tại vì:**
- **String concatenation**: Nối string trực tiếp
- **Không validate input**: Không validate user input
- **Không escape**: Không escape special characters

**Nếu không phòng tránh:**
- Attacker có thể lấy data
- Attacker có thể delete data
- Security breach

---

## 3️⃣ CÁCH PHÒNG TRÁNH

**Best practices:**
1. **Parameterized queries**: Luôn dùng parameters
2. **Input validation**: Validate user input
3. **Least privilege**: Giới hạn quyền database user
4. **Escape special characters**: Nếu phải dùng string

---

## 4️⃣ PRODUCTION STORY: SECURITY BREACH DO SQL INJECTION

**Context:**
Application có SQL injection vulnerability → attacker lấy được data.

**Problem:**
- String concatenation trong queries
- Không validate input
- Attacker inject SQL

**Fix:**
- Refactor thành parameterized queries
- Validate input
- Result: Không còn SQL injection

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **SQL Injection**: Lỗ hổng bảo mật nghiêm trọng
2. **Phòng tránh**: Parameterized queries, input validation
3. **Best practice**: Luôn dùng parameters, validate input

---

**Chuẩn bị cho Day-090: Data Quality - NULL Handling** 🚀
