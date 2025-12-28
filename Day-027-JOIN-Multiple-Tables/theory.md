# Day-027: JOIN - Multiple Tables

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- JOIN 3+ tables
- JOIN order và performance
- JOIN conditions (equality vs inequality)
- Hậu quả nếu JOIN sai thứ tự

---

## 1️⃣ JOIN 3+ TABLES

**JOIN nhiều tables:**

```sql
SELECT u.name, o.total_amount, p.name as product_name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id;
```

---

## 2️⃣ JOIN ORDER VÀ PERFORMANCE

**JOIN order quan trọng:**
- Database tự optimize
- Nhưng có thể ảnh hưởng performance
- Nên JOIN từ small → large tables

---

## 3️⃣ JOIN CONDITIONS

**Equality JOIN:**
```sql
ON u.id = o.user_id
```

**Inequality JOIN:**
```sql
ON u.created_at < o.created_at
```

---

## 4️⃣ PRODUCTION STORY: QUERY CHẬM DO JOIN 5 BẢNG KHÔNG CÓ INDEX

**Context:**
JOIN 5 tables không có indexes → query chậm 30s.

**Fix:**
Tạo indexes trên JOIN columns → query nhanh 0.5s.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. JOIN 3+ tables: Có thể JOIN nhiều tables
2. JOIN order: Ảnh hưởng performance
3. JOIN conditions: Equality vs inequality
4. Best practice: Có indexes cho JOIN columns

---

**Chuẩn bị cho Day-028: Subquery - Scalar Subquery** 🚀
