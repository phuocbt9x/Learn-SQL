# Day-099: Interview Pattern - Data Deduplication

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Tìm và xóa duplicates
- Keep one, delete others
- Khi nào cần deduplication?
- Production scenarios

---

## 1️⃣ DATA DEDUPLICATION LÀ GÌ?

**Data deduplication** là **tìm và xóa duplicates**:

```sql
-- Tìm duplicates
SELECT email, COUNT(*) AS count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Xóa duplicates (giữ lại 1)
DELETE FROM users
WHERE id NOT IN (
  SELECT MIN(id) FROM users GROUP BY email
);
```

**Đặc điểm:**
- Tìm duplicates
- Xóa duplicates
- Giữ lại 1 record

---

## 2️⃣ TẠI SAO TỒN TẠI DEDUPLICATION?

**Deduplication tồn tại để:**
- **Data quality**: Đảm bảo data unique
- **Storage**: Tiết kiệm storage
- **Consistency**: Đảm bảo consistency

**Nếu không có:**
- Duplicate data
- Data inconsistency
- Storage waste

---

## 3️⃣ PRODUCTION STORY: CLEANUP DUPLICATE USERS

**Context:**
Có duplicate users → cần cleanup → dùng deduplication.

**Problem:**
- Duplicate emails
- Data inconsistency
- Users confusion

**Fix:**
- Identify duplicates
- Keep one, delete others
- Result: Clean data, no duplicates

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Deduplication**: Tìm và xóa duplicates
2. **Keep one**: Giữ lại 1 record
3. **Best practice**: Identify trước, delete sau

---

**Chuẩn bị cho Day-100: Final Review** 🚀
