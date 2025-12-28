# Day-054: Solutions - Query Optimization - LIMIT optimization

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: LIMIT Optimization

**LIMIT với ORDER BY:** Cần index trên ORDER BY columns.

**Index cho LIMIT:** Tạo index phù hợp.

**Pagination:** Cursor-based tốt hơn OFFSET.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize LIMIT Queries

**a)**
```sql
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

SELECT * FROM orders 
ORDER BY created_at DESC 
LIMIT 10;
```

**b) Cursor-based:**
```sql
SELECT * FROM orders 
WHERE created_at < '2024-01-01'
ORDER BY created_at DESC 
LIMIT 10;
```

---

**Chúc mừng hoàn thành Day-054!** 🎉
