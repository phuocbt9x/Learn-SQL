# Day-047: Index - Covering Index

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Covering index là gì?
- Index Only Scan
- Trade-off: Index size vs Query speed
- Khi nào dùng covering index?

---

## 1️⃣ COVERING INDEX LÀ GÌ?

**Covering index** là index chứa **tất cả columns cần thiết** cho query:

```sql
CREATE INDEX idx_orders_covering ON orders(user_id, created_at, total_amount);
```

**Query:**
```sql
SELECT user_id, created_at, total_amount 
FROM orders 
WHERE user_id = 123;
```

**Kết quả:** Index Only Scan (không cần đọc table).

---

## 2️⃣ INDEX ONLY SCAN

**Index Only Scan:**
- Chỉ đọc index (không đọc table)
- Nhanh hơn Index Scan
- Cần covering index

---

## 3️⃣ TRADE-OFF: INDEX SIZE VS QUERY SPEED

**Trade-off:**
- **Larger index**: Tốn storage
- **Faster queries**: Index Only Scan nhanh hơn

**Cân nhắc:**
- Query frequency
- Storage cost
- Performance requirements

---

## 4️⃣ PRODUCTION STORY: QUERY NHANH HƠN 10X VỚI COVERING INDEX

**Context:**
Query chậm 1s → Index Scan (phải đọc table).

**Fix:**
Tạo covering index → Index Only Scan → nhanh 0.1s (nhanh hơn 10x).

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Covering index: Chứa tất cả columns cần thiết
2. Index Only Scan: Chỉ đọc index, nhanh hơn
3. Trade-off: Index size vs Query speed
4. Best practice: Dùng cho frequently-queried columns

---



**Chuẩn bị cho [Day-048: Query-Performance-Full-Table-Scan](Day-048-Query-Performance-Full-Table-Scan/theory.md)** 🚀
