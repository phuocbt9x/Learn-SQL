# Day-036: Window Functions - Giới thiệu

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Window Functions là gì?
- Tại sao cần Window Functions?
- OVER clause
- Window Functions vs Aggregate Functions

---

## 1️⃣ WINDOW FUNCTIONS LÀ GÌ?

**Window Functions** tính toán trên **window (cửa sổ) của rows** mà không group rows:

```sql
SELECT name, 
       salary,
       AVG(salary) OVER() as avg_salary
FROM employees;
```

**Đặc điểm:**
- Không group rows (giữ tất cả rows)
- Tính toán trên window của rows
- Dùng OVER() clause

---

## 2️⃣ TẠI SAO CẦN WINDOW FUNCTIONS?

**Lợi ích:**
- **Giữ tất cả rows**: Không mất rows như GROUP BY
- **Tính toán phức tạp**: Running totals, rankings, comparisons
- **Performance**: Thường nhanh hơn subqueries

---

## 3️⃣ OVER CLAUSE

**OVER()** định nghĩa window:

```sql
SELECT name, 
       salary,
       AVG(salary) OVER() as avg_salary,
       ROW_NUMBER() OVER(ORDER BY salary DESC) as rank
FROM employees;
```

**PARTITION BY:**
```sql
SELECT name, 
       department,
       salary,
       AVG(salary) OVER(PARTITION BY department) as dept_avg
FROM employees;
```

---

## 4️⃣ WINDOW FUNCTIONS VS AGGREGATE FUNCTIONS

**Aggregate Functions:**
- Group rows
- Mất individual rows

**Window Functions:**
- Giữ tất cả rows
- Tính toán trên window

---

## 5️⃣ PRODUCTION STORY: QUERY PHỨC TẠP ĐƠN GIẢN HÓA BẰNG WINDOW FUNCTIONS

**Context:**
Query phức tạp với subqueries → khó đọc, chậm.

**Fix:**
Dùng Window Functions → đơn giản, nhanh hơn.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. Window Functions: Tính toán trên window của rows
2. OVER clause: Định nghĩa window
3. Giữ rows: Không mất rows như GROUP BY
4. Performance: Thường nhanh hơn subqueries

---

**Chuẩn bị cho Day-037: Window Functions - RANK** 🚀
