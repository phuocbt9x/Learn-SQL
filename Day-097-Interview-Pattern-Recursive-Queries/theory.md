# Day-097: Interview Pattern - Recursive Queries

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Recursive CTE
- Hierarchical queries
- Khi nào dùng recursive queries?
- Production scenarios

---

## 1️⃣ RECURSIVE CTE LÀ GÌ?

**Recursive CTE** là **CTE tự gọi chính nó**:

```sql
-- Organization tree
WITH RECURSIVE org_tree AS (
  -- Base case
  SELECT id, name, parent_id, 0 AS level
  FROM organizations
  WHERE parent_id IS NULL
  
  UNION ALL
  
  -- Recursive case
  SELECT o.id, o.name, o.parent_id, ot.level + 1
  FROM organizations o
  JOIN org_tree ot ON o.parent_id = ot.id
)
SELECT * FROM org_tree;
```

**Đặc điểm:**
- Base case: Điểm bắt đầu
- Recursive case: Tự gọi chính nó
- Dùng cho hierarchical data

---

## 2️⃣ TẠI SAO TỒN TẠI RECURSIVE QUERIES?

**Recursive queries tồn tại để:**
- **Hierarchical data**: Query tree structures
- **Graph traversal**: Traverse graphs
- **Complex relationships**: Query complex relationships

**Nếu không có:**
- Khó query hierarchical data
- Phải dùng multiple queries
- Performance tệ

---

## 3️⃣ PRODUCTION STORY: ORGANIZATION TREE VỚI RECURSIVE CTE

**Context:**
Cần query toàn bộ organization tree → dùng recursive CTE.

**Problem:**
- Query phức tạp
- Nhiều queries

**Fix:**
- Dùng recursive CTE
- Single query
- Performance tốt

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Recursive CTE**: CTE tự gọi chính nó
2. **Hierarchical data**: Query tree structures
3. **Best practice**: Dùng recursive CTE cho hierarchical data

---

**Chuẩn bị cho Day-098: Interview Pattern - Complex Joins** 🚀
