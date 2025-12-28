# Day-082: Bài Tập - Stored Procedures - Giới thiệu

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Stored Procedure là gì?

**Câu hỏi:**
- Stored Procedure là gì?
- Tại sao cần Stored Procedure?
- Khi nào dùng Stored Procedure trong production?
- Hậu quả nếu dùng sai?

---

### Câu 1.2: Stored Procedure vs Application Logic

**Câu hỏi:**
- Stored Procedure vs Application Logic?
- Trade-offs?
- Khi nào dùng gì?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Stored Procedure Đơn giản

**Yêu cầu:**
1. Tạo stored procedure `get_user_orders`:
   - Input: user_id
   - Output: List orders của user
   - Return: Cursor hoặc table

2. Tạo stored procedure `update_product_price`:
   - Input: product_id, new_price
   - Update price và log change

---

### Câu 2.2: Stored Procedure với Transaction

**Yêu cầu:**
1. Tạo stored procedure `transfer_balance`:
   - Input: from_user_id, to_user_id, amount
   - Transfer balance giữa 2 users
   - Rollback nếu không đủ balance

2. Tạo stored procedure `create_order_with_items`:
   - Input: user_id, items (JSON)
   - Tạo order và order_items trong transaction

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Performance Optimization

**Yêu cầu:**
Có application code tạo order với 10 round-trips:
- Get user
- Get products (5 queries)
- Create order
- Create order_items (5 queries)
- Update order total

**Yêu cầu:**
1. Refactor thành stored procedure
2. So sánh performance
3. Đánh giá trade-offs

---

### Câu 3.2: Security với Stored Procedure

**Yêu cầu:**
Cần control access đến sensitive operations:
- Chỉ admin có thể update product price
- Chỉ user owner có thể cancel order
- Chỉ system có thể update order status

**Yêu cầu:**
1. Implement với stored procedures
2. Control access với permissions
3. Test security

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Stored Procedure với Error Handling

**Yêu cầu:**
1. Tạo stored procedure với error handling:
   - Try-catch blocks
   - Rollback on error
   - Log errors

2. Test với different error scenarios

**Câu hỏi:**
- Làm thế nào handle errors trong stored procedures?
- Best practices?

---

### Câu 4.2: Stored Procedure vs Function

**Yêu cầu:**
1. Implement cùng logic với:
   - Stored Procedure
   - Function

2. So sánh:
   - Syntax
   - Usage
   - Performance

**Câu hỏi:**
- Khi nào dùng Stored Procedure vs Function?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

