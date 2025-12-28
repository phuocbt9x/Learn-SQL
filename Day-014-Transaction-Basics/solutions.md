# Day-014: Solutions - Transaction - Cơ bản

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Transaction là gì?

**Transaction là gì?**

**Đáp án:** Transaction (Giao dịch) là một nhóm các operations được thực thi như một đơn vị duy nhất - hoặc tất cả thành công (COMMIT), hoặc tất cả thất bại (ROLLBACK).

**Tại sao cần transaction?**

**Lý do:**
- Đảm bảo data integrity
- Tránh partial updates
- Recovery
- Concurrent access an toàn

**BEGIN, COMMIT, ROLLBACK là gì?**

- **BEGIN**: Bắt đầu transaction
- **COMMIT**: Kết thúc transaction thành công
- **ROLLBACK**: Hủy transaction

---

### Câu 1.2: Transaction Lifecycle

**a) Transaction lifecycle:**

1. **BEGIN**: Bắt đầu transaction
2. **Operations**: Thực hiện các operations
3. **COMMIT/ROLLBACK**: Kết thúc transaction

**b) Khi nào dùng COMMIT? ROLLBACK?**

- **COMMIT**: Khi tất cả operations thành công
- **ROLLBACK**: Khi có lỗi hoặc cần hủy

**c) Hậu quả nếu không COMMIT hoặc ROLLBACK?**

- Transaction vẫn mở → giữ locks
- Resources không được giải phóng
- Có thể block other transactions

---

### Câu 1.3: Transaction và ACID

**a) Transaction liên quan đến ACID:**

Transaction đảm bảo ACID properties:
- **Atomicity**: Tất cả operations cùng thành công hoặc cùng rollback
- **Consistency**: Database luôn ở trạng thái hợp lệ
- **Isolation**: Transactions độc lập với nhau
- **Durability**: Data đã commit không bị mất

**b) Transaction đảm bảo ACID:**

- **BEGIN/COMMIT/ROLLBACK**: Đảm bảo Atomicity
- **Constraints**: Đảm bảo Consistency
- **Isolation levels**: Đảm bảo Isolation
- **Write-ahead logging**: Đảm bảo Durability

**c) Có thể có transaction không có ACID không?**

**Đáp án: CÓ, nhưng không recommended**

Một số NoSQL databases có "transactions" nhưng không đảm bảo ACID đầy đủ.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Thiếu Transaction

**a) Phân tích vấn đề:**

**Vấn đề:**
- Không có transaction
- Nếu bước 2 lỗi → bước 1 vẫn commit
- Partial update → data không nhất quán

**b) Hậu quả:**

- User mất tiền nhưng không có payment record
- Data không nhất quán
- Khó recover

**c) Code đúng:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- Hoặc ROLLBACK nếu lỗi
```

---

### Câu 2.2: Race Condition

**a) Phân tích vấn đề:**

**Race condition:**
- 2 transactions cùng check balance = 100
- Cả 2 đều trừ 50
- Kết quả: Balance = 0 (sai, nên là 0 hoặc -50 tùy thứ tự)

**b) Kết quả cuối cùng:**

Balance = 0 (nếu cả 2 cùng commit) hoặc -50 (nếu có lỗi)

**c) Fix: Dùng FOR UPDATE**

```sql
BEGIN;
  SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;  -- Lock row
  UPDATE accounts SET balance = balance - 50 WHERE id = 1;
COMMIT;
```

---

### Câu 2.3: Long Transaction

**a) Phân tích vấn đề:**

**Vấn đề:**
- Transaction giữ lock quá lâu
- Block other transactions
- Performance issues

**b) Hậu quả:**

- Block other transactions
- Timeout errors
- Poor performance

**c) Fix:**

**Option 1: Short transactions**
```sql
-- Transaction 1: Database operations
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- External operations (không trong transaction)
-- Wait, API calls, etc.

-- Transaction 2: Database operations
BEGIN;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**Option 2: Optimistic locking**
- Dùng version numbers
- Check version trước khi update

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Payment Transaction

**a) SQL transaction:**

```sql
BEGIN;
  -- 1. Check balance (với lock)
  SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;
  
  -- 2. Trừ tiền
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  
  -- 3. Tạo payment record
  INSERT INTO payments (user_id, amount) VALUES (1, 100);
  
  -- 4. Update order status
  UPDATE orders SET status = 'paid' WHERE id = 123;
COMMIT;
```

**b) Xử lý lỗi:**

```sql
BEGIN;
  -- Operations
  -- Nếu lỗi → ROLLBACK
EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END;
```

**c) ACID properties:**

- **Atomicity**: Cả 4 bước cùng thành công hoặc cùng rollback
- **Consistency**: Balance, constraints được đảm bảo
- **Isolation**: Transactions khác không thấy changes
- **Durability**: Data không bị mất sau COMMIT

---

### Câu 3.2: Order Transaction

**a) SQL transaction:**

```sql
BEGIN;
  -- 1. Tạo order
  INSERT INTO orders (user_id, total) VALUES (1, 100);
  
  -- 2. Trừ inventory (với lock)
  UPDATE products SET stock = stock - 1 WHERE id = 5 FOR UPDATE;
  
  -- 3. Tạo payment
  INSERT INTO payments (order_id, amount) VALUES (123, 100);
  
  -- 4. Send notification (không trong transaction - async)
COMMIT;
```

**b) Xử lý lỗi:**

- Check stock trước khi update
- Rollback nếu stock < 0
- Handle payment errors

**c) Tối ưu:**

- Keep transaction short
- External operations (notifications) ngoài transaction
- Use appropriate isolation level

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Transaction vs No Transaction

**a) So sánh:**

| Tiêu chí | Transaction | No Transaction |
|----------|------------|---------------|
| **ACID** | ✅ Đảm bảo | ❌ Không |
| **Performance** | Có thể chậm | Nhanh hơn |
| **Data integrity** | ✅ Đảm bảo | ❌ Không |

**b) Khi nào chọn:**

**Transaction:**
- Multi-step operations
- Financial transactions
- Critical operations

**No Transaction:**
- Single operations
- Non-critical operations
- Performance-critical (cẩn thận!)

**c) Trade-offs:**

- **Performance vs Integrity**: Transaction đảm bảo integrity nhưng có thể chậm hơn
- **Complexity**: Transaction phức tạp hơn

---

### Câu 4.2: Transaction Performance

**a) Ảnh hưởng đến performance:**

- **Locking**: Transactions giữ locks → có thể block
- **Overhead**: BEGIN/COMMIT có overhead
- **Long transactions**: Giữ locks lâu → performance tệ

**b) Optimize:**

- **Short transactions**: Giữ transactions ngắn
- **Appropriate isolation**: Chọn isolation level phù hợp
- **Lock only when needed**: Chỉ lock khi cần
- **Batch operations**: Batch operations khi có thể

**c) Best practices:**

- Keep transactions short
- Avoid long-running operations trong transaction
- Use appropriate isolation level
- Monitor transaction performance

---

### Câu 4.3: Nested Transactions

**a) Nested transactions là gì?**

**Đáp án:** Nested transactions là transactions bên trong transactions.

**b) Databases có hỗ trợ không?**

**Đáp án: MỘT SỐ có, nhưng không phổ biến**

- **SQL Server**: Có SAVE TRANSACTION
- **PostgreSQL**: Không hỗ trợ nested transactions
- **MySQL**: Không hỗ trợ nested transactions

**c) Khi nào cần?**

**Đáp án: HIẾM KHI cần**

Thường có thể refactor để không cần nested transactions.

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Transaction**: Nhóm các operations được thực thi như một đơn vị duy nhất
2. **BEGIN/COMMIT/ROLLBACK**: Bắt đầu, kết thúc, hủy transaction
3. **Khi nào dùng**: Multi-step operations, financial transactions
4. **Hậu quả**: Partial updates, data inconsistency
5. **Tránh race conditions**: Dùng FOR UPDATE, appropriate isolation level

---

### Câu 5.2: Áp dụng thực tế

**a) SQL transaction:**

```sql
BEGIN;
  -- Lock và check balance
  SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;
  
  -- Trừ tiền
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  
  -- Cộng tiền
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**b) Xử lý lỗi:**

- Check balance trước khi transfer
- Rollback nếu insufficient balance
- Handle database errors

**c) Tối ưu:**

- Keep transaction short
- Lock only when needed
- Use appropriate isolation level

---

## 📝 TÓM TẮT

### Key Learnings

1. **Transaction**: Nhóm các operations được thực thi như một đơn vị duy nhất
2. **BEGIN/COMMIT/ROLLBACK**: Bắt đầu, kết thúc, hủy transaction
3. **Transaction bắt buộc**: Phải dùng cho multi-step operations
4. **Race conditions**: Dùng FOR UPDATE để prevent
5. **Short transactions**: Giữ transactions ngắn

### Best Practices

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Keep transactions short**: Giữ transactions ngắn
✅ **Handle errors**: Luôn handle errors và rollback
✅ **Test**: Test các trường hợp lỗi
✅ **Monitor**: Monitor transaction performance

---

**Chúc mừng hoàn thành Day-014!** 🎉

**Chuẩn bị cho Day-015: Review Phase 1 - Tổng hợp Foundations** 🚀

