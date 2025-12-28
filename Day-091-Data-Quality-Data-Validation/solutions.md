# Day-091: Solutions - Data Quality - Data Validation

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Data Validation là gì?

**Data validation:** Kiểm tra dữ liệu trước khi lưu.

**CHECK constraints:** Validate data theo điều kiện.

**Khi nào dùng:** Business rules, range checks, format validation.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo CHECK Constraints

**Solution:**

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) CHECK (price > 0),
  stock INTEGER CHECK (stock >= 0),
  status VARCHAR(20) CHECK (status IN ('active', 'inactive', 'archived'))
);

-- Test valid data
INSERT INTO products (name, price, stock, status) 
VALUES ('Product 1', 10.99, 100, 'active');  -- ✅ OK

-- Test invalid data
INSERT INTO products (name, price, stock, status) 
VALUES ('Product 2', -10.99, 100, 'active');  -- ❌ Error: price > 0
```

---

**Chúc mừng hoàn thành Day-091!** 🎉

**Chuẩn bị cho Day-092: Interview Pattern - Top N per Group** 🚀

