# Day-045: Solutions - Index Types - Partial Index

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Partial Index

**Partial index:** Index chỉ trên subset của rows.

**Khi nào dùng:** Chỉ query subset, index size quan trọng.

**Lợi ích:** Smaller, faster, less maintenance.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Partial Index

**a)**
```sql
CREATE INDEX idx_orders_active ON orders(user_id) 
WHERE status = 'active';
```

**b)**
```sql
-- So sánh size
SELECT pg_size_pretty(pg_relation_size('idx_orders_active')) as partial_size;
SELECT pg_size_pretty(pg_relation_size('idx_orders_full')) as full_size;
```

---

**Chúc mừng hoàn thành Day-045!** 🎉

**Chuẩn bị cho Phase 3.2!** 🚀
