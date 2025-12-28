# Day-013: ACID Properties - Nền tảng Transaction

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- ACID là gì? (Atomicity, Consistency, Isolation, Durability)
- Tại sao ACID quan trọng?
- Ví dụ minh họa từng property
- Hậu quả nếu thiếu ACID
- Production scenarios liên quan đến ACID

---

## 1️⃣ ACID LÀ GÌ?

### **Nó là gì?**

**ACID** là 4 properties đảm bảo database transactions đáng tin cậy:

- **A**tomicity (Tính nguyên tử)
- **C**onsistency (Tính nhất quán)
- **I**solation (Tính cô lập)
- **D**urability (Tính bền vững)

**Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**ACID đảm bảo:**
- **Atomicity**: Cả 2 UPDATE hoặc cùng thành công, hoặc cùng thất bại
- **Consistency**: Balance tổng không đổi (100 - 100 + 100 = 100)
- **Isolation**: Transaction khác không thấy changes cho đến khi COMMIT
- **Durability**: Sau khi COMMIT, data không bị mất

### **Tại sao tồn tại?**

ACID tồn tại để:

1. **Đảm bảo data integrity**: Dữ liệu luôn đúng và nhất quán
2. **Tránh data corruption**: Không có partial updates
3. **Concurrent access an toàn**: Nhiều transactions cùng chạy
4. **Recovery**: Có thể recover sau khi crash

### **Khi nào dùng trong production?**

ACID **BẮT BUỘC** trong production khi:

✅ **Financial transactions**: Chuyển tiền, thanh toán
✅ **E-commerce**: Đặt hàng, inventory
✅ **Critical operations**: Bất kỳ operation nào cần đảm bảo data integrity

**Lưu ý:** Không phải tất cả databases đều hỗ trợ ACID đầy đủ (NoSQL thường không có ACID).

---

## 2️⃣ ATOMICITY (TÍNH NGUYÊN TỬ)

### **Nó là gì?**

**Atomicity** (Tính nguyên tử) đảm bảo transaction **hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn** - không có trạng thái "một phần".

**Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  -- Nếu lỗi ở đây → ROLLBACK toàn bộ
COMMIT;
```

**Nếu lỗi:**
- ❌ Không có trường hợp: Account 1 trừ 100 nhưng Account 2 không cộng 100
- ✅ Hoặc cả 2 cùng thành công, hoặc cả 2 cùng rollback

### **Tại sao quan trọng?**

Atomicity quan trọng vì:

1. **Tránh partial updates**: Không có trạng thái "một phần"
2. **Data integrity**: Dữ liệu luôn nhất quán
3. **Recovery**: Có thể rollback nếu lỗi

### **Khi nào dùng trong production?**

Atomicity **BẮT BUỘC** khi:

✅ **Multi-step operations**: Nhiều bước phải cùng thành công
✅ **Financial transactions**: Chuyển tiền, thanh toán
✅ **Data consistency**: Đảm bảo data consistency

---

## 3️⃣ CONSISTENCY (TÍNH NHẤT QUÁN)

### **Nó là gì?**

**Consistency** (Tính nhất quán) đảm bảo database **luôn ở trạng thái hợp lệ** - không vi phạm constraints, rules.

**Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**Consistency đảm bảo:**
- Balance tổng không đổi: 1000 - 100 + 100 = 1000
- Không có negative balance (nếu có constraint)
- Foreign keys vẫn hợp lệ

### **Tại sao quan trọng?**

Consistency quan trọng vì:

1. **Data validity**: Dữ liệu luôn hợp lệ
2. **Business rules**: Đảm bảo business rules được tuân thủ
3. **Constraints**: Đảm bảo constraints không bị vi phạm

### **Khi nào dùng trong production?**

Consistency **BẮT BUỘC** khi:

✅ **Data validation**: Cần đảm bảo data hợp lệ
✅ **Business rules**: Cần tuân thủ business rules
✅ **Constraints**: Cần đảm bảo constraints

---

## 4️⃣ ISOLATION (TÍNH CÔ LẬP)

### **Nó là gì?**

**Isolation** (Tính cô lập) đảm bảo các transactions **độc lập với nhau** - transaction này không thấy changes của transaction khác cho đến khi COMMIT.

**Ví dụ:**

```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  -- Transaction 2 không thấy change này cho đến khi COMMIT
COMMIT;

-- Transaction 2
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;
  -- Vẫn thấy balance cũ (chưa trừ 100)
COMMIT;
```

### **Tại sao quan trọng?**

Isolation quan trọng vì:

1. **Concurrent access**: Nhiều transactions cùng chạy an toàn
2. **Data integrity**: Tránh dirty reads, phantom reads
3. **Consistency**: Đảm bảo consistency khi có concurrent access

### **Khi nào dùng trong production?**

Isolation **BẮT BUỘC** khi:

✅ **Concurrent access**: Nhiều users cùng truy cập
✅ **Data integrity**: Cần đảm bảo data integrity
✅ **Consistency**: Cần đảm bảo consistency

---

## 5️⃣ DURABILITY (TÍNH BỀN VỮNG)

### **Nó là gì?**

**Durability** (Tính bền vững) đảm bảo data **đã commit không bị mất** - ngay cả khi database crash.

**Ví dụ:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;  -- Sau khi COMMIT, data được ghi vào disk
-- Ngay cả khi database crash ngay sau đó, data vẫn còn
```

