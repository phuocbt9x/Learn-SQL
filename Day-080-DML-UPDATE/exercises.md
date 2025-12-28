# Day-080: Bài Tập - DML - UPDATE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: UPDATE là gì?

**Câu hỏi:**
- UPDATE là gì?
- Tại sao cần UPDATE?
- Khi nào dùng UPDATE trong production?
- Hậu quả nếu UPDATE sai?

---

### Câu 1.2: UPDATE Variants

**Câu hỏi:**
- UPDATE với WHERE clause?
- UPDATE với JOIN?
- UPDATE ... RETURNING?
- Khi nào dùng mỗi variant?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: UPDATE với WHERE Clause

**Yêu cầu:**
1. Update single row: Update name của user id = 1
2. Update với điều kiện: Update status của orders đã quá 30 ngày
3. Update với subquery: Update price của products có category không còn active

---

### Câu 2.2: UPDATE với JOIN

**Yêu cầu:**
1. Update orders với total từ products price
2. Update users với last_order_date từ orders
3. Update products với category_name từ categories

**So sánh:**
- UPDATE với JOIN vs Subquery
- Performance considerations?

---

### Câu 2.3: UPDATE ... RETURNING

**Yêu cầu:**
1. Update user và return updated row
2. Update products và return chỉ ids
3. Update orders và return count

**So sánh:**
- UPDATE + SELECT vs UPDATE ... RETURNING
- Performance difference?

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Update 1 Triệu Rows An toàn

**Yêu cầu:**
Cần update status của 1 triệu orders từ 'pending' → 'processed':
- Không được lock table lâu
- Users vẫn có thể access
- Có thể rollback nếu sai

**Yêu cầu:**
1. Plan batch update strategy
2. Implement với transactions
3. Monitor progress và locks

---

### Câu 3.2: Conditional Update

**Yêu cầu:**
Cần update price của products:
- Tăng 10% nếu price < 100
- Tăng 5% nếu price >= 100
- Không update nếu price > 1000

**Yêu cầu:**
1. Implement với CASE expression
2. Test với different scenarios
3. Verify results

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Update với CTE

**Yêu cầu:**
1. Update products với price từ pricing table (chỉ update nếu price khác)
2. Update users với last_login từ login_logs (chỉ update nếu mới hơn)
3. Update orders với calculated_total từ order_items

**Câu hỏi:**
- Khi nào dùng CTE trong UPDATE?
- Performance vs readability?

---

### Câu 4.2: Update với Lock

**Yêu cầu:**
1. Update order với SELECT FOR UPDATE (prevent race condition)
2. Update user balance với lock (prevent concurrent updates)
3. Update product stock với lock (prevent overselling)

**Câu hỏi:**
- Khi nào cần lock trong UPDATE?
- Trade-offs với performance?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

