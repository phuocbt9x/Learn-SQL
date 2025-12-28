# Day-062: Isolation Levels - READ UNCOMMITTED

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Isolation Level là gì?
- READ UNCOMMITTED là gì?
- Dirty Read là gì?
- Khi nào dùng READ UNCOMMITTED?

---

## 1️⃣ ISOLATION LEVEL LÀ GÌ?

**Isolation Level** xác định **mức độ cô lập** giữa các transactions:

- **READ UNCOMMITTED**: Thấp nhất
- **READ COMMITTED**: Mặc định (PostgreSQL)
- **REPEATABLE READ**: Cao hơn
- **SERIALIZABLE**: Cao nhất

---

## 2️⃣ READ UNCOMMITTED LÀ GÌ?

**READ UNCOMMITTED** cho phép đọc **uncommitted data**:

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

**Đặc điểm:**
- Đọc được data chưa commit
- Không có locks khi đọc
- Nhanh nhất nhưng không an toàn

---

## 3️⃣ DIRTY READ LÀ GÌ?

**Dirty Read** là đọc **uncommitted data**:

```sql
-- Transaction 1
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  -- Chưa commit

-- Transaction 2 (READ UNCOMMITTED)
BEGIN;
  SELECT balance FROM accounts WHERE id = 1;
  -- Đọc được balance mới (chưa commit)
  -- Nếu Transaction 1 rollback → data sai!
```

---

## 4️⃣ KHI NÀO DÙNG READ UNCOMMITTED?

**Dùng khi:**
- Không cần data chính xác (analytics, reports)
- Performance quan trọng hơn accuracy

**KHÔNG nên dùng khi:**
- Financial transactions
- Critical data
- Production systems (thường)

---

## 5️⃣ PRODUCTION STORY: ĐỌC DỮ LIỆU CHƯA COMMIT → BUG

**Context:**
READ UNCOMMITTED → đọc uncommitted data → hiển thị data sai.

**Fix:**
Đổi sang READ COMMITTED → không đọc uncommitted data.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. READ UNCOMMITTED: Đọc uncommitted data
2. Dirty Read: Đọc data chưa commit
3. Khi nào dùng: Analytics, không cần accuracy
4. Best practice: KHÔNG dùng trong production (thường)

---






**Chuẩn bị cho [Day-063: Isolation-READ-COMMITTED](../Day-063-Isolation-READ-COMMITTED/theory.md)** 🚀
