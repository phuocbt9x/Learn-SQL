# Day-098: Solutions - Interview Pattern - Complex Joins

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Complex Joins

**Complex JOINs:** Nhiều JOINs với complex conditions.

**JOIN optimization:** Indexes, join order, filter early.

**Khi nào dùng:** Data warehouse, complex reports.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize Complex Query

**Solution:**

```sql
-- Query với indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_products_category_id ON products(category_id);

-- Optimized query
SELECT 
  u.name,
  o.total,
  p.name AS product_name
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE u.status = 'active'
  AND o.status = 'completed';
```

---

**Chúc mừng hoàn thành Day-098!** 🎉
