# Day-085: Views - Virtual Tables

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- View là gì?
- View vs Table
- Updatable views
- Security với views

---

## 1️⃣ VIEW LÀ GÌ?

**View** là **virtual table** dựa trên query:

```sql
-- Tạo view
CREATE VIEW active_users AS
SELECT id, email, name, created_at
FROM users
WHERE deleted_at IS NULL;

-- Query view như table
SELECT * FROM active_users;
```

**Đặc điểm:**
- Virtual table (không lưu data)
- Dựa trên query
- Có thể query như table
- Có thể updatable (nếu đơn giản)

---

## 2️⃣ VIEW VS TABLE

**View:**
- Virtual (không lưu data)
- Dựa trên query
- Có thể có logic

**Table:**
- Physical (lưu data)
- Cấu trúc cố định
- Không có logic

**Khi nào dùng:**
- **View**: Simplify queries, security, abstraction
- **Table**: Store actual data

---

## 3️⃣ PRODUCTION STORY: SECURITY VỚI VIEWS

**Context:**
Cần giới hạn access → users chỉ thấy data của mình.

**Fix:**
Tạo view với WHERE clause → users chỉ query view → chỉ thấy data của mình.

**Result:**
- Security tốt hơn
- Đơn giản hóa queries
- Dễ maintain

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **View**: Virtual table dựa trên query
2. **vs Table**: View không lưu data, Table lưu data
3. **Updatable views**: Có thể update nếu đơn giản
4. **Security**: Views giúp control access

---

**Chuẩn bị cho Phase 5.3!** 🚀





**Chuẩn bị cho [Day-086: Backup-Restore-Concept](../Day-086-Backup-Restore-Concept/theory.md)** 🚀
