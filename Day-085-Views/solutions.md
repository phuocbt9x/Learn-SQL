# Day-085: Solutions - Views

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: View là gì?

**View:** Virtual table dựa trên query.

**vs Table:** View không lưu data, Table lưu data.

**Khi nào dùng:** Simplify queries, security, abstraction.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo View

**Solution:**

```sql
-- User orders view
CREATE VIEW user_orders AS
SELECT 
  u.id AS user_id,
  u.email,
  u.name AS user_name,
  o.id AS order_id,
  o.total,
  o.status,
  o.created_at
FROM users u
JOIN orders o ON u.id = o.user_id;

-- Product sales view
CREATE VIEW product_sales AS
SELECT 
  p.id AS product_id,
  p.name AS product_name,
  COUNT(oi.id) AS total_orders,
  SUM(oi.quantity) AS total_quantity,
  SUM(oi.quantity * oi.price) AS total_revenue
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
GROUP BY p.id, p.name;
```

---

**Chúc mừng hoàn thành Day-085!** 🎉

**Chuẩn bị cho Phase 5.3!** 🚀
