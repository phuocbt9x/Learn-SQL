# Day-050: Solutions - Query Performance - Sort & Aggregation

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Sort & Aggregation

**Sort:** External Sort cho large data, tốn I/O.

**Aggregation:** Scan toàn bộ rows, có thể chậm.

**GROUP BY:** Cần sort hoặc hash, optimize với index.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Optimize Sort & Aggregation

**a) ORDER BY:**
```sql
-- Tạo index trên ORDER BY column
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Query sẽ dùng index
SELECT * FROM orders ORDER BY created_at DESC LIMIT 10;
```

**b) GROUP BY:**
```sql
-- Tạo index trên GROUP BY column
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Query sẽ nhanh hơn
SELECT user_id, COUNT(*) FROM orders GROUP BY user_id;
```

---

**Chúc mừng hoàn thành Day-050!** 🎉

**Chuẩn bị cho Phase 3.3!** 🚀
