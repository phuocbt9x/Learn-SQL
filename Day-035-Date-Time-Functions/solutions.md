# Day-035: Solutions - Date & Time Functions

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Date Functions

**Date Functions:** EXTRACT, DATE_TRUNC, AGE.

**Timestamp arithmetic:** Add/subtract intervals.

**Timezone:** Convert timezones.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Viết Date Function Queries

**a)**
```sql
SELECT EXTRACT(YEAR FROM created_at) as year FROM orders;
```

**b)**
```sql
SELECT DATE_TRUNC('month', created_at) as month FROM orders;
```

**c)**
```sql
SELECT AGE(created_at) as age FROM orders;
```

---

**Chúc mừng hoàn thành Day-035!** 🎉

**Chuẩn bị cho Phase 2.5!** 🚀
