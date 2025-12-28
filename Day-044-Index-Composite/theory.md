# Day-044: Index Types - Composite Index

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Composite index là gì?
- Column order trong composite index
- Left-prefix rule
- Khi nào dùng composite index?

---

## 1️⃣ COMPOSITE INDEX LÀ GÌ?

**Composite index** là index trên **nhiều columns**:

```sql
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
```

**Đặc điểm:**
- Sorted theo thứ tự columns
- Có thể dùng cho queries với columns đầu tiên

---

## 2️⃣ COLUMN ORDER

**Column order quan trọng:**
- Index (A, B) có thể dùng cho queries với A
- Index (A, B) KHÔNG thể dùng cho queries chỉ với B

**Ví dụ:**
```sql
-- Index (user_id, created_at)
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);

-- ✅ Có thể dùng
SELECT * FROM orders WHERE user_id = 123;

-- ❌ KHÔNG thể dùng (chỉ có created_at)
SELECT * FROM orders WHERE created_at > '2024-01-01';
```

---

## 3️⃣ LEFT-PREFIX RULE

**Left-prefix rule:**
- Index (A, B, C) có thể dùng cho:
  - A
  - A, B
  - A, B, C
- KHÔNG thể dùng cho:
  - B
  - C
  - B, C

---

## 4️⃣ PRODUCTION STORY: INDEX KHÔNG DÙNG ĐƯỢC DO COLUMN ORDER SAI

**Context:**
Index (created_at, user_id) → query WHERE user_id = ... không dùng được.

**Fix:**
Đổi order → Index (user_id, created_at) → query dùng được.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Composite index: Index trên nhiều columns
2. Column order: Quan trọng, ảnh hưởng đến usability
3. Left-prefix rule: Chỉ dùng được từ trái sang phải
4. Best practice: Đặt columns thường query nhất trước

---



**Chuẩn bị cho [Day-045: Index-Partial-Covering](Day-045-Index-Partial-Covering/theory.md)** 🚀
