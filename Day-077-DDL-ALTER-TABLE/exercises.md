# Day-077: Bài Tập - DDL - ALTER TABLE

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: ALTER TABLE là gì?

**Câu hỏi:**
- ALTER TABLE là gì?
- Tại sao cần ALTER TABLE?
- Khi nào dùng ALTER TABLE trong production?
- Hậu quả nếu ALTER TABLE sai?

---

### Câu 1.2: ADD/DROP/MODIFY Column

**Câu hỏi:**
- ADD COLUMN là gì? Khi nào dùng?
- DROP COLUMN là gì? Cẩn thận gì?
- MODIFY COLUMN là gì? Khi nào dùng?
- Hậu quả nếu dùng sai mỗi operation?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Thêm Column với Constraints

**Yêu cầu:**
Có table `users` hiện tại:
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL,
  name VARCHAR(255)
);
```

**Yêu cầu:**
1. Thêm column `phone` VARCHAR(20) với UNIQUE constraint
2. Thêm column `status` VARCHAR(20) với DEFAULT 'active' và NOT NULL
3. Thêm column `age` INTEGER với CHECK (age >= 0)

**Lưu ý:** Table đã có 1000 rows. Làm thế nào để thêm columns an toàn?

---

### Câu 2.2: Modify Column

**Yêu cầu:**
1. Thay đổi `email` từ VARCHAR(255) thành VARCHAR(320) (để support email dài hơn)
2. Thêm NOT NULL cho `name` (table đã có data)
3. Thay đổi DEFAULT của `status` từ 'active' thành 'pending'

**Lưu ý:** Làm thế nào để modify an toàn?

---

### Câu 2.3: Add/Drop Constraints

**Yêu cầu:**
1. Thêm FOREIGN KEY constraint cho `orders.user_id` → `users.id`
2. Thêm CHECK constraint cho `products.price > 0`
3. Xóa UNIQUE constraint trên `users.email` (giả sử cần cho phép duplicate)

**Lưu ý:** Table đã có data. Làm thế nào để add constraints an toàn?

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Migrate Schema không Downtime

**Yêu cầu:**
Có table `products` với 10 triệu rows:
```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  description TEXT
);
```

**Yêu cầu:**
1. Thêm column `category_id` INTEGER với FOREIGN KEY đến `categories`
2. Thêm column `stock` INTEGER với DEFAULT 0 và NOT NULL
3. Thêm column `created_at` TIMESTAMP với DEFAULT CURRENT_TIMESTAMP

**Yêu cầu:**
- Không downtime
- Users vẫn có thể access table
- Plan migration step-by-step

---

### Câu 3.2: Refactor Column

**Yêu cầu:**
Có table `orders` với column `status` VARCHAR(20):
- Hiện tại: 'pending', 'confirmed', 'shipped', 'delivered'
- Cần thêm: 'cancelled', 'refunded'

**Yêu cầu:**
1. Thêm CHECK constraint để chỉ cho phép các giá trị trên
2. Thay đổi DEFAULT từ NULL thành 'pending'
3. Thêm NOT NULL constraint

**Lưu ý:** Table đã có data với status NULL. Làm thế nào để migrate?

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Rename Column

**Yêu cầu:**
1. Rename column `users.email` thành `users.email_address`
2. Rename table `users` thành `customers`

**Câu hỏi:**
- Làm thế nào để rename an toàn?
- Impact đến application code?
- Có cần migration script không?

---

### Câu 4.2: Change Data Type

**Yêu cầu:**
Có table `products` với column `price` DECIMAL(10, 2):
- Cần thay đổi thành DECIMAL(12, 2) để support giá trị lớn hơn

**Yêu cầu:**
1. Thay đổi data type an toàn
2. Đảm bảo không mất data
3. Test trên staging trước

**Câu hỏi:**
- Khi nào có thể change data type an toàn?
- Khi nào cần migration script?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

