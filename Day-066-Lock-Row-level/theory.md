# Day-066: Lock - Row-level Lock

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Lock là gì?
- Row-level lock là gì?
- SELECT FOR UPDATE
- Khi nào dùng row-level lock?

---

## 1️⃣ LOCK LÀ GÌ?

**Lock** là cơ chế **ngăn chặn concurrent access**:

- **Shared Lock**: Nhiều transactions có thể đọc
- **Exclusive Lock**: Chỉ một transaction có thể write

---

## 2️⃣ ROW-LEVEL LOCK LÀ GÌ?

**Row-level lock** lock **từng row**:

```sql
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
```

**Đặc điểm:**
- Lock chỉ row cụ thể
- Các rows khác không bị ảnh hưởng
- Granular hơn table lock

---

## 3️⃣ SELECT FOR UPDATE

**SELECT FOR UPDATE** lock rows được select:

```sql
BEGIN;
  SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
  -- Row bị lock, transactions khác không thể update
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

---

## 4️⃣ PRODUCTION STORY: RACE CONDITION DO THIẾU LOCK

**Context:**
Không có lock → 2 transactions cùng update → race condition.

**Fix:**
Dùng SELECT FOR UPDATE → prevent race condition.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Lock: Ngăn chặn concurrent access
2. Row-level lock: Lock từng row
3. SELECT FOR UPDATE: Lock rows được select
4. Best practice: Dùng để prevent race conditions

---






**Chuẩn bị cho [Day-067: Lock-Table-level](../Day-067-Lock-Table-level/theory.md)** 🚀
