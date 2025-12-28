# Day-036: Solutions - Window Functions - Giới thiệu

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Window Functions là gì?

**Window Functions:** Tính toán trên window của rows.

**Tại sao cần:** Giữ tất cả rows, tính toán phức tạp.

**OVER clause:** Định nghĩa window.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Window Function Queries

**a)**
```sql
SELECT name, 
       salary,
       AVG(salary) OVER() as avg_salary
FROM employees;
```

**b)**
```sql
SELECT name, 
       salary,
       ROW_NUMBER() OVER(ORDER BY salary DESC) as rank
FROM employees;
```

---

**Chúc mừng hoàn thành Day-036!** 🎉
