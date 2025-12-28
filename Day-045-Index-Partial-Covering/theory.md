# Day-045: Index Types - Partial Index

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Partial index là gì?
- Khi nào dùng partial index?
- Lợi ích của partial index
- Trade-offs

---

## 1️⃣ PARTIAL INDEX LÀ GÌ?

**Partial index** là index chỉ trên **subset của rows**:

```sql
CREATE INDEX idx_orders_active ON orders(user_id) 
WHERE status = 'active';
```

**Đặc điểm:**
- Chỉ index rows thỏa điều kiện
- Nhỏ hơn full index
- Nhanh hơn với queries matching condition

---

## 2️⃣ KHI NÀO DÙNG PARTIAL INDEX?

**Dùng khi:**
- Chỉ query subset của rows
- Index size quan trọng
- Most queries có cùng WHERE condition

**Ví dụ:**
- Chỉ query active orders
- Chỉ query non-deleted users
- Chỉ query recent data

---

## 3️⃣ LỢI ÍCH

**Lợi ích:**
- **Smaller size**: Index nhỏ hơn
- **Faster**: Nhanh hơn với matching queries
- **Less maintenance**: Ít overhead

---

## 4️⃣ PRODUCTION STORY: INDEX SIZE GIẢM 80% VỚI PARTIAL INDEX

**Context:**
Full index trên orders → 10GB.

**Fix:**
Partial index chỉ active orders → 2GB (giảm 80%).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Partial index: Chỉ index subset của rows
2. Khi nào dùng: Chỉ query subset, index size quan trọng
3. Lợi ích: Smaller, faster, less maintenance
4. Best practice: Dùng khi có WHERE condition cố định

---

**Chuẩn bị cho Phase 3.2!** 🚀


**Chuẩn bị cho [Day-046: Index-Unique](Day-046-Index-Unique/theory.md)** 🚀
