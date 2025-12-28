# Day-077: Solutions - DDL - ALTER TABLE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: ALTER TABLE là gì?

**ALTER TABLE:** Câu lệnh DDL để thay đổi cấu trúc table đã tồn tại.

**Tại sao cần:** Schema evolution, migration, maintenance, performance optimization.

**Khi nào dùng:** Thay đổi schema, thêm/sửa/xóa columns, constraints.

**Hậu quả nếu dùng sai:**
- Lock table → downtime
- Mất data nếu drop nhầm
- Break application nếu thay đổi không compatible

---

### Câu 1.2: ADD/DROP/MODIFY Column

**ADD COLUMN:** Thêm column mới. Dùng khi cần feature mới.

**DROP COLUMN:** Xóa column. Cẩn thận: mất data vĩnh viễn, có thể break application.

**MODIFY COLUMN:** Sửa đổi column. Dùng khi cần thay đổi data type, constraints.

**Hậu quả nếu dùng sai:**
- ADD NOT NULL không có DEFAULT → lỗi nếu có data
- DROP column đang dùng → break application
- MODIFY data type không compatible → lỗi

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Thêm Column với Constraints

**Solution:**

```sql
-- Step 1: Thêm column phone (cho phép NULL trước)
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- Step 2: Update existing rows (nếu cần)
-- UPDATE users SET phone = '...' WHERE phone IS NULL;

-- Step 3: Thêm UNIQUE constraint (sau khi đảm bảo không duplicate)
ALTER TABLE users ADD CONSTRAINT uk_users_phone UNIQUE (phone);

-- Step 4: Thêm column status với DEFAULT và NOT NULL (không lock trong PostgreSQL)
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active' NOT NULL;

-- Step 5: Thêm column age với CHECK
ALTER TABLE users ADD COLUMN age INTEGER;
ALTER TABLE users ADD CONSTRAINT ck_users_age CHECK (age IS NULL OR age >= 0);
```

**Giải thích:**
- Thêm column cho phép NULL trước, sau đó thêm constraint
- Dùng DEFAULT để thêm NOT NULL không lock (PostgreSQL)
- CHECK constraint cho phép NULL (age có thể không biết)

---

### Câu 2.2: Modify Column

**Solution:**

```sql
-- Step 1: Thay đổi email length (PostgreSQL)
ALTER TABLE users ALTER COLUMN email TYPE VARCHAR(320);

-- Step 2: Thêm NOT NULL cho name (cần đảm bảo không có NULL trước)
-- Kiểm tra trước:
-- SELECT COUNT(*) FROM users WHERE name IS NULL;

-- Nếu có NULL, update trước:
-- UPDATE users SET name = '' WHERE name IS NULL;

-- Sau đó thêm NOT NULL:
ALTER TABLE users ALTER COLUMN name SET NOT NULL;

-- Step 3: Thay đổi DEFAULT
ALTER TABLE users ALTER COLUMN status SET DEFAULT 'pending';
```

**Giải thích:**
- Thay đổi data type: Cần đảm bảo compatible
- Thêm NOT NULL: Cần đảm bảo không có NULL values trước
- Thay đổi DEFAULT: Chỉ ảnh hưởng rows mới

---

### Câu 2.3: Add/Drop Constraints

**Solution:**

```sql
-- Step 1: Kiểm tra data trước khi thêm FOREIGN KEY
-- SELECT COUNT(*) FROM orders o 
-- LEFT JOIN users u ON o.user_id = u.id 
-- WHERE u.id IS NULL;

-- Nếu có orphan records, fix trước:
-- DELETE FROM orders WHERE user_id NOT IN (SELECT id FROM users);

-- Sau đó thêm FOREIGN KEY:
ALTER TABLE orders 
  ADD CONSTRAINT fk_orders_user_id 
  FOREIGN KEY (user_id) REFERENCES users(id);

-- Step 2: Thêm CHECK constraint (cần đảm bảo existing data thỏa mãn)
-- SELECT COUNT(*) FROM products WHERE price <= 0;

-- Nếu có invalid data, fix trước:
-- UPDATE products SET price = 0.01 WHERE price <= 0;

-- Sau đó thêm CHECK:
ALTER TABLE products 
  ADD CONSTRAINT ck_products_price 
  CHECK (price > 0);

-- Step 3: Xóa UNIQUE constraint
ALTER TABLE users DROP CONSTRAINT uk_users_email;
```

