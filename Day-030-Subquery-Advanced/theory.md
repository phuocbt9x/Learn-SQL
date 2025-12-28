# Day-030: Subquery - Correlated Subquery

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Correlated subquery là gì?
- Execution flow của correlated subquery
- Khi nào dùng correlated subquery?
- Performance impact

---

## 1️⃣ CORRELATED SUBQUERY LÀ GÌ?

**Correlated subquery** là subquery **reference đến outer query**:

```sql
SELECT name, 
       (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) as order_count
FROM users u;
```

**Đặc điểm:**
- Subquery phụ thuộc vào outer query
- Execute nhiều lần (mỗi row của outer query)

---

## 2️⃣ EXECUTION FLOW

**Execution flow:**
1. Execute outer query (users)
2. Với mỗi row, execute subquery
3. Return kết quả

**Vấn đề:**
- N+1 queries (1 outer + N subqueries)
- Có thể chậm với large datasets

---

## 3️⃣ KHI NÀO DÙNG CORRELATED SUBQUERY?

**Dùng khi:**
- Cần tính toán per-row
- Không thể dùng JOIN dễ dàng
- Performance acceptable

**Tránh khi:**
- Có thể dùng JOIN
- Performance critical
- Large datasets

---

## 4️⃣ PERFORMANCE IMPACT

**Correlated subquery:**
- Có thể chậm (N+1 queries)
- Tốn resources

**Alternative:**
- Dùng JOIN + GROUP BY
- Thường nhanh hơn

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Correlated subquery: Reference đến outer query
2. Execution: N+1 queries
3. Performance: Có thể chậm
4. Best practice: Cân nhắc JOIN thay vì correlated subquery

---

**Chuẩn bị cho Phase 2.4!** 🚀


**Chuẩn bị cho [Day-031: CTE-WITH](Day-031-CTE-WITH/theory.md)** 🚀
