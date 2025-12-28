# Day-053: Query Optimization - Avoid SELECT *

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Tại sao tránh SELECT *?
- Impact lên network, memory, index usage
- Best practices
- Khi nào có thể dùng SELECT *?

---

## 1️⃣ TẠI SAO TRÁNH SELECT *?

**Vấn đề với SELECT *:**
- **Network**: Tốn bandwidth không cần thiết
- **Memory**: Tốn memory
- **Index usage**: Không tận dụng covering index
- **Maintainability**: Khó maintain khi schema thay đổi

---

## 2️⃣ IMPACT LÊN NETWORK, MEMORY, INDEX USAGE

**Network:**
- SELECT * lấy tất cả columns → tốn bandwidth

**Memory:**
- SELECT * load tất cả data → tốn memory

**Index usage:**
- SELECT * không tận dụng covering index
- Không thể Index Only Scan

---

## 3️⃣ BEST PRACTICES

**Best practices:**
- Luôn dùng SELECT column_list trong production
- Chỉ SELECT columns cần thiết
- Tận dụng covering index

---

## 4️⃣ KHI NÀO CÓ THỂ DÙNG SELECT *?

**Có thể dùng khi:**
- Development/testing
- Table nhỏ, ít columns
- Quick queries

**KHÔNG nên dùng khi:**
- Production
- Table lớn, nhiều columns
- Performance critical

---

## 5️⃣ PRODUCTION STORY: SELECT * LÀM QUERY CHẬM VÀ TỐN BỘ NHỚ

**Context:**
SELECT * trên table 20 columns → query chậm 10s, tốn 500MB memory.

**Fix:**
SELECT chỉ 5 columns cần thiết → query nhanh 0.5s, tốn 25MB memory.

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. Tránh SELECT *: Tốn network, memory
2. Impact: Network, memory, index usage
3. Best practice: Luôn SELECT column_list trong production
4. Khi nào dùng: Development/testing only

---



**Chuẩn bị cho [Day-054: Query-Optimization-LIMIT](../Day-054-Query-Optimization-LIMIT/theory.md)** 🚀
