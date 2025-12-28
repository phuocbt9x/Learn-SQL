# Day-095: Interview Pattern - Self JOIN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Self JOIN là gì?
- Khi nào dùng self JOIN?
- Hierarchical data patterns
- Production scenarios

---

## 1️⃣ SELF JOIN LÀ GÌ?

**Self JOIN** là **JOIN table với chính nó**:

```sql
-- Employee-Manager relationship
SELECT 
  e.id AS employee_id,
  e.name AS employee_name,
  m.name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

**Đặc điểm:**
- JOIN table với chính nó
- Dùng aliases để phân biệt
- Dùng cho hierarchical data

---

## 2️⃣ TẠI SAO TỒN TẠI SELF JOIN?

**Self JOIN tồn tại để:**
- **Hierarchical data**: Employee-manager, category-subcategory
- **Compare rows**: So sánh rows trong cùng table
- **Recursive queries**: Query hierarchical structures

**Nếu không có:**
- Khó query hierarchical data
- Phải dùng multiple queries
- Performance tệ

---

## 3️⃣ HIERARCHICAL DATA PATTERNS

**Employee-Manager:**
```sql
SELECT 
  e.name AS employee,
  m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

**Category-Subcategory:**
```sql
SELECT 
  c.name AS category,
  p.name AS parent_category
FROM categories c
LEFT JOIN categories p ON c.parent_id = p.id;
```

---

## 4️⃣ PRODUCTION STORY: HIERARCHICAL DATA (EMPLOYEE-MANAGER)

**Context:**
Cần hiển thị employee hierarchy → cần query manager của mỗi employee.

**Problem:**
- Query phức tạp
- Nhiều queries

**Fix:**
- Dùng Self JOIN
- Single query
- Performance tốt

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. **Self JOIN**: JOIN table với chính nó
2. **Hierarchical data**: Employee-manager, category-subcategory
3. **Best practice**: Dùng Self JOIN cho hierarchical data

---

**Chuẩn bị cho Phase 5.5!** 🚀


**Chuẩn bị cho [Day-096: Interview-Pattern-Pivot-Unpivot](../Day-096-Interview-Pattern-Pivot-Unpivot/theory.md)** 🚀
