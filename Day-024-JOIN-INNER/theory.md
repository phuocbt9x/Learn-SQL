# Day-024: JOIN - INNER JOIN

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- JOIN là gì? Tại sao cần JOIN?
- INNER JOIN là gì?
- JOIN syntax (explicit vs implicit)
- JOIN execution (high-level)

---

## 1️⃣ JOIN LÀ GÌ?

**JOIN** kết hợp rows từ **nhiều tables**:

```sql
SELECT u.name, o.total_amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

---

## 2️⃣ INNER JOIN LÀ GÌ?

**INNER JOIN** chỉ trả về rows **có match** ở cả 2 tables:

```sql
SELECT u.name, o.total_amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

**Kết quả:** Chỉ users có orders.

---

## 3️⃣ JOIN SYNTAX

**Explicit (recommended):**
```sql
SELECT * FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

**Implicit (không recommended):**
```sql
SELECT * FROM users u, orders o
WHERE u.id = o.user_id;
```

---

## 4️⃣ JOIN EXECUTION (HIGH-LEVEL)

**Các strategies:**
- Nested Loop Join
- Hash Join
- Merge Join

**Database tự chọn strategy tốt nhất.**

---

## 5️⃣ PRODUCTION STORY: QUERY TIMEOUT DO JOIN SAI THỨ TỰ

**Context:**
JOIN sai thứ tự → query timeout.

**Fix:**
JOIN đúng thứ tự → query nhanh.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. JOIN: Kết hợp rows từ nhiều tables
2. INNER JOIN: Chỉ rows có match
3. Syntax: Explicit tốt hơn implicit
4. Performance: Database tự chọn strategy

---

**Chuẩn bị cho Day-025: JOIN - LEFT/RIGHT JOIN** 🚀
