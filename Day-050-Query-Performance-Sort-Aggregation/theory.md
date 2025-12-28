# Day-050: Query Performance - Sort & Aggregation

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Sort operations (External Sort)
- Aggregation performance
- GROUP BY performance
- Cách optimize sort và aggregation

---

## 1️⃣ SORT OPERATIONS

**External Sort:**
- Sort data lớn hơn memory
- Chia thành chunks, sort từng chunk, merge
- Tốn I/O và memory

**Optimize:**
- Có index trên ORDER BY columns
- Giảm số rows sort (WHERE, LIMIT)

---

## 2️⃣ AGGREGATION PERFORMANCE

**Aggregation:**
- COUNT, SUM, AVG, MIN, MAX
- Có thể chậm với large datasets
- Cần scan toàn bộ rows

**Optimize:**
- Có index trên GROUP BY columns
- Filter trước khi aggregate (WHERE)

---

## 3️⃣ GROUP BY PERFORMANCE

**GROUP BY:**
- Nhóm rows
- Có thể chậm nếu không có index
- Cần sort hoặc hash

**Optimize:**
- Index trên GROUP BY columns
- Giảm số groups

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. Sort: External Sort cho large data
2. Aggregation: Scan toàn bộ rows
3. GROUP BY: Cần sort hoặc hash
4. Optimize: Index, filter, reduce data

---

**Chuẩn bị cho [Day-051: Query-Optimization-WHERE](Day-051-Query-Optimization-WHERE/theory.md)** 🚀
