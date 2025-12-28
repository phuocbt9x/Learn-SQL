# Day-040: Review Phase 2 - Tổng hợp Query Language

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ:
- Tổng hợp lại tất cả concepts từ Day-016 đến Day-039
- Hiểu mối liên hệ giữa các concepts
- Chuẩn bị cho Phase 3: Advanced SQL & Performance
- Có nền tảng vững chắc về SQL Query Language

---

## 1️⃣ TỔNG HỢP CÁC CONCEPTS

### **Day-016 đến Day-020: Basic SQL**
- SELECT, WHERE, ORDER BY, LIMIT, DISTINCT

### **Day-021 đến Day-025: Aggregations & JOINs**
- Aggregate Functions, GROUP BY, HAVING
- INNER JOIN, LEFT/RIGHT JOIN

### **Day-026 đến Day-030: Advanced JOINs & Subqueries**
- FULL OUTER JOIN, Multiple Tables JOIN
- Subqueries (Scalar, EXISTS, IN, Correlated)

### **Day-031 đến Day-035: CTE & Functions**
- CTE, UNION, CASE, String Functions, Date Functions

### **Day-036 đến Day-039: Window Functions**
- Window Functions basics, RANK, Aggregate, LAG/LEAD

---

## 2️⃣ MỐI LIÊN HỆ GIỮA CÁC CONCEPTS

**Basic → Advanced:**
- SELECT → JOIN → Subqueries → CTE → Window Functions

**Performance:**
- Indexes → Query optimization → Execution plans

---

## 3️⃣ BEST PRACTICES

✅ **SELECT column_list**: Luôn dùng trong production
✅ **Use WHERE**: Filter data khi có thể
✅ **Indexes**: Tạo indexes cho WHERE, JOIN, ORDER BY
✅ **JOIN vs Subquery**: JOIN thường nhanh hơn
✅ **Window Functions**: Dùng cho analytics

---

## 4️⃣ CHUẨN BỊ CHO PHASE 3

**Phase 3 sẽ học:**
- Advanced SQL patterns
- Performance tuning
- Query optimization
- Index strategies

---

## 5️⃣ TÓM TẮT

**Key Learnings:**
1. SQL Query Language: Từ cơ bản đến nâng cao
2. JOINs: INNER, LEFT, RIGHT, FULL OUTER
3. Subqueries: Scalar, EXISTS, IN, Correlated
4. Window Functions: RANK, Aggregate, LAG/LEAD
5. Best practices: Performance, readability

---

**Chuẩn bị cho Phase 3: Advanced SQL & Performance** 🚀


**Chuẩn bị cho [Day-041: EXPLAIN-Execution-Plan](../Day-041-EXPLAIN-Execution-Plan/theory.md)** 🚀
