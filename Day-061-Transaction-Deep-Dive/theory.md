# Day-061: Transaction - Deep Dive

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Transaction lifecycle
- Savepoints
- Nested transactions (nếu hỗ trợ)
- Advanced transaction patterns

---

## 1️⃣ TRANSACTION LIFECYCLE

**Transaction lifecycle:**
1. BEGIN: Bắt đầu transaction
2. Operations: Thực hiện operations
3. COMMIT/ROLLBACK: Kết thúc transaction

**States:**
- Active: Đang thực thi
- Committed: Đã commit
- Rolled back: Đã rollback
- Aborted: Bị abort

---

## 2️⃣ SAVEPOINTS

**Savepoints** cho phép rollback đến điểm cụ thể:

```sql
BEGIN;
  INSERT INTO users (email) VALUES ('user1@example.com');
  SAVEPOINT sp1;
  INSERT INTO users (email) VALUES ('user2@example.com');
  ROLLBACK TO sp1;  -- Rollback về sp1
  INSERT INTO users (email) VALUES ('user3@example.com');
COMMIT;
```

**Kết quả:** user1 và user3 được insert, user2 không được insert.

---

## 3️⃣ NESTED TRANSACTIONS

**Nested transactions:**
- Transactions bên trong transactions
- Không phải database nào cũng hỗ trợ
- SQL Server: Có SAVE TRANSACTION
- PostgreSQL/MySQL: Không hỗ trợ

---

## 4️⃣ PRODUCTION STORY: TRANSACTION ROLLBACK DO LỖI BUSINESS LOGIC

**Context:**
Transaction phức tạp, lỗi ở bước cuối → rollback toàn bộ.

**Fix:**
Dùng savepoints → rollback đến điểm cụ thể → giữ được một phần work.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Transaction lifecycle: BEGIN → Operations → COMMIT/ROLLBACK
2. Savepoints: Rollback đến điểm cụ thể
3. Nested transactions: Không phổ biến
4. Best practice: Dùng savepoints cho complex transactions

---

**Chuẩn bị cho Day-062: Isolation Levels - READ UNCOMMITTED** 🚀
