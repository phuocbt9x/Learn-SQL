# Day-014: Bài Tập - Transaction - Cơ bản

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Transaction. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Transaction là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Transaction là gì?
- Tại sao cần transaction?
- BEGIN, COMMIT, ROLLBACK là gì?

---

### Câu 1.2: Transaction Lifecycle

**Câu hỏi:**

a) Transaction lifecycle là gì? (BEGIN → Operations → COMMIT/ROLLBACK)

b) Khi nào dùng COMMIT? Khi nào dùng ROLLBACK?

c) Hậu quả nếu không COMMIT hoặc ROLLBACK?

---

### Câu 1.3: Transaction và ACID

**Câu hỏi:**

a) Transaction liên quan đến ACID như thế nào?

b) Transaction đảm bảo ACID properties như thế nào?

c) Có thể có transaction không có ACID không?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Thiếu Transaction

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

### Câu 2.2: Race Condition

**Tình huống:**

2 transactions cùng chạy:

```sql
-- Transaction 1
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;  -- Balance = 100
  UPDATE accounts SET balance = balance - 50 WHERE id = 1;
COMMIT;

-- Transaction 2 (cùng lúc)
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;  -- Vẫn thấy 100 (chưa commit)
  UPDATE accounts SET balance = balance - 50 WHERE id = 1;
COMMIT;
```

**Câu hỏi:**

a) Phân tích vấn đề (race condition).

b) Kết quả cuối cùng là gì? (Balance = ?)

c) Làm thế nào fix? (dùng FOR UPDATE)

---

### Câu 2.3: Long Transaction

**Tình huống:**

Transaction quá dài:

```sql
BEGIN;
  -- Operation 1
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  
  -- Wait 30 giây (user input, external API call, etc.)
  -- ❌ Transaction giữ lock quá lâu!
  
  -- Operation 2
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**Câu hỏi:**

a) Phân tích vấn đề.

b) Hậu quả của long transaction?

c) Làm thế nào fix?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Payment Transaction

**Yêu cầu:**

Thiết kế payment transaction:

**Operations:**
1. Check balance
2. Trừ tiền từ user account
3. Tạo payment record
4. Update order status

**Câu hỏi:**

a) Viết SQL transaction.

b) Xử lý các trường hợp lỗi.

c) Giải thích ACID properties.

---

### Câu 3.2: Order Transaction

**Yêu cầu:**

Thiết kế order transaction:

**Operations:**
1. Tạo order
2. Trừ inventory
3. Tạo payment
4. Send notification

**Câu hỏi:**

a) Viết SQL transaction.

b) Xử lý các trường hợp lỗi.

c) Tối ưu transaction (short transactions).

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Transaction vs No Transaction

**Tình huống:**

Bạn có 2 options:

**Option A: Dùng Transaction**
- Đảm bảo ACID
- Có thể chậm hơn (locking)

**Option B: Không dùng Transaction**
- Nhanh hơn
- Không đảm bảo ACID

**Câu hỏi:**

a) So sánh 2 options.

b) Khi nào chọn Option A? Khi nào chọn Option B?

c) Trade-offs?

---

### Câu 4.2: Transaction Performance

**Câu hỏi:**

a) Transaction ảnh hưởng đến performance như thế nào?

b) Làm thế nào optimize transaction performance?

c) Best practices?

---

### Câu 4.3: Nested Transactions

**Câu hỏi:**

a) Nested transactions là gì?

b) Databases có hỗ trợ nested transactions không?

c) Khi nào cần nested transactions?

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Transaction là gì? Tại sao cần transaction?

2. BEGIN, COMMIT, ROLLBACK là gì?

3. Khi nào dùng transaction?

4. Hậu quả nếu không dùng transaction?

5. Làm thế nào tránh race conditions?

---

### Câu 5.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế transfer money system:

**Yêu cầu:**

a) Viết SQL transaction.

b) Xử lý các trường hợp lỗi.

c) Tối ưu transaction.

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Transaction rất quan trọng trong production
- Senior SQL Engineer hiểu transaction và biết tránh race conditions

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

