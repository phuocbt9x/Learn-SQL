# Day-058: Partitioning - Concept

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Partitioning là gì?
- Tại sao cần partitioning?
- Partition pruning
- Khi nào dùng partitioning?

---

## 1️⃣ PARTITIONING LÀ GÌ?

**Partitioning** chia table thành **nhiều partitions nhỏ hơn**:

```sql
CREATE TABLE orders (
  id INT,
  user_id INT,
  created_at DATE
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024_01 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

**Đặc điểm:**
- Chia table thành nhiều partitions
- Mỗi partition là table riêng
- Query chỉ scan partitions cần thiết

---

## 2️⃣ TẠI SAO CẦN PARTITIONING?

**Lợi ích:**
- **Performance**: Query chỉ scan partitions cần thiết
- **Maintenance**: Dễ maintain (drop old partitions)
- **Scale**: Scale tốt hơn với large tables

---

## 3️⃣ PARTITION PRUNING

**Partition pruning:**
- Database tự động skip partitions không cần
- Query chỉ scan partitions matching WHERE condition

**Ví dụ:**
```sql
SELECT * FROM orders WHERE created_at = '2024-01-15';
-- Chỉ scan orders_2024_01 partition
```

---

## 4️⃣ PRODUCTION STORY: QUERY NHANH HƠN 100X NHỜ PARTITIONING

**Context:**
Table 100 triệu rows → query chậm 10s.

**Fix:**
Partitioning theo tháng → query chỉ scan 1 partition → nhanh 0.1s (nhanh hơn 100x).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Partitioning: Chia table thành partitions
2. Tại sao cần: Performance, maintenance, scale
3. Partition pruning: Skip partitions không cần
4. Best practice: Dùng cho large tables, time-series data

---

**Chuẩn bị cho Day-059: Common Performance Anti-patterns** 🚀
