# Day-013: Solutions - ACID Properties

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: ACID là gì?

**ACID là gì?**

**Đáp án:** ACID là 4 properties đảm bảo database transactions đáng tin cậy:
- **A**tomicity (Tính nguyên tử)
- **C**onsistency (Tính nhất quán)
- **I**solation (Tính cô lập)
- **D**urability (Tính bền vững)

**4 properties là gì?**

1. **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
2. **Consistency**: Database luôn ở trạng thái hợp lệ
3. **Isolation**: Transactions độc lập với nhau
4. **Durability**: Data đã commit không bị mất

**Tại sao quan trọng?**

**Lý do:**
- Đảm bảo data integrity
- Tránh data corruption
- Concurrent access an toàn
- Recovery sau khi crash

---

### Câu 1.2: Atomicity

**a) Atomicity là gì?**

**Đáp án:** Atomicity đảm bảo transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn - không có trạng thái "một phần".

**b) Tại sao quan trọng?**

**Lý do:**
- Tránh partial updates
- Data integrity
- Recovery

**c) Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  -- Nếu lỗi → ROLLBACK toàn bộ
COMMIT;
```

---

### Câu 1.3: Consistency, Isolation, Durability

**a) Consistency:**

**Đáp án:** Consistency đảm bảo database luôn ở trạng thái hợp lệ - không vi phạm constraints, rules.

**Tại sao quan trọng:** Đảm bảo data validity, business rules, constraints.

**b) Isolation:**

**Đáp án:** Isolation đảm bảo các transactions độc lập với nhau - transaction này không thấy changes của transaction khác cho đến khi COMMIT.

**Tại sao quan trọng:** Concurrent access an toàn, data integrity, consistency.

**c) Durability:**

**Đáp án:** Durability đảm bảo data đã commit không bị mất - ngay cả khi database crash.

**Tại sao quan trọng:** Data safety, recovery, reliability.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Thiếu Atomicity

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

```python
def transfer_money(from_id, to_id, amount):
    conn = get_connection()
    try:
        conn.begin()
        update_account(conn, from_id, -amount)
        update_account(conn, to_id, +amount)
        conn.commit()
    except Exception as e:
        conn.rollback()
        raise e
```

---

### Câu 2.2: Thiếu Consistency

**a) Phân tích:**

**Vấn đề:**
- Balance tổng có thể thay đổi
- Không đảm bảo constraints

**b) Đảm bảo consistency:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  -- Check constraints, business rules
COMMIT;
```

---

### Câu 2.3: Thiếu Isolation

**a) Phân tích:**

**Vấn đề:**
- Dirty read: Transaction 2 có thể thấy uncommitted changes
- Data inconsistency

**b) Đảm bảo isolation:**

```sql
-- Set isolation level
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  -- Transaction 2 không thấy change này
COMMIT;
```

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Payment System

**a) SQL transaction:**

```sql
BEGIN;
  -- 1. Trừ tiền từ user account
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  
  -- 2. Tạo payment record
  INSERT INTO payments (user_id, amount) VALUES (1, 100);
  
  -- 3. Update order status
  UPDATE orders SET status = 'paid' WHERE id = 123;
COMMIT;
```

**b) ACID properties:**

- **Atomicity**: Cả 3 bước cùng thành công hoặc cùng rollback
- **Consistency**: Balance, constraints được đảm bảo
- **Isolation**: Transactions khác không thấy changes
- **Durability**: Data không bị mất sau COMMIT

---

### Câu 3.2: E-commerce Order

**a) SQL transaction:**

```sql
BEGIN;
  -- 1. Tạo order
  INSERT INTO orders (user_id, total) VALUES (1, 100);
  
  -- 2. Trừ inventory
  UPDATE products SET stock = stock - 1 WHERE id = 5;
  
  -- 3. Tạo payment
  INSERT INTO payments (order_id, amount) VALUES (123, 100);
COMMIT;
```

**b) ACID properties:**

- **Atomicity**: Cả 3 bước cùng thành công hoặc cùng rollback
- **Consistency**: Stock, constraints được đảm bảo
- **Isolation**: Transactions khác không thấy changes
- **Durability**: Data không bị mất sau COMMIT

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: ACID vs NoSQL

**a) So sánh:**

| Tiêu chí | RDBMS (ACID) | NoSQL |
|----------|--------------|-------|
| **ACID** | ✅ Đầy đủ | ❌ Không có |
| **Performance** | Có thể chậm | Nhanh hơn |
| **Consistency** | Strong | Eventual |

**b) Khi nào chọn:**

**RDBMS (ACID):**
- Financial transactions
- E-commerce orders
- Critical operations

**NoSQL:**
- High-scale systems
- Eventual consistency OK
- Schema flexibility needed

---

### Câu 4.2: ACID Trade-offs

**a) Trade-offs:**

- **Performance**: ACID có thể chậm hơn
- **Scalability**: Khó scale ngang
- **Complexity**: Phức tạp hơn

**b) Khi nào relax:**

- Analytics (có thể eventual consistency)
- Caching (có thể mất data)
- Logs (không critical)

**c) Best practices:**

- Dùng ACID cho critical operations
- Relax ACID cho non-critical
- Monitor và test

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **ACID**: Atomicity, Consistency, Isolation, Durability
2. **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
3. **Consistency**: Database luôn ở trạng thái hợp lệ
4. **Isolation**: Transactions độc lập với nhau
5. **Durability**: Data đã commit không bị mất

---

## 📝 TÓM TẮT

### Key Learnings

1. **ACID**: Atomicity, Consistency, Isolation, Durability
2. **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
3. **Consistency**: Database luôn ở trạng thái hợp lệ
4. **Isolation**: Transactions độc lập với nhau
5. **Durability**: Data đã commit không bị mất

### Best Practices

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Test edge cases**: Test các trường hợp lỗi
✅ **Monitor**: Monitor transactions
✅ **Recovery**: Có recovery plan

---

**Chúc mừng hoàn thành Day-013!** 🎉

**Chuẩn bị cho Day-014: Transaction - Cơ bản** 🚀
