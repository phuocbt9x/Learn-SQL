# Day-095: Solutions - Interview Pattern - Self JOIN

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Self JOIN

**Self JOIN:** JOIN table với chính nó.

**Khi nào dùng:** Hierarchical data, compare rows, recursive queries.

**Hierarchical data:** Employee-manager, category-subcategory.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Employee-Manager Hierarchy

**Solution:**

```sql
-- Employee và Manager
SELECT 
  e.id AS employee_id,
  e.name AS employee_name,
  m.name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

-- Full hierarchy (recursive CTE)
WITH RECURSIVE employee_hierarchy AS (
  SELECT id, name, manager_id, 0 AS level
  FROM employees
  WHERE manager_id IS NULL
  
  UNION ALL
  
  SELECT e.id, e.name, e.manager_id, eh.level + 1
  FROM employees e
  JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy;
```

---

**Chúc mừng hoàn thành Day-095!** 🎉

**Chuẩn bị cho Phase 5.5!** 🚀
