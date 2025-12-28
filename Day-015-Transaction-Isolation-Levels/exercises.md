# Day-015: Bài Tập - Review Phase 1

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn tổng hợp lại tất cả kiến thức từ Day-001 đến Day-014. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: TỔNG HỢP KIẾN THỨC

### Câu 1.1: Database Foundations

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Database là gì? RDBMS là gì?
- Table, Row, Column là gì?
- Primary Key là gì? Foreign Key là gì?
- Normalization (1NF, 2NF, 3NF) là gì?

---

### Câu 1.2: Design & Performance

**Câu hỏi:**

a) Logical design vs Physical design?

b) Data types quan trọng như thế nào?

c) Index là gì? Tại sao cần?

d) SQL Execution Flow?

---

### Câu 1.3: Operations & Reliability

**Câu hỏi:**

a) Connection vs Session?

b) ACID là gì? 4 properties là gì?

c) Transaction là gì? BEGIN, COMMIT, ROLLBACK?

---

## 🔍 BÀI TẬP 2: THIẾT KẾ DATABASE

### Câu 2.1: E-commerce Database Design

**Yêu cầu:** Thiết kế database cho e-commerce system:

**Entities:**
- Users (id, email, name, created_at)
- Products (id, name, price, category_id)
- Categories (id, name)
- Orders (id, user_id, total_amount, created_at)
- Order Items (id, order_id, product_id, quantity)

**Yêu cầu:**

a) Vẽ ERD (logical design - mô tả bằng text).

b) Viết CREATE TABLE statements (physical design).

c) Thêm Primary Keys, Foreign Keys.

d) Thêm indexes cho performance.

e) Đảm bảo normalization (1NF, 2NF, 3NF).

---

### Câu 2.2: Blog System Database Design

**Yêu cầu:** Thiết kế database cho blog system:

**Entities:**
- Users (authors)
- Posts
- Tags
- Comments

**Yêu cầu:**

a) Vẽ ERD.

b) Viết CREATE TABLE statements.

c) Thêm indexes.

d) Đảm bảo normalization.

---

## 🧠 BÀI TẬP 3: TRANSACTION DESIGN

### Câu 3.1: Payment Transaction

**Yêu cầu:** Thiết kế transaction cho payment system:

**Operations:**
1. Check balance (với lock)
2. Trừ tiền từ user account
3. Tạo payment record
4. Update order status

**Yêu cầu:**

a) Viết SQL transaction.

b) Đảm bảo ACID properties.

c) Xử lý các trường hợp lỗi.

d) Tối ưu transaction.

---

### Câu 3.2: Order Transaction

**Yêu cầu:** Thiết kế transaction cho order system:

**Operations:**
1. Tạo order
2. Trừ inventory
3. Tạo payment
4. Send notification (async)

**Yêu cầu:**

a) Viết SQL transaction.

b) Đảm bảo ACID properties.

c) Tối ưu transaction (short transactions).

---

## 🎓 BÀI TẬP 4: PERFORMANCE OPTIMIZATION

### Câu 4.1: Query Optimization

**Query:**
```sql
SELECT u.name, o.total_amount
FROM users u
JOIN orders o ON u.id = o.user_id
WHERE u.email = 'john@example.com'
ORDER BY o.created_at DESC
LIMIT 10;
```

**Yêu cầu:**

a) Phân tích query plan (dự đoán EXPLAIN output).

b) Xác định indexes cần thiết.

c) Tối ưu query.

d) Giải thích performance improvements.

---

### Câu 4.2: Index Design

**Yêu cầu:** Thiết kế indexes cho e-commerce system:

**Queries thường dùng:**
1. `SELECT * FROM users WHERE email = '...'`
2. `SELECT * FROM orders WHERE user_id = ... ORDER BY created_at DESC`
3. `SELECT * FROM products WHERE category_id = ... AND price BETWEEN ... AND ...`

**Yêu cầu:**

a) Xác định indexes cần thiết.

b) Viết CREATE INDEX statements.

c) Giải thích tại sao chọn mỗi index.

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Database là gì? RDBMS là gì?

2. Primary Key là gì? Foreign Key là gì?

3. Normalization (1NF, 2NF, 3NF) là gì?

4. ACID là gì? 4 properties là gì?

5. Transaction là gì? BEGIN, COMMIT, ROLLBACK?

6. Index là gì? Tại sao cần?

7. Logical design vs Physical design?

8. SQL Execution Flow?

9. Connection vs Session?

10. Data types quan trọng như thế nào?

---

### Câu 5.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý dự án**:

**Yêu cầu:**

a) Vẽ ERD (logical design).

b) Viết CREATE TABLE statements (physical design).

c) Thêm indexes.

d) Thiết kế transactions cho critical operations.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Database Migration

**Yêu cầu:** Thiết kế migration strategy:

**Scenario:**
- Database hiện tại: MySQL
- Cần migrate sang PostgreSQL
- Có 10 tables, 1 triệu rows

**Yêu cầu:**

a) Thiết kế migration strategy.

b) Xử lý data type differences.

c) Xử lý indexes.

d) Xử lý downtime.

---

### Câu A.2: Performance Tuning

**Yêu cầu:** Tối ưu database performance:

**Scenario:**
- Database chậm
- Queries mất 5-10 giây
- Có 100 concurrent users

**Yêu cầu:**

a) Phân tích vấn đề.

b) Tối ưu indexes.

c) Tối ưu queries.

d) Tối ưu connections.

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Review lại các Day chưa hiểu rõ
- Thực hành với database thực tế
- Chuẩn bị cho Phase 2: Core SQL Query Language

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

