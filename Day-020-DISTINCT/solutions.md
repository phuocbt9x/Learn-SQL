# Day-020: Solutions - DISTINCT

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: DISTINCT là gì?

**DISTINCT:** Loại bỏ rows trùng lặp.

**DISTINCT vs GROUP BY:**
- DISTINCT: Loại bỏ duplicates, không aggregate
- GROUP BY: Nhóm rows, có thể aggregate

**Performance:** DISTINCT có thể chậm do sort.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết DISTINCT Queries

**a)**
```sql
SELECT DISTINCT status FROM orders;
```

**b)**
```sql
SELECT DISTINCT user_id FROM orders;
```

---

**Chúc mừng hoàn thành Day-020!** 🎉

**Chuẩn bị cho Phase 2.2!** 🚀
