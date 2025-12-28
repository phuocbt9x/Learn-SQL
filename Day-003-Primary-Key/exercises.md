# Day-003: Bài Tập - Primary Key

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Primary Key. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Primary Key là gì?

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Primary Key là gì?
- Tại sao cần Primary Key?
- Đặc điểm của Primary Key (4 đặc điểm chính)?

---

### Câu 1.2: Single Key vs Composite Key

**Câu hỏi:** Trong các tình huống sau, nên dùng Single Key hay Composite Key? Giải thích tại sao.

a) Table `users` với column `id` (auto-increment)

b) Table `order_items` (một order có nhiều products, một product có trong nhiều orders)

c) Table `enrollments` (students enroll courses, một student chỉ enroll một course một lần mỗi semester)

d) Table `products` với column `id` (UUID)

---

### Câu 1.3: Auto-increment vs UUID vs Natural Key

**Câu hỏi:** Trong các tình huống sau, nên dùng loại Primary Key nào? Giải thích tại sao.

a) **Single database**, table `users`, cần performance tốt

b) **Distributed system** (nhiều servers), table `events`, cần security (không muốn expose sequential IDs)

c) **Table `citizens`** với SSN (Social Security Number) - đảm bảo unique và không bao giờ đổi

d) **Table `orders`** trong single database, cần query theo thứ tự (newest first)

e) **Microservices architecture**, mỗi service tự generate ID

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Table không có Primary Key

**Tình huống:**

Developer tạo table `products` như sau:

```sql
CREATE TABLE products (
  name VARCHAR(200),
  price DECIMAL(10, 2),
  category VARCHAR(100)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với table này.

b) Viết lại CREATE TABLE với Primary Key phù hợp.

c) Giải thích tại sao chọn Primary Key đó.

---

### Câu 2.2: Chọn sai Primary Key

**Tình huống:**

Developer tạo table `users` với email làm Primary Key:

```sql
CREATE TABLE users (
  email VARCHAR(100) PRIMARY KEY,
  name VARCHAR(100),
  phone VARCHAR(20)
);
```

**Câu hỏi:**

a) Phân tích các vấn đề với cách này.

b) Viết lại CREATE TABLE tốt hơn.

c) Nếu vẫn muốn email unique, làm thế nào?

---

### Câu 2.3: Composite Key vs Single Key

**Tình huống:**

Table `order_items` có 2 cách thiết kế:

**Option A: Composite Key**
```sql
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);
```

**Option B: Single Key**
```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT,
  quantity INT,
  UNIQUE (order_id, product_id)
);
```

**Câu hỏi:**

a) So sánh 2 cách trên về:
   - Độ phức tạp
   - Foreign Key reference
   - Storage
   - Performance

b) Bạn sẽ chọn cách nào? Tại sao?

c) Trong tình huống nào nên dùng Option A? Option B?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ SCHEMA

### Câu 3.1: Thiết kế Primary Key cho E-commerce

**Yêu cầu:** Thiết kế tables cho hệ thống e-commerce:

- `users`: Users của hệ thống
- `products`: Sản phẩm
- `orders`: Đơn hàng
- `order_items`: Chi tiết đơn hàng (một order có nhiều products)

**Câu hỏi:**

a) Thiết kế Primary Key cho mỗi table.

b) Giải thích tại sao chọn mỗi Primary Key.

c) Nếu hệ thống là distributed (nhiều servers), Primary Key có thay đổi không?

---

### Câu 3.2: Thiết kế Primary Key cho Blog System

**Yêu cầu:** Thiết kế tables:

- `posts`: Blog posts
- `tags`: Tags
- `post_tags`: Relationship giữa posts và tags (many-to-many)

**Câu hỏi:**

a) Thiết kế Primary Key cho mỗi table.

b) Table `post_tags` nên dùng Single Key hay Composite Key? Tại sao?

c) Nếu muốn lưu thêm thông tin trong `post_tags` (ví dụ: `added_at`), có ảnh hưởng đến Primary Key không?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Auto-increment vs UUID - Trade-offs

**Tình huống:**

Bạn đang thiết kế table `users` cho một startup. Có 2 options:

**Option A: Auto-increment INT**
```sql
id INT AUTO_INCREMENT PRIMARY KEY
```

**Option B: UUID**
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
```

**Câu hỏi:**

a) Phân tích trade-offs của mỗi option về:
   - Storage
   - Performance
   - Security
   - Scalability
   - Distributed systems

b) Trong các tình huống sau, chọn option nào?
   - Single database, performance quan trọng
   - Distributed system, nhiều servers
   - Cần security (không muốn expose sequential IDs)
   - Cần merge data từ nhiều databases

c) Có thể dùng cả 2 không? (Ví dụ: `id` INT + `uuid` UUID)

---