**Giải thích:**
- Thêm FOREIGN KEY: Cần đảm bảo không có orphan records
- Thêm CHECK: Cần đảm bảo existing data thỏa mãn
- Xóa constraint: Cẩn thận vì mất data integrity

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Migrate Schema không Downtime

**Solution (PostgreSQL):**

```sql
-- Step 1: Thêm category_id (cho phép NULL trước)
ALTER TABLE products ADD COLUMN category_id INTEGER;

-- Step 2: Thêm FOREIGN KEY (sau khi có data)
-- Có thể làm gradual: Update từng batch
-- UPDATE products SET category_id = ... WHERE category_id IS NULL;

-- Sau đó thêm FOREIGN KEY:
ALTER TABLE products 
  ADD CONSTRAINT fk_products_category_id 
  FOREIGN KEY (category_id) REFERENCES categories(id);

-- Step 3: Thêm stock với DEFAULT và NOT NULL (không lock)
ALTER TABLE products ADD COLUMN stock INTEGER DEFAULT 0 NOT NULL;

-- Step 4: Thêm created_at với DEFAULT (không lock)
ALTER TABLE products ADD COLUMN created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
```

**Solution (MySQL với Online DDL):**

```sql
-- Step 1: Thêm category_id
ALTER TABLE products ADD COLUMN category_id INTEGER, 
  ALGORITHM=INPLACE, LOCK=NONE;

-- Step 2: Thêm FOREIGN KEY
ALTER TABLE products 
  ADD CONSTRAINT fk_products_category_id 
  FOREIGN KEY (category_id) REFERENCES categories(id),
  ALGORITHM=INPLACE, LOCK=NONE;

-- Step 3: Thêm stock
ALTER TABLE products ADD COLUMN stock INTEGER DEFAULT 0 NOT NULL, 
  ALGORITHM=INPLACE, LOCK=NONE;

-- Step 4: Thêm created_at
ALTER TABLE products ADD COLUMN created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, 
  ALGORITHM=INPLACE, LOCK=NONE;
```

**Migration Plan:**
1. Test trên staging trước
2. Backup production database
3. Thực hiện từng step, monitor performance
4. Có rollback plan

---

### Câu 3.2: Refactor Column

**Solution:**

```sql
-- Step 1: Update NULL values thành 'pending'
UPDATE orders SET status = 'pending' WHERE status IS NULL;

-- Step 2: Thêm DEFAULT
ALTER TABLE orders ALTER COLUMN status SET DEFAULT 'pending';

-- Step 3: Thêm NOT NULL
ALTER TABLE orders ALTER COLUMN status SET NOT NULL;

-- Step 4: Thêm CHECK constraint
ALTER TABLE orders 
  ADD CONSTRAINT ck_orders_status 
  CHECK (status IN ('pending', 'confirmed', 'shipped', 'delivered', 'cancelled', 'refunded'));
```

**Giải thích:**
- Update NULL values trước
- Thêm DEFAULT và NOT NULL
- Cuối cùng thêm CHECK constraint

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Rename Column

**Solution:**

```sql
-- Step 1: Rename column
ALTER TABLE users RENAME COLUMN email TO email_address;

-- Step 2: Rename table
ALTER TABLE users RENAME TO customers;
```

**Impact:**
- Application code cần update
- Views, stored procedures cần update
- Cần migration script để update references

**Best Practice:**
- Rename trong transaction nếu có thể
- Update application code cùng lúc
- Test kỹ trước khi deploy

---

### Câu 4.2: Change Data Type

**Solution:**

```sql
-- Step 1: Kiểm tra data trước
-- SELECT MAX(price) FROM products;
-- Đảm bảo giá trị hiện tại fit trong DECIMAL(12, 2)

-- Step 2: Thay đổi data type (PostgreSQL)
ALTER TABLE products ALTER COLUMN price TYPE DECIMAL(12, 2);

-- Hoặc với USING clause nếu cần conversion:
-- ALTER TABLE products ALTER COLUMN price TYPE DECIMAL(12, 2) USING price::DECIMAL(12, 2);
```

**Khi nào an toàn:**
- Khi data type compatible (DECIMAL(10,2) → DECIMAL(12,2))
- Khi không cần conversion logic

**Khi nào cần migration:**
- Khi cần conversion logic (VARCHAR → INTEGER)
- Khi có data không compatible

---

**Chúc mừng hoàn thành Day-077!** 🎉

**Chuẩn bị cho Day-078: DDL - DROP TABLE, TRUNCATE, DELETE** 🚀

