# Day-064: Isolation Levels - REPEATABLE READ

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- REPEATABLE READ là gì?
- Phantom Read là gì?
- Khi nào dùng REPEATABLE READ?
- Trade-offs

---

## 1️⃣ REPEATABLE READ LÀ GÌ?

**REPEATABLE READ** đảm bảo **đọc cùng giá trị** trong transaction:

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

**Đặc điểm:**
- Đọc cùng giá trị trong transaction
- Không có Non-repeatable Read
- Có thể có Phantom Read

---

## 2️⃣ PHANTOM READ

**Phantom Read** là đọc **rows mới** xuất hiện sau lần đọc đầu:

```sql
-- Transaction 1
BEGIN;
  SELECT COUNT(*) FROM orders WHERE user_id = 1;  -- 10 orders
  -- Transaction 2 insert new order
  SELECT COUNT(*) FROM orders WHERE user_id = 1;  -- 11 orders (phantom!)
COMMIT;
```

---

## 3️⃣ KHI NÀO DÙNG REPEATABLE READ?

**Dùng khi:**
- Cần đọc cùng giá trị
- Tránh Non-repeatable Read
- Chấp nhận Phantom Read

**Trade-off:**
- Performance có thể chậm hơn
- Có thể có Phantom Read

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. REPEATABLE READ: Đọc cùng giá trị
2. Phantom Read: Đọc rows mới xuất hiện
3. Khi nào dùng: Cần repeatable reads
4. Trade-off: Có thể có Phantom Read

---



**Chuẩn bị cho [Day-065: Isolation-SERIALIZABLE](Day-065-Isolation-SERIALIZABLE/theory.md)** 🚀
