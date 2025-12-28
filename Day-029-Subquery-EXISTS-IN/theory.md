# Day-029: Subquery - EXISTS vs IN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- EXISTS là gì?
- IN là gì?
- EXISTS vs IN - khi nào dùng gì?
- Performance comparison

---

## 1️⃣ EXISTS LÀ GÌ?

**EXISTS** kiểm tra **có tồn tại rows** không:

```sql
SELECT * FROM users u
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
```

---

## 2️⃣ IN LÀ GÌ?

**IN** kiểm tra giá trị **có trong list** không:

```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

---

## 3️⃣ EXISTS VS IN

**EXISTS:**
- Dừng khi tìm thấy match đầu tiên
- Thường nhanh hơn với large datasets

**IN:**
- Phải evaluate toàn bộ subquery
- Có thể chậm với large datasets

---

## 4️⃣ PRODUCTION STORY: QUERY TỪ 10S → 0.5S NHỜ ĐỔI IN → EXISTS

**Context:**
Query dùng IN → chậm 10s.

**Fix:**
Đổi IN → EXISTS → nhanh 0.5s.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. EXISTS: Kiểm tra tồn tại
2. IN: Kiểm tra trong list
3. EXISTS vs IN: EXISTS thường nhanh hơn
4. Best practice: Dùng EXISTS khi có thể

---

**Chuẩn bị cho Day-030: Subquery - Correlated Subquery** 🚀
