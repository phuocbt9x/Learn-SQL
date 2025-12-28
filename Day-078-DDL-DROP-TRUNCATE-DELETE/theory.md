# Day-078: DDL - DROP TABLE, TRUNCATE, DELETE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- DROP TABLE vs TRUNCATE vs DELETE
- CASCADE options
- Khi nào dùng gì?
- Hậu quả nếu dùng sai

---

## 1️⃣ DROP TABLE LÀ GÌ?

**DROP TABLE** là câu lệnh DDL để **xóa bảng hoàn toàn**:

```sql
-- Xóa table
DROP TABLE users;

-- Xóa table với CASCADE (xóa dependencies)
DROP TABLE users CASCADE;
```

**Đặc điểm:**
- Xóa table và tất cả data
- Không thể rollback (DDL)
- Xóa structure và data
- Có thể xóa dependencies với CASCADE

---

## 2️⃣ TRUNCATE LÀ GÌ?

**TRUNCATE** là câu lệnh DDL để **xóa tất cả rows** nhưng giữ structure:

```sql
-- Xóa tất cả rows
TRUNCATE TABLE users;

-- Xóa với CASCADE (xóa rows trong dependent tables)
TRUNCATE TABLE users CASCADE;

-- Xóa và reset auto-increment
TRUNCATE TABLE users RESTART IDENTITY;
```

**Đặc điểm:**
- Xóa tất cả rows nhanh
- Giữ structure (columns, constraints)
- Không thể rollback (DDL)
- Nhanh hơn DELETE (không log từng row)

---

## 3️⃣ DELETE LÀ GÌ?

**DELETE** là câu lệnh DML để **xóa rows theo điều kiện**:

```sql
-- Xóa rows theo điều kiện
DELETE FROM users WHERE id = 1;

-- Xóa tất cả rows
DELETE FROM users;
```

**Đặc điểm:**
- Xóa rows theo WHERE clause
- Có thể rollback (DML trong transaction)
- Log từng row → chậm hơn TRUNCATE
- Trigger được fire

---

## 4️⃣ TẠI SAO TỒN TẠI 3 CÁCH XÓA?

**DROP TABLE tồn tại để:**
- Xóa table không còn cần thiết
- Cleanup schema

**TRUNCATE tồn tại để:**
- Xóa tất cả rows nhanh
- Reset table về trạng thái ban đầu
- Performance tốt hơn DELETE

**DELETE tồn tại để:**
- Xóa rows có điều kiện
- Có thể rollback
- Trigger được fire

---

## 5️⃣ SO SÁNH: DROP vs TRUNCATE vs DELETE

| Aspect | DROP TABLE | TRUNCATE | DELETE |
|--------|------------|----------|--------|
| **Xóa structure** | ✅ Có | ❌ Không | ❌ Không |
| **Xóa data** | ✅ Tất cả | ✅ Tất cả | ✅ Theo WHERE |
| **Rollback** | ❌ Không | ❌ Không | ✅ Có (trong transaction) |
| **Performance** | Nhanh | Rất nhanh | Chậm (log từng row) |
| **Trigger** | ❌ Không | ❌ Không | ✅ Có |
| **Auto-increment** | Reset | Reset (với RESTART) | Không reset |
| **Foreign keys** | Cần CASCADE | Cần CASCADE | Check constraints |

---

## 6️⃣ KHI NÀO DÙNG GÌ?

**DROP TABLE:**
- Xóa table không còn cần thiết
- Cleanup schema
- Migration: Drop table cũ

**TRUNCATE:**
- Xóa tất cả rows nhanh
- Reset table
- Test data cleanup

**DELETE:**
- Xóa rows có điều kiện
- Cần rollback capability
- Cần trigger fire

---

## 7️⃣ CASCADE OPTIONS

**CASCADE** xóa dependencies:

```sql
-- DROP với CASCADE
DROP TABLE users CASCADE;  -- Xóa views, constraints, etc.

-- TRUNCATE với CASCADE
TRUNCATE TABLE users CASCADE;  -- Xóa rows trong dependent tables
```

**Khi nào dùng:**
- Khi muốn xóa cả dependencies
- Cleanup toàn bộ

**Hậu quả:**
- Xóa nhầm dependencies → mất data
- Cẩn thận trong production

---

## 8️⃣ PRODUCTION STORY: XÓA NHẦM DỮ LIỆU PRODUCTION

**Context:**
Developer chạy `DELETE FROM users` thay vì `DELETE FROM users WHERE id = 1` → xóa tất cả users.

**Problem:**
- Xóa nhầm tất cả data
- Không có backup gần đây
- Users không thể login

**Investigation:**
- Logs cho thấy DELETE không có WHERE clause
- Transaction đã commit → không thể rollback

**Root Cause:**
- Thiếu WHERE clause
- Không có backup
- Không có transaction safety

**Fix:**
- Restore từ backup (nếu có)
- Hoặc recover từ transaction logs (nếu có)
- Implement safety measures

**Prevention:**
1. **Always use WHERE clause**: Luôn có WHERE khi DELETE
2. **Backup before DELETE**: Backup trước khi xóa lớn
3. **Use transactions**: DELETE trong transaction, check trước khi commit
4. **Soft delete**: Dùng soft delete pattern
5. **Permissions**: Giới hạn quyền DELETE trong production

**Lesson Learned:**
- Luôn cẩn thận với DELETE
- Luôn có WHERE clause
- Backup trước khi xóa lớn
- Dùng soft delete khi có thể

---

## 9️⃣ SOFT DELETE PATTERN

**Soft delete** là pattern **không xóa thật** mà đánh dấu deleted:

```sql
-- Thêm column deleted_at
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMP;

-- Soft delete
UPDATE users SET deleted_at = CURRENT_TIMESTAMP WHERE id = 1;

-- Query (bỏ qua deleted)
SELECT * FROM users WHERE deleted_at IS NULL;
```

**Lợi ích:**
- Có thể recover
- Audit trail
- Không mất data

**Trade-off:**
- Tốn storage
- Cần filter deleted trong queries

---

## 🔟 TÓM TẮT

**Key Takeaways:**
1. **DROP TABLE**: Xóa table hoàn toàn
2. **TRUNCATE**: Xóa tất cả rows nhanh, giữ structure
3. **DELETE**: Xóa rows có điều kiện, có thể rollback
4. **CASCADE**: Xóa dependencies
5. **Best practice**: Luôn có WHERE clause, backup trước, dùng soft delete khi có thể

---




**Chuẩn bị cho [Day-079: DML-INSERT](../Day-079-DML-INSERT/theory.md)** 🚀
