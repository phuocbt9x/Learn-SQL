# Day-078: Bài Tập - DDL - DROP TABLE, TRUNCATE, DELETE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: DROP vs TRUNCATE vs DELETE

**Câu hỏi:**
- DROP TABLE là gì? Khi nào dùng?
- TRUNCATE là gì? Khi nào dùng?
- DELETE là gì? Khi nào dùng?
- So sánh 3 cách xóa?

---

### Câu 1.2: CASCADE Options

**Câu hỏi:**
- CASCADE là gì?
- Khi nào dùng CASCADE?
- Hậu quả nếu dùng CASCADE sai?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: So sánh Performance

**Yêu cầu:**
1. Tạo table `test_data` với 100,000 rows
2. So sánh thời gian:
   - DELETE FROM test_data
   - TRUNCATE TABLE test_data
   - DROP TABLE test_data (sau đó recreate)

**Đánh giá:**
- Performance của mỗi cách
- Khi nào nên dùng gì?

---

### Câu 2.2: Soft Delete Pattern

**Yêu cầu:**
1. Implement soft delete cho table `users`:
   - Thêm column `deleted_at TIMESTAMP`
   - Tạo function để soft delete
   - Tạo view để filter deleted rows

2. So sánh:
   - Soft delete vs Hard delete
   - Trade-offs

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Xóa Data An toàn

**Yêu cầu:**
Cần xóa 1 triệu rows từ table `logs` (10 triệu rows):
- Chỉ xóa logs cũ hơn 1 năm
- Không được lock table lâu
- Có thể rollback nếu sai

**Yêu cầu:**
- Plan xóa an toàn
- Implement với transactions
- Monitor performance

---

### Câu 3.2: Cleanup Schema

**Yêu cầu:**
Cần cleanup schema:
- Xóa table `old_users` (không còn dùng)
- Xóa test data từ `products` (status = 'test')
- Reset `orders` table về trạng thái ban đầu (test)

**Yêu cầu:**
- Chọn cách xóa phù hợp
- Đảm bảo an toàn
- Có rollback plan

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: CASCADE Behavior

**Yêu cầu:**
Có schema:
- `users` table
- `orders` table (FOREIGN KEY → users)
- `order_items` table (FOREIGN KEY → orders)

**Yêu cầu:**
1. Test DROP TABLE users CASCADE
2. Test TRUNCATE users CASCADE
3. Test DELETE FROM users

**Câu hỏi:**
- Behavior của mỗi cách?
- Khi nào dùng CASCADE?
- Cách xóa an toàn?

---

### Câu 4.2: Recovery Strategy

**Yêu cầu:**
Nếu xóa nhầm data, làm thế nào recover?

**Yêu cầu:**
1. Plan backup strategy
2. Plan recovery procedure
3. Test recovery trên staging

**Câu hỏi:**
- Backup frequency?
- Recovery time objective (RTO)?
- Recovery point objective (RPO)?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

