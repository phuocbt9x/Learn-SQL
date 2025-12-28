# Day-077: DDL - ALTER TABLE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- ALTER TABLE syntax
- ADD/DROP/MODIFY column
- ADD/DROP constraint
- Online DDL (nếu hỗ trợ)
- Khi nào dùng ALTER TABLE?
- Hậu quả nếu ALTER TABLE sai

---

## 1️⃣ ALTER TABLE LÀ GÌ?

**ALTER TABLE** là câu lệnh DDL để **thay đổi cấu trúc bảng** đã tồn tại:

```sql
-- Thêm column
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- Xóa column
ALTER TABLE users DROP COLUMN phone;

-- Sửa column
ALTER TABLE users ALTER COLUMN email TYPE VARCHAR(255);
```

**Đặc điểm:**
- Thay đổi schema của table đã tồn tại
- Có thể lock table (tùy database)
- Không thể rollback (DDL là auto-commit)
- Cần cẩn thận trong production

---

## 2️⃣ TẠI SAO TỒN TẠI ALTER TABLE?

**ALTER TABLE tồn tại để:**
- **Schema evolution**: Thay đổi schema khi requirements thay đổi
- **Migration**: Migrate schema không cần recreate table
- **Maintenance**: Thêm/sửa/xóa columns, constraints
- **Performance**: Thêm indexes, modify data types

**Nếu không có ALTER TABLE:**
- Phải drop và recreate table → mất data
- Không thể migrate schema an toàn
- Khó maintain và evolve schema

---

## 3️⃣ ADD/DROP/MODIFY COLUMN

### **ADD COLUMN**

**ADD COLUMN** thêm column mới vào table:

```sql
-- Thêm column đơn giản
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- Thêm column với NOT NULL và DEFAULT
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active' NOT NULL;

-- Thêm column với constraint
ALTER TABLE users ADD COLUMN age INTEGER CHECK (age >= 0);
```

**Khi nào dùng:**
- Thêm feature mới cần column mới
- Migration: Thêm column cho backward compatibility

**Hậu quả nếu dùng sai:**
- Thêm NOT NULL không có DEFAULT → lỗi nếu table có data
- Thêm column không cần thiết → waste storage
- Lock table lâu → downtime

---

### **DROP COLUMN**

**DROP COLUMN** xóa column khỏi table:

```sql
-- Xóa column
ALTER TABLE users DROP COLUMN phone;

-- Xóa column với CASCADE (nếu có dependencies)
ALTER TABLE users DROP COLUMN phone CASCADE;
```

**Khi nào dùng:**
- Column không còn cần thiết
- Cleanup schema

**Hậu quả nếu dùng sai:**
- Xóa nhầm column → mất data vĩnh viễn
- Xóa column đang được dùng → break application
- Lock table → downtime

---

### **MODIFY COLUMN**

**MODIFY COLUMN** (hoặc ALTER COLUMN) sửa đổi column:

```sql
-- Thay đổi data type
ALTER TABLE users ALTER COLUMN email TYPE VARCHAR(255);

-- Thêm NOT NULL
ALTER TABLE users ALTER COLUMN email SET NOT NULL;

-- Xóa NOT NULL
ALTER TABLE users ALTER COLUMN email DROP NOT NULL;

-- Thay đổi DEFAULT
ALTER TABLE users ALTER COLUMN status SET DEFAULT 'inactive';
```

**Khi nào dùng:**
- Thay đổi data type (VARCHAR length, etc.)
- Thêm/xóa constraints
- Thay đổi DEFAULT value

**Hậu quả nếu dùng sai:**
- Thay đổi data type không compatible → lỗi
- Thêm NOT NULL không có DEFAULT → lỗi nếu có NULL values
- Lock table lâu → downtime

---

## 4️⃣ ADD/DROP CONSTRAINT

### **ADD CONSTRAINT**

**ADD CONSTRAINT** thêm constraint mới:

```sql
-- Thêm PRIMARY KEY
ALTER TABLE users ADD CONSTRAINT pk_users PRIMARY KEY (id);

-- Thêm FOREIGN KEY
ALTER TABLE orders ADD CONSTRAINT fk_orders_user_id 
  FOREIGN KEY (user_id) REFERENCES users(id);

-- Thêm UNIQUE
ALTER TABLE users ADD CONSTRAINT uk_users_email UNIQUE (email);

-- Thêm CHECK
ALTER TABLE products ADD CONSTRAINT ck_products_price 
  CHECK (price > 0);
```

**Khi nào dùng:**
- Thêm constraints sau khi tạo table
- Migration: Thêm constraints cho existing data

**Hậu quả nếu dùng sai:**
- Thêm constraint vi phạm existing data → lỗi
- Lock table lâu → downtime

---

### **DROP CONSTRAINT**

**DROP CONSTRAINT** xóa constraint:

