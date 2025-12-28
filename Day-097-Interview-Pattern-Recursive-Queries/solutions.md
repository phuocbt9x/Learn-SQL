# Day-097: Solutions - Interview Pattern - Recursive Queries

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Recursive Queries

**Recursive CTE:** CTE tự gọi chính nó.

**Khi nào dùng:** Hierarchical data, graph traversal, complex relationships.

**Hierarchical queries:** Query tree structures.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Organization Tree

**Solution:**

```sql
-- Full tree
WITH RECURSIVE org_tree AS (
  SELECT id, name, parent_id, 0 AS level
  FROM organizations
  WHERE parent_id IS NULL
  
  UNION ALL
  
  SELECT o.id, o.name, o.parent_id, ot.level + 1
  FROM organizations o
  JOIN org_tree ot ON o.parent_id = ot.id
)
SELECT * FROM org_tree;

-- Descendants of specific org
WITH RECURSIVE descendants AS (
  SELECT id, name, parent_id
  FROM organizations
  WHERE id = 1
  
  UNION ALL
  
  SELECT o.id, o.name, o.parent_id
  FROM organizations o
  JOIN descendants d ON o.parent_id = d.id
)
SELECT * FROM descendants;
```

---

**Chúc mừng hoàn thành Day-097!** 🎉
