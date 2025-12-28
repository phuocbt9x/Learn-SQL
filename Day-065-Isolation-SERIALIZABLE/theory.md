# Day-065: Isolation Levels - SERIALIZABLE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- SERIALIZABLE là gì?
- Khi nào dùng SERIALIZABLE?
- Trade-offs
- Performance impact

---

## 1️⃣ SERIALIZABLE LÀ GÌ?

**SERIALIZABLE** đảm bảo **transactions chạy như serial**:

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

**Đặc điểm:**
- Không có Dirty Read
- Không có Non-repeatable Read
- Không có Phantom Read
- Highest isolation

---

## 2️⃣ KHI NÀO DÙNG SERIALIZABLE?

**Dùng khi:**
- Cần highest isolation
- Critical operations
- Financial transactions
- Không chấp nhận bất kỳ read phenomena nào

**Trade-off:**
- Performance có thể chậm nhất
- Có thể có serialization errors

---

## 3️⃣ PERFORMANCE IMPACT

**Performance:**
- Có thể chậm nhất
- Nhiều locks
- Có thể có serialization errors

**Best practice:**
- Chỉ dùng khi thực sự cần
- Test performance impact

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. SERIALIZABLE: Highest isolation
2. Khi nào dùng: Critical operations
3. Trade-off: Performance có thể chậm
4. Best practice: Chỉ dùng khi thực sự cần

---

**Chuẩn bị cho Phase 4.2!** 🚀
