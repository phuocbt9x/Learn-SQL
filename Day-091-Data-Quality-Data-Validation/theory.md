# Day-091: Data Quality - Data Validation

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- CHECK constraints
- Data type validation
- Khi nào dùng validation?
- Hậu quả nếu không có validation

---

## 1️⃣ DATA VALIDATION LÀ GÌ?

**Data validation** là **kiểm tra dữ liệu** trước khi lưu vào database:

```sql
-- CHECK constraint
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) CHECK (price > 0),
  stock INTEGER CHECK (stock >= 0),
  status VARCHAR(20) CHECK (status IN ('active', 'inactive', 'archived'))
);
```

**Đặc điểm:**
- Validate data ở database level
- Reject invalid data
- Đảm bảo data quality

---

## 2️⃣ TẠI SAO TỒN TẠI DATA VALIDATION?

**Data validation tồn tại để:**
- **Data quality**: Đảm bảo data đúng format
- **Business rules**: Enforce business rules
- **Prevent errors**: Ngăn invalid data
- **Consistency**: Đảm bảo data nhất quán

**Nếu không có:**
- Invalid data vào database
- Business logic errors
- Data inconsistency

---

## 3️⃣ CHECK CONSTRAINTS

**CHECK constraints** validate data theo điều kiện:

```sql
-- Range check
CHECK (price > 0 AND price <= 10000)

-- Enum check
CHECK (status IN ('active', 'inactive'))

-- Complex check
CHECK (discount_price IS NULL OR (discount_price > 0 AND discount_price < price))
```

**Khi nào dùng:**
- Business rules validation
- Range checks
- Format validation

---

## 4️⃣ DATA TYPE VALIDATION

**Data type validation** đảm bảo data đúng type:

```sql
-- Correct types
price DECIMAL(10, 2)  -- Số thập phân
quantity INTEGER      -- Số nguyên
email VARCHAR(255)    -- String
created_at TIMESTAMP  -- Date/time
```

**Khi nào dùng:**
- Đảm bảo data type đúng
- Prevent type errors
- Database tự động validate

---

## 5️⃣ PRODUCTION STORY: INVALID DATA VÀO DATABASE

**Context:**
Application không validate → invalid data vào database → business logic errors.

**Problem:**
- Negative prices
- Invalid status values
- Data inconsistency

**Fix:**
- Add CHECK constraints
- Validate ở database level
- Result: Không còn invalid data

---

## 6️⃣ TÓM TẮT

**Key Takeaways:**
1. **Data validation**: Kiểm tra dữ liệu trước khi lưu
2. **CHECK constraints**: Validate business rules
3. **Data type validation**: Đảm bảo đúng type
4. **Best practice**: Validate ở database level

---




**Chuẩn bị cho [Day-092: Interview-Pattern-Top-N-per-Group](Day-092-Interview-Pattern-Top-N-per-Group/theory.md)** 🚀
