# Day-055: Solutions - Statistics & Query Planner

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Statistics & Query Planner

**Statistics:** Thông tin về data distribution.

**ANALYZE:** Update statistics.

**Planner:** Dùng statistics để chọn plan tốt nhất.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Update Statistics

**a)**
```sql
ANALYZE users;
ANALYZE orders;
```

**b)**
```sql
-- Kiểm tra statistics
SELECT schemaname, tablename, last_analyze 
FROM pg_stat_user_tables;
```

---

**Chúc mừng hoàn thành Day-055!** 🎉

**Chuẩn bị cho Phase 3.4!** 🚀
