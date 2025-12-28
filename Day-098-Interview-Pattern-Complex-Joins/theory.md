# Day-098: Interview Pattern - Complex Joins

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Multiple JOINs với complex conditions
- JOIN optimization
- Khi nào dùng complex joins?
- Production scenarios

---

## 1️⃣ COMPLEX JOINS LÀ GÌ?

**Complex JOINs** là **nhiều JOINs với điều kiện phức tạp**:

```sql
-- Multiple JOINs
SELECT 
  u.name,
  o.total,
  p.name AS product_name,
  c.name AS category_name
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
JOIN categories c ON p.category_id = c.id
WHERE u.status = 'active'
  AND o.status = 'completed'
  AND c.name = 'Electronics';
```

**Đặc điểm:**
- Nhiều JOINs
- Complex conditions
- Cần optimize

---

## 2️⃣ JOIN OPTIMIZATION

**Best practices:**
1. **Indexes**: Index foreign keys
2. **Join order**: Join từ nhỏ đến lớn
3. **Filter early**: WHERE clause sớm
4. **Avoid cartesian products**: Cẩn thận với multiple JOINs

---

## 3️⃣ PRODUCTION STORY: DATA WAREHOUSE QUERY VỚI 10+ JOINS

**Context:**
Data warehouse query với 10+ JOINs → chậm.

**Problem:**
- Query phức tạp
- Performance tệ
- Timeout

**Fix:**
- Optimize join order
- Add indexes
- Filter early
- Result: Performance cải thiện 10x

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Complex JOINs**: Nhiều JOINs với complex conditions
2. **Optimization**: Indexes, join order, filter early
3. **Best practice**: Optimize join order, add indexes

---

**Chuẩn bị cho Day-099: Interview Pattern - Data Deduplication** 🚀
