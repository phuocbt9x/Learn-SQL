# Day-043: Index Types - B-Tree Index

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- B-Tree index chi tiết
- Index Scan vs Index Only Scan
- Index trên single column
- Khi nào dùng B-Tree index?

---

## 1️⃣ B-TREE INDEX CHI TIẾT

**B-Tree index** là cấu trúc dữ liệu dạng cây:

```
        [50]
       /    \
    [25]    [75]
   /   \    /   \
 [10] [30] [60] [90]
```

**Đặc điểm:**
- Sorted: Giá trị được sắp xếp
- Fast lookup: O(log n) complexity
- Range queries: Hỗ trợ tốt

---

## 2️⃣ INDEX SCAN VS INDEX ONLY SCAN

**Index Scan:**
- Dùng index để tìm rows
- Sau đó đọc rows từ table

**Index Only Scan:**
- Chỉ dùng index (không đọc table)
- Nhanh hơn Index Scan

---

## 3️⃣ INDEX TRÊN SINGLE COLUMN

**Tạo index:**
```sql
CREATE INDEX idx_users_email ON users(email);
```

**Dùng index:**
```sql
SELECT * FROM users WHERE email = 'john@example.com';
```

---

## 4️⃣ PRODUCTION STORY: QUERY TỪ 5S → 0.05S NHỜ INDEX

**Context:**
Query chậm 5s → không có index.

**Fix:**
Tạo B-Tree index → query nhanh 0.05s (nhanh hơn 100x).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. B-Tree index: Cấu trúc cây, sorted
2. Index Scan vs Index Only Scan: Index Only Scan nhanh hơn
3. Single column: Index trên một column
4. Performance: Index giúp queries nhanh hơn rất nhiều

---

**Chuẩn bị cho Day-044: Index Types - Composite Index** 🚀
