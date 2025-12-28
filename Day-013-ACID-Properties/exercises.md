# Day-013: Bài Tập - ACID Properties

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về ACID Properties. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: ACID là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- ACID là gì?
- 4 properties là gì?
- Tại sao ACID quan trọng?

---

### Câu 1.2: Atomicity

**Câu hỏi:**

a) Atomicity là gì?

b) Tại sao Atomicity quan trọng?

c) Ví dụ minh họa Atomicity?

---

### Câu 1.3: Consistency, Isolation, Durability

**Câu hỏi:**

a) Consistency là gì? Tại sao quan trọng?

b) Isolation là gì? Tại sao quan trọng?

c) Durability là gì? Tại sao quan trọng?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Thiếu Atomicity

**Tình huống:**

Code hiện tại (SAI):

```python
def transfer_money(from_id, to_id, amount):
    update_account(from_id, -amount)  # Bước 1
    update_account(to_id, +amount)    # Bước 2
    # ❌ Nếu lỗi ở đây → from_id đã trừ tiền nhưng to_id chưa cộng!
```

**Câu hỏi:**

a) Phân tích vấn đề với code này.

b) Hậu quả nếu dùng code này trong production?

c) Viết lại code đúng (dùng transaction).

---

### Câu 2.2: Thiếu Consistency

**Tình huống:**

Transaction vi phạm consistency:

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  -- ❌ Balance tổng thay đổi (1000 - 100 + 100 = 1000, nhưng nếu có lỗi → không nhất quán)
COMMIT;
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Làm thế nào đảm bảo consistency?

---

### Câu 2.3: Thiếu Isolation

**Tình huống:**

2 transactions cùng chạy:

```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  -- Transaction 2 có thể thấy change này (dirty read)

-- Transaction 2
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;
  -- ❌ Có thể thấy balance chưa commit (dirty read)
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Làm thế nào đảm bảo isolation?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Payment System

**Yêu cầu:**

Thiết kế payment system với ACID:

**Operations:**
1. Trừ tiền từ user account
2. Tạo payment record
3. Update order status

**Câu hỏi:**

a) Viết SQL transaction đảm bảo ACID.

b) Giải thích từng ACID property.

---

### Câu 3.2: E-commerce Order

**Yêu cầu:**

Thiết kế order system với ACID:

**Operations:**
1. Tạo order
2. Trừ inventory
3. Tạo payment

**Câu hỏi:**

a) Viết SQL transaction.

b) Giải thích ACID properties.

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: ACID vs NoSQL

**Tình huống:**

Bạn có 2 options:

**Option A: RDBMS với ACID**
- Đảm bảo ACID
- Performance có thể chậm hơn

**Option B: NoSQL không có ACID**
- Performance nhanh hơn
- Không đảm bảo ACID

**Câu hỏi:**

a) So sánh 2 options.

b) Khi nào chọn Option A? Khi nào chọn Option B?

---

### Câu 4.2: ACID Trade-offs

**Câu hỏi:**

a) Trade-offs của ACID?

b) Khi nào có thể relax ACID?

c) Best practices?

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. ACID là gì? 4 properties là gì?

2. Atomicity là gì? Tại sao quan trọng?

3. Consistency là gì? Tại sao quan trọng?

4. Isolation là gì? Tại sao quan trọng?

5. Durability là gì? Tại sao quan trọng?

---

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀
