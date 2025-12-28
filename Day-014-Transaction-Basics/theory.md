# Day-014: Transaction - Cơ bản

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Transaction là gì?
- BEGIN, COMMIT, ROLLBACK
- Tại sao cần transaction?
- Khi nào dùng transaction?
- Hậu quả nếu không dùng transaction

---

## 1️⃣ TRANSACTION LÀ GÌ?

### **Nó là gì?**

**Transaction** (Giao dịch) là một **nhóm các operations** được thực thi như một **đơn vị duy nhất**:
- **Hoặc tất cả thành công** (COMMIT)
- **Hoặc tất cả thất bại** (ROLLBACK)

**Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- Hoặc ROLLBACK nếu lỗi
```

**Đặc điểm:**

1. **Atomic**: Tất cả operations cùng thành công hoặc cùng thất bại
2. **Consistent**: Database luôn ở trạng thái hợp lệ
3. **Isolated**: Transactions độc lập với nhau
4. **Durable**: Data đã commit không bị mất

### **Tại sao tồn tại?**

Transaction tồn tại để:

1. **Đảm bảo data integrity**: Dữ liệu luôn nhất quán
2. **Tránh partial updates**: Không có trạng thái "một phần"
3. **Recovery**: Có thể rollback nếu lỗi
4. **Concurrent access**: Nhiều transactions cùng chạy an toàn

### **Khi nào dùng trong production?**

Transaction **BẮT BUỘC** khi:

✅ **Multi-step operations**: Nhiều bước phải cùng thành công
✅ **Financial transactions**: Chuyển tiền, thanh toán
✅ **Data consistency**: Đảm bảo data consistency
✅ **Critical operations**: Bất kỳ operation nào cần đảm bảo data integrity

---

## 2️⃣ BEGIN, COMMIT, ROLLBACK

### **BEGIN**

**BEGIN** bắt đầu một transaction:

```sql
BEGIN;
  -- Các operations trong transaction
```

**Đặc điểm:**
- Bắt đầu transaction
- Tất cả operations sau BEGIN là một phần của transaction
- Chưa commit → changes chưa permanent

### **COMMIT**

**COMMIT** kết thúc transaction thành công:

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- Tất cả changes được lưu permanent
```

**Đặc điểm:**
- Tất cả changes được lưu permanent
- Transaction kết thúc
- Không thể rollback sau COMMIT

### **ROLLBACK**

**ROLLBACK** hủy transaction:

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  -- Nếu lỗi:
ROLLBACK;  -- Tất cả changes bị hủy
```

**Đặc điểm:**
- Tất cả changes bị hủy
- Database trở về trạng thái trước BEGIN
- Transaction kết thúc

---

## 3️⃣ TẠI SAO CẦN TRANSACTION?

### **Vấn đề không có Transaction**

**Ví dụ không có transaction:**

```python
# ❌ SAI: Không có transaction
def transfer_money(from_id, to_id, amount):
    update_account(from_id, -amount)  # Bước 1
    update_account(to_id, +amount)    # Bước 2
    # Nếu lỗi ở đây → from_id đã trừ tiền nhưng to_id chưa cộng!
```

**Vấn đề:**
- Nếu bước 2 lỗi → bước 1 vẫn commit
- Partial update → data không nhất quán
- Khó recover

### **Giải pháp: Dùng Transaction**

**Ví dụ có transaction:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- Hoặc ROLLBACK nếu lỗi
```

**Lợi ích:**
- Cả 2 bước cùng thành công hoặc cùng rollback
- Data luôn nhất quán
- Có thể recover

---

## 4️⃣ PRODUCTION STORY: DOUBLE-SPENDING BUG DO THIẾU TRANSACTION

### **Context**

E-commerce platform có payment system:

**Code ban đầu (SAI):**

```python
# ❌ SAI: Không có transaction
def process_payment(user_id, order_id, amount):
    # Check balance
    balance = get_balance(user_id)
    if balance < amount:
        return "Insufficient balance"
    
    # Trừ tiền
    update_account(user_id, -amount)
    
    # Tạo payment record
    create_payment_record(order_id, amount)
    
    # ❌ Nếu 2 requests cùng lúc → có thể double-spending!
```

### **Vấn đề xuất hiện**

**Ngày 1: Bug xuất hiện**