### **Tại sao quan trọng?**

Durability quan trọng vì:

1. **Data safety**: Dữ liệu không bị mất
2. **Recovery**: Có thể recover sau khi crash
3. **Reliability**: Database đáng tin cậy

### **Khi nào dùng trong production?**

Durability **BẮT BUỘC** khi:

✅ **Critical data**: Dữ liệu quan trọng
✅ **Financial transactions**: Giao dịch tài chính
✅ **Any production**: Bất kỳ production system nào

---

## 6️⃣ PRODUCTION STORY: MẤT TIỀN DO THIẾU ATOMICITY

### **Context**

E-commerce platform có payment system:

**Code ban đầu (SAI):**

```python
# ❌ SAI: Không có transaction
def process_payment(user_id, amount):
    # Bước 1: Trừ tiền từ user account
    update_account(user_id, -amount)
    
    # Bước 2: Tạo payment record
    create_payment_record(user_id, amount)
    
    # ❌ Nếu lỗi ở đây → user đã mất tiền nhưng không có payment record!
```

### **Vấn đề xuất hiện**

**Ngày 1: Bug xuất hiện**

- User thanh toán $100
- Bước 1 thành công: User account trừ $100
- Bước 2 lỗi: Database error → không tạo payment record
- **Kết quả**: User mất $100 nhưng không có payment record!

**Ngày 2: Nhiều users bị ảnh hưởng**

- 50 users bị mất tiền
- Tổng thiệt hại: $5,000
- Customer support bị quá tải

### **Investigation**

**Root cause:**
1. **Không có transaction**: Mỗi bước chạy độc lập
2. **Không có Atomicity**: Nếu một bước lỗi, bước trước đó vẫn commit
3. **No rollback**: Không có cơ chế rollback

### **Fix**

**Fix: Dùng transaction**

```python
# ✅ ĐÚNG: Dùng transaction
def process_payment(user_id, amount):
    conn = get_connection()
    try:
        conn.begin()  # BEGIN transaction
        
        # Bước 1: Trừ tiền từ user account
        update_account(conn, user_id, -amount)
        
        # Bước 2: Tạo payment record
        create_payment_record(conn, user_id, amount)
        
        conn.commit()  # COMMIT - cả 2 bước cùng thành công
    except Exception as e:
        conn.rollback()  # ROLLBACK - cả 2 bước cùng rollback
        raise e
```

**SQL:**

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  INSERT INTO payments (user_id, amount) VALUES (1, 100);
COMMIT;  -- Hoặc ROLLBACK nếu lỗi
```

### **Kết quả**

✅ **Atomicity**: Cả 2 bước cùng thành công hoặc cùng rollback
✅ **No data loss**: Không còn mất tiền
✅ **Data integrity**: Dữ liệu luôn nhất quán

### **Lesson Learned**

1. **ACID bắt buộc**: Phải dùng transaction cho multi-step operations
2. **Atomicity quan trọng**: Đảm bảo tất cả bước cùng thành công hoặc cùng rollback
3. **Test edge cases**: Test các trường hợp lỗi
4. **Monitor**: Monitor transactions để phát hiện issues sớm

---

## 7️⃣ BEST PRACTICES

### **7.1. Sử dụng ACID**

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Test edge cases**: Test các trường hợp lỗi
✅ **Monitor**: Monitor transactions
✅ **Recovery**: Có recovery plan

### **7.2. Atomicity**

✅ **Multi-step operations**: Dùng transaction cho multi-step operations
✅ **Rollback on error**: Rollback khi có lỗi
✅ **Test failures**: Test các trường hợp lỗi

### **7.3. Consistency**

✅ **Constraints**: Đảm bảo constraints được tuân thủ
✅ **Business rules**: Đảm bảo business rules được tuân thủ
✅ **Validation**: Validate data trước khi commit

---

## 8️⃣ TÓM TẮT

### **Key Takeaways**

1. **ACID**: Atomicity, Consistency, Isolation, Durability
2. **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
3. **Consistency**: Database luôn ở trạng thái hợp lệ
4. **Isolation**: Transactions độc lập với nhau
5. **Durability**: Data đã commit không bị mất

### **Best Practices**

✅ **Dùng transaction**: Dùng transaction cho multi-step operations
✅ **Test edge cases**: Test các trường hợp lỗi
✅ **Monitor**: Monitor transactions
✅ **Recovery**: Có recovery plan

### **Câu hỏi tự kiểm tra**

1. ACID là gì? 4 properties là gì?
2. Atomicity là gì? Tại sao quan trọng?
3. Consistency là gì? Tại sao quan trọng?
4. Isolation là gì? Tại sao quan trọng?
5. Durability là gì? Tại sao quan trọng?

---







**Chuẩn bị cho [Day-014: Transaction-Basics](../Day-014-Transaction-Basics/theory.md)** 🚀