```sql
-- Xóa constraint
ALTER TABLE users DROP CONSTRAINT uk_users_email;

-- Xóa PRIMARY KEY
ALTER TABLE users DROP CONSTRAINT pk_users;
```

**Khi nào dùng:**
- Xóa constraint không còn cần thiết
- Migration: Tạm thời xóa constraint

**Hậu quả nếu dùng sai:**
- Xóa constraint quan trọng → mất data integrity
- Xóa FOREIGN KEY → orphan records

---

## 5️⃣ ONLINE DDL

**Online DDL** cho phép ALTER TABLE **không lock table**:

- **MySQL 5.6+**: ALGORITHM=INPLACE, LOCK=NONE
- **PostgreSQL**: Một số operations không lock (thêm column với DEFAULT)
- **SQL Server**: ONLINE option

**Ví dụ (MySQL):**
```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20), 
  ALGORITHM=INPLACE, LOCK=NONE;
```

**Khi nào dùng:**
- Production: Cần ALTER TABLE không downtime
- Large tables: Tránh lock lâu

**Hậu quả nếu không dùng:**
- Lock table → downtime
- Users không thể access table

---

## 6️⃣ KHI NÀO DÙNG ALTER TABLE TRONG PRODUCTION?

**Dùng khi:**
- **Schema migration**: Thay đổi schema cho feature mới
- **Performance optimization**: Thêm indexes, modify data types
- **Maintenance**: Cleanup, refactor schema

**Best practices:**
- **Test trước**: Test trên staging trước
- **Backup**: Backup trước khi ALTER
- **Online DDL**: Dùng nếu có thể
- **Gradual migration**: Migrate từng bước
- **Rollback plan**: Có kế hoạch rollback

---

## 7️⃣ PRODUCTION STORY: MIGRATE SCHEMA KHÔNG DOWNTIME

**Context:**
Cần thêm column `phone` vào table `users` (10 triệu rows) không downtime.

**Problem:**
- ALTER TABLE thông thường → lock table → downtime
- Users không thể access trong quá trình ALTER

**Investigation:**
- PostgreSQL: ADD COLUMN với DEFAULT không lock table
- MySQL: Cần ALGORITHM=INPLACE, LOCK=NONE

**Root Cause:**
- ALTER TABLE mặc định lock table

**Fix:**

**PostgreSQL:**
```sql
-- Step 1: Thêm column với DEFAULT (không lock)
ALTER TABLE users ADD COLUMN phone VARCHAR(20) DEFAULT NULL;

-- Step 2: Update existing rows (có thể làm gradual)
UPDATE users SET phone = '...' WHERE phone IS NULL;

-- Step 3: Thêm NOT NULL nếu cần (lock ngắn)
ALTER TABLE users ALTER COLUMN phone SET NOT NULL;
ALTER TABLE users ALTER COLUMN phone DROP DEFAULT;
```

**MySQL:**
```sql
-- Dùng Online DDL
ALTER TABLE users ADD COLUMN phone VARCHAR(20), 
  ALGORITHM=INPLACE, LOCK=NONE;
```

**Result:**
- Không downtime
- Users vẫn có thể access table
- Migration thành công

**Lesson Learned:**
- Hiểu Online DDL của database
- Plan migration cẩn thận
- Test trên staging trước

---

## 8️⃣ SO SÁNH: ALTER TABLE VỚI VÀ KHÔNG CÓ ONLINE DDL

**Query A: ALTER TABLE thông thường**
```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
-- Lock table → downtime
```

**Query B: ALTER TABLE với Online DDL**
```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20), 
  ALGORITHM=INPLACE, LOCK=NONE;
-- Không lock table → không downtime
```

**So sánh:**

| Aspect | Query A | Query B |
|--------|---------|---------|
| **Downtime** | ❌ Có downtime | ✅ Không downtime |
| **User Impact** | ❌ Users bị block | ✅ Users vẫn access |
| **Performance** | ❌ Lock table | ✅ Không lock |
| **Production** | ❌ Không nên dùng | ✅ Nên dùng |

**Kết luận:**
- Query B tốt hơn cho production
- Online DDL cho phép migrate không downtime
- Luôn dùng Online DDL nếu có thể

---

## 9️⃣ TÓM TẮT

**Key Takeaways:**
1. **ALTER TABLE**: Thay đổi cấu trúc table đã tồn tại
2. **ADD/DROP/MODIFY**: Thêm/sửa/xóa columns
3. **ADD/DROP CONSTRAINT**: Thêm/xóa constraints
4. **Online DDL**: Migrate không downtime
5. **Best practice**: Test trước, backup, dùng Online DDL, có rollback plan

---




**Chuẩn bị cho [Day-078: DDL-DROP-TRUNCATE-DELETE](../Day-078-DDL-DROP-TRUNCATE-DELETE/theory.md)** 🚀