- User có $100 balance
- User đặt 2 orders cùng lúc ($80 mỗi order)
- 2 requests cùng chạy:
  - Request 1: Check balance = $100 → OK → Trừ $80 → Balance = $20
  - Request 2: Check balance = $100 → OK → Trừ $80 → Balance = $20
- **Kết quả**: User chỉ trả $80 nhưng có 2 orders ($160)!

**Ngày 2: Nhiều users bị ảnh hưởng**

- 100 users bị double-spending
- Tổng thiệt hại: $10,000
- Customer support bị quá tải

### **Investigation**

**Root cause:**
1. **Không có transaction**: Mỗi bước chạy độc lập
2. **Race condition**: 2 requests cùng check balance → cả 2 đều pass
3. **No locking**: Không có lock để prevent concurrent access

### **Fix**

**Fix: Dùng transaction với locking**

```sql
BEGIN;
  -- Lock row để prevent concurrent access
  SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;
  
  -- Check balance
  -- Trừ tiền
  UPDATE accounts SET balance = balance - 80 WHERE id = 1;
  
  -- Tạo payment record
  INSERT INTO payments (order_id, amount) VALUES (123, 80);
COMMIT;
```

**Hoặc dùng application-level transaction:**

```python
# ✅ ĐÚNG: Dùng transaction
def process_payment(user_id, order_id, amount):
    conn = get_connection()
    try:
        conn.begin()
        
        # Lock row
        cursor.execute("SELECT balance FROM accounts WHERE id = %s FOR UPDATE", (user_id,))
        balance = cursor.fetchone()[0]
        
        if balance < amount:
            conn.rollback()
            return "Insufficient balance"
        
        # Trừ tiền
        cursor.execute("UPDATE accounts SET balance = balance - %s WHERE id = %s", (amount, user_id))
        
        # Tạo payment record
        cursor.execute("INSERT INTO payments (order_id, amount) VALUES (%s, %s)", (order_id, amount))
        
        conn.commit()
    except Exception as e:
        conn.rollback()
        raise e
```

### **Kết quả**

✅ **No double-spending**: Transaction đảm bảo atomicity
✅ **Locking**: FOR UPDATE prevent concurrent access
✅ **Data integrity**: Dữ liệu luôn nhất quán

### **Lesson Learned**

1. **Transaction bắt buộc**: Phải dùng transaction cho multi-step operations
2. **Locking quan trọng**: Dùng FOR UPDATE để prevent race conditions
3. **Test concurrency**: Test các trường hợp concurrent access
4. **Monitor**: Monitor transactions để phát hiện issues sớm

---

## 5️⃣ BEST PRACTICES

### **5.1. Sử dụng Transaction**

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Keep transactions short**: Giữ transactions ngắn
✅ **Handle errors**: Luôn handle errors và rollback
✅ **Test**: Test các trường hợp lỗi

### **5.2. BEGIN, COMMIT, ROLLBACK**

✅ **Always BEGIN**: Luôn BEGIN trước khi thực hiện operations
✅ **COMMIT on success**: COMMIT khi thành công
✅ **ROLLBACK on error**: ROLLBACK khi có lỗi
✅ **Error handling**: Luôn có error handling

### **5.3. Performance**

✅ **Short transactions**: Giữ transactions ngắn
✅ **Avoid long transactions**: Tránh transactions dài
✅ **Lock only when needed**: Chỉ lock khi cần
✅ **Monitor**: Monitor transaction performance

---

## 6️⃣ TÓM TẮT

### **Key Takeaways**

1. **Transaction**: Nhóm các operations được thực thi như một đơn vị duy nhất
2. **BEGIN**: Bắt đầu transaction
3. **COMMIT**: Kết thúc transaction thành công
4. **ROLLBACK**: Hủy transaction
5. **Transaction bắt buộc**: Phải dùng cho multi-step operations

### **Best Practices**

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Keep transactions short**: Giữ transactions ngắn
✅ **Handle errors**: Luôn handle errors và rollback
✅ **Test**: Test các trường hợp lỗi
✅ **Monitor**: Monitor transaction performance

### **Câu hỏi tự kiểm tra**

1. Transaction là gì? Tại sao cần transaction?
2. BEGIN, COMMIT, ROLLBACK là gì?
3. Khi nào dùng transaction?
4. Hậu quả nếu không dùng transaction?
5. Làm thế nào tránh race conditions?

---




**Chuẩn bị cho [Day-015: Transaction-Isolation-Levels](../Day-015-Transaction-Isolation-Levels/theory.md)** 🚀
