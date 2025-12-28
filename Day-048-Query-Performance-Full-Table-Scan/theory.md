# Day-048: Query Performance - Full Table Scan

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Khi nào Full Table Scan xảy ra?
- Khi nào Full Table Scan là tốt?
- Khi nào Full Table Scan là xấu?
- Cách tránh Full Table Scan không cần thiết

---

## 1️⃣ KHI NÀO FULL TABLE SCAN XẢY RA?

**Full Table Scan xảy ra khi:**
- Không có index trên WHERE columns
- Query trả về > 20-30% rows (Planner chọn Seq Scan)
- Index không selective (quá nhiều rows match)

---

## 2️⃣ KHI NÀO FULL TABLE SCAN LÀ TỐT?

**Full Table Scan tốt khi:**
- Table nhỏ (< 1000 rows)
- Query trả về > 20-30% rows
- Index overhead > Seq Scan cost

---

## 3️⃣ KHI NÀO FULL TABLE SCAN LÀ XẤU?

**Full Table Scan xấu khi:**
- Table lớn (> 100K rows)
- Query chỉ trả về vài rows
- Có index available nhưng không dùng

---

## 4️⃣ PRODUCTION STORY: FULL TABLE SCAN NHANH HƠN INDEX SCAN (EDGE CASE)

**Context:**
Table nhỏ, query trả về 80% rows → Full Table Scan nhanh hơn Index Scan.

**Lesson:**
Full Table Scan không phải lúc nào cũng xấu.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Full Table Scan: Scan toàn bộ table
2. Khi nào tốt: Table nhỏ, query trả về nhiều rows
3. Khi nào xấu: Table lớn, query trả về ít rows
4. Best practice: Hiểu khi nào Full Table Scan là tốt

---

**Chuẩn bị cho Day-049: Query Performance - Join Algorithms** 🚀
