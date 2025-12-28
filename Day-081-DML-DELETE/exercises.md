# Day-081: Bài Tập - DML - DELETE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: DELETE là gì?

**Câu hỏi:**
- DELETE là gì?
- Tại sao cần DELETE?
- Khi nào dùng DELETE trong production?
- Hậu quả nếu DELETE sai?

---

### Câu 1.2: DELETE Variants

**Câu hỏi:**
- DELETE với WHERE clause?
- DELETE với JOIN?
- Soft delete pattern?
- Khi nào dùng mỗi variant?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: DELETE với WHERE Clause

**Yêu cầu:**
1. Delete single row: Xóa user id = 1
2. Delete với điều kiện: Xóa orders đã quá 1 năm và status = 'cancelled'
3. Delete với subquery: Xóa products có category không còn active

---

### Câu 2.2: DELETE với JOIN

**Yêu cầu:**
1. Xóa orders của users đã bị deleted
2. Xóa order_items của orders đã bị cancelled
3. Xóa products không còn trong bất kỳ order nào

**So sánh:**
- DELETE với JOIN vs Subquery
- Performance considerations?

---

### Câu 2.3: Soft Delete Pattern

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

### Câu 3.1: Xóa Dữ liệu Lớn Không Lock Table

**Yêu cầu:**
Cần xóa 5 triệu rows từ table `logs` (20 triệu rows):
- Chỉ xóa logs cũ hơn 2 năm
- Không được lock table lâu
- Có thể rollback nếu sai

**Yêu cầu:**
1. Plan batch delete strategy
2. Implement với transactions
3. Monitor progress và locks

---

### Câu 3.2: Cleanup Orphan Records

**Yêu cầu:**
Cần cleanup orphan records:
- Xóa orders không có user (user đã bị xóa)
- Xóa order_items không có order
- Xóa products không có category

**Yêu cầu:**
1. Identify orphan records
2. Delete an toàn
3. Verify results

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: DELETE với CTE

**Yêu cầu:**
1. Xóa duplicate users (giữ lại user đầu tiên)
2. Xóa old logs nhưng giữ lại 1000 logs mới nhất mỗi user
3. Xóa products không có sales trong 1 năm

**Câu hỏi:**
- Khi nào dùng CTE trong DELETE?
- Performance vs readability?

---

### Câu 4.2: CASCADE DELETE

**Yêu cầu:**
1. Thiết kế schema với CASCADE DELETE:
   - users → orders → order_items
   - Khi xóa user, xóa orders và order_items

2. So sánh:
   - CASCADE DELETE vs Manual delete
   - Trade-offs

**Câu hỏi:**
- Khi nào nên dùng CASCADE DELETE?
- Khi nào nên manual delete?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

