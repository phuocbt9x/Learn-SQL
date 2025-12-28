# Day-058: Solutions - Partitioning - Concept

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Partitioning

**Partitioning:** Chia table thành nhiều partitions.

**Tại sao cần:** Performance, maintenance, scale.

**Partition pruning:** Skip partitions không cần.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Thiết kế Partitioning

**a)**
```sql
CREATE TABLE orders (
  id INT,
  user_id INT,
  created_at DATE
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024_01 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

**b)**
```sql
EXPLAIN SELECT * FROM orders WHERE created_at = '2024-01-15';
-- Kiểm tra partition pruning
```

---

**Chúc mừng hoàn thành Day-058!** 🎉