### Câu 4.2: Natural Key vs Surrogate Key

**Tình huống:**

Table `countries` có 2 cách thiết kế:

**Option A: Natural Key**
```sql
CREATE TABLE countries (
  country_code CHAR(2) PRIMARY KEY,  -- "US", "VN", "JP"
  name VARCHAR(100)
);
```

**Option B: Surrogate Key**
```sql
CREATE TABLE countries (
  id INT PRIMARY KEY,
  country_code CHAR(2) UNIQUE,
  name VARCHAR(100)
);
```

**Câu hỏi:**

a) So sánh 2 cách trên về:
   - Storage
   - Foreign Key reference
   - Performance
   - Flexibility (có thể đổi country_code không?)

b) Bạn sẽ chọn cách nào? Tại sao?

c) Trong tình huống nào nên dùng Natural Key? Surrogate Key?

---

### Câu 4.3: Primary Key và Performance

**Câu hỏi:**

a) Tại sao Primary Key tự động có index?

b) Index trên Primary Key là gì? (Clustered vs Non-clustered)

c) Query `WHERE id = 1` nhanh hơn `WHERE name = 'John'` (không có index) như thế nào?

d) Nếu Primary Key là UUID (string), performance có khác với INT không?

---

### Câu 4.4: Primary Key và Foreign Key

**Tình huống:**

Table `users` có Primary Key `id INT`.

Table `orders` cần reference đến `users`.

**Câu hỏi:**

a) Làm thế nào để `orders` reference đến `users`?

b) Nếu `users.id` thay đổi (ví dụ: từ 1 → 999), điều gì xảy ra với `orders`?

c) Tại sao Primary Key không nên thay đổi?

d) Nếu bắt buộc phải thay đổi Primary Key, làm thế nào?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Tạo Tables với Primary Key

**Yêu cầu:** Viết CREATE TABLE cho:

a) `users` với auto-increment ID

b) `products` với UUID

c) `order_items` với composite key (order_id, product_id)

d) `user_roles` với composite key (user_id, role_id)

---

### Câu 5.2: Xử lý Duplicate Key

**Tình huống:**

Table `orders` có `order_number` cần unique:

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  order_number VARCHAR(20),
  total_amount DECIMAL(10, 2)
);
```

**Câu hỏi:**

a) Làm thế nào để đảm bảo `order_number` unique?

b) Nếu có 2 requests cùng lúc tạo order, làm thế nào để tránh duplicate?

c) Viết code (pseudo-code) generate `order_number` thread-safe.

---

### Câu 5.3: Migrate từ Natural Key sang Surrogate Key

**Tình huống:**

Table `users` hiện tại dùng email làm Primary Key:

```sql
CREATE TABLE users (
  email VARCHAR(100) PRIMARY KEY,
  name VARCHAR(100)
);
```

Muốn migrate sang dùng ID làm Primary Key.

**Câu hỏi:**

a) Làm thế nào để migrate mà không mất dữ liệu?

b) Nếu có Foreign Keys reference đến `users.email`, phải làm gì?

c) Viết migration script (pseudo-code).

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Primary Key là gì? Tại sao cần Primary Key?

2. Single Key vs Composite Key - khi nào dùng gì?

3. Auto-increment vs UUID - trade-offs?

4. Tại sao không nên dùng Natural Key làm Primary Key?

5. Primary Key có thể thay đổi không? Tại sao?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế database cho **hệ thống quản lý dự án**:

- `projects`: Dự án
- `users`: Users
- `project_members`: Members của dự án (many-to-many)
- `tasks`: Tasks trong dự án
- `task_assignments`: Assignment của tasks cho users

**Yêu cầu:**

a) Thiết kế Primary Key cho mỗi table.

b) Giải thích tại sao chọn mỗi Primary Key.

c) Nếu hệ thống là distributed (nhiều servers), có cần thay đổi không?

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Sequence vs Auto-increment

**Câu hỏi:**

a) Sequence là gì? Khác với Auto-increment như thế nào?

b) Khi nào nên dùng Sequence? Khi nào nên dùng Auto-increment?

c) Sequence có ưu điểm gì so với Auto-increment?

---

### Câu A.2: UUID v4 vs UUID v1

**Câu hỏi:**

a) UUID v4 và UUID v1 khác nhau như thế nào?

b) Khi nào nên dùng UUID v4? Khi nào nên dùng UUID v1?

c) UUID v1 có thể sort theo creation time không?

---

### Câu A.3: Primary Key và Partitioning

**Câu hỏi:**

a) Primary Key ảnh hưởng đến partitioning như thế nào?

b) Nếu table được partition theo `created_at`, Primary Key nên là gì?

c) Composite Key có thể dùng cho partitioning không?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer hiểu trade-offs và biết khi nào dùng gì

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

