# Day-060: Solutions - Review Phase 3

## 🎯 BÀI TẬP 1: TỔNG HỢP KIẾN THỨC

### Câu 1.1: Advanced SQL & Performance

**EXPLAIN:** Hiển thị execution plan.

**Index types:** B-Tree, Composite, Partial, Covering, Unique.

**Query optimization:** WHERE, JOIN, SELECT, LIMIT.

**Advanced:** Materialized Views, Partitioning.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Complex Performance Optimization

**Ví dụ:**
```sql
-- 1. EXPLAIN ANALYZE
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;

-- 2. Tạo index
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 3. Kiểm tra lại
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;
```

---

**Chúc mừng hoàn thành Phase 3!** 🎉

**Chuẩn bị cho Phase 4: Transactions & Concurrency** 🚀
