# Day-046: Index Types - Unique Index

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Unique index là gì?
- Unique index vs Primary Key
- Khi nào dùng unique index?
- Hậu quả nếu không có unique index

---

## 1️⃣ UNIQUE INDEX LÀ GÌ?

**Unique index** đảm bảo **không có duplicate values**:

```sql
CREATE UNIQUE INDEX idx_users_email ON users(email);
```

**Đặc điểm:**
- Đảm bảo uniqueness
- Có thể có NULL (tùy database)
- Có thể có nhiều unique indexes trên một table

---

## 2️⃣ UNIQUE INDEX VS PRIMARY KEY

**Primary Key:**
- Chỉ có một per table
- Không được NULL
- Tự động tạo unique index

**Unique Index:**
- Có thể có nhiều per table
- Có thể có NULL (tùy database)
- Explicit tạo

---

## 3️⃣ KHI NÀO DÙNG UNIQUE INDEX?

**Dùng khi:**
- Cần đảm bảo uniqueness (email, username, etc.)
- Không phải Primary Key
- Cần index cho performance

---

## 4️⃣ PRODUCTION STORY: DUPLICATE PREVENTION VỚI UNIQUE INDEX

**Context:**
Không có unique index → duplicate emails → data inconsistency.

**Fix:**
Tạo unique index → prevent duplicates → data integrity.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Unique index: Đảm bảo uniqueness
2. Unique index vs Primary Key: Khác nhau
3. Khi nào dùng: Cần uniqueness, không phải PK
4. Best practice: Dùng cho business-critical unique columns

---

**Chuẩn bị cho Day-047: Index - Covering Index** 🚀
