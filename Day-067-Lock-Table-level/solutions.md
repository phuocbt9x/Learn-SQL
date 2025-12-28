# Day-067: Solutions - Lock - Table-level Lock

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Table-level Lock

**Table lock:** Lock toàn bộ table.

**SHARE vs EXCLUSIVE:** SHARE cho phép read, EXCLUSIVE không cho phép gì.

**Khi nào dùng:** DDL, bulk operations.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Sử dụng Table Lock

**a) SHARE LOCK:**
```sql
LOCK TABLE users IN SHARE MODE;
```

**b) EXCLUSIVE LOCK:**
```sql
LOCK TABLE users IN EXCLUSIVE MODE;
```

---

**Chúc mừng hoàn thành Day-067!** 🎉
