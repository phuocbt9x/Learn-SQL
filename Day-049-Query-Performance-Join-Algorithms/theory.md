# Day-049: Query Performance - Join Algorithms

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Nested Loop Join
- Hash Join
- Merge Join
- Khi nào dùng algorithm nào?

---

## 1️⃣ NESTED LOOP JOIN

**Nested Loop Join:**
- Với mỗi row của table 1, scan table 2
- O(n*m) complexity
- Tốt cho: Small tables, có index trên join column

---

## 2️⃣ HASH JOIN

**Hash Join:**
- Build hash table từ table nhỏ hơn
- Probe hash table với table lớn hơn
- O(n+m) complexity
- Tốt cho: Large tables, không có index

---

## 3️⃣ MERGE JOIN

**Merge Join:**
- Cả 2 tables phải sorted
- Merge 2 sorted lists
- O(n+m) complexity
- Tốt cho: Sorted tables, có index

---

## 4️⃣ KHI NÀO DÙNG ALGORITHM NÀO?

**Nested Loop:**
- Small tables
- Có index

**Hash Join:**
- Large tables
- Không có index

**Merge Join:**
- Sorted tables
- Có index

---

## 5️⃣ PRODUCTION STORY: QUERY CHẬM DO NESTED LOOP JOIN VỚI BẢNG LỚN

**Context:**
Nested Loop Join với large tables → query chậm 30s.

**Fix:**
Tạo index hoặc force Hash Join → query nhanh 1s.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. Nested Loop: Small tables, có index
2. Hash Join: Large tables, không có index
3. Merge Join: Sorted tables, có index
4. Best practice: Database tự chọn, nhưng hiểu để optimize

---



**Chuẩn bị cho [Day-050: Query-Performance-Sort-Aggregation](Day-050-Query-Performance-Sort-Aggregation/theory.md)** 🚀
