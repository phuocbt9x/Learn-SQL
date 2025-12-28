# Day-079: Bài Tập - DML - INSERT

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: INSERT là gì?

**Câu hỏi:**
- INSERT là gì?
- Tại sao cần INSERT?
- Khi nào dùng INSERT trong production?
- Hậu quả nếu INSERT sai?

---

### Câu 1.2: INSERT Variants

**Câu hỏi:**
- INSERT single row vs multiple rows?
- INSERT ... ON CONFLICT là gì?
- INSERT ... RETURNING là gì?
- Khi nào dùng mỗi variant?

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: INSERT Single và Multiple Rows

**Yêu cầu:**
1. Tạo table `products`:
   - id SERIAL PRIMARY KEY
   - name VARCHAR(255) NOT NULL
   - price DECIMAL(10, 2) NOT NULL
   - stock INTEGER DEFAULT 0

2. Insert:
   - 1 row đơn lẻ
   - 5 rows cùng lúc
   - 100 rows từ SELECT

---

### Câu 2.2: INSERT ... ON CONFLICT (UPSERT)

**Yêu cầu:**
1. Tạo table `users` với email UNIQUE
2. Insert user với email 'test@example.com'
3. Insert lại cùng email với ON CONFLICT:
   - DO UPDATE (update name nếu exists)
   - DO NOTHING (ignore nếu exists)

**So sánh:**
- DO UPDATE vs DO NOTHING
- Khi nào dùng gì?

---

### Câu 2.3: INSERT ... RETURNING

**Yêu cầu:**
1. Insert user và return id
2. Insert product và return id, name, price
3. Insert multiple users và return tất cả ids

**So sánh:**
- INSERT + SELECT vs INSERT ... RETURNING
- Performance difference?

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Bulk Insert Optimization

**Yêu cầu:**
Cần insert 1 triệu rows vào table `logs`:
- timestamp TIMESTAMP
- level VARCHAR(20)
- message TEXT

**Yêu cầu:**
1. So sánh:
   - INSERT từng row
   - INSERT batch (1000 rows)
   - COPY command (nếu PostgreSQL)

2. Optimize:
   - Disable indexes tạm thời
   - Batch size tối ưu
   - Monitor performance

---

### Câu 3.2: Idempotent Insert

**Yêu cầu:**
Cần sync data từ external API vào table `products`:
- API trả về list products
- Cần insert nếu chưa có, update nếu đã có
- Phải idempotent (chạy nhiều lần không duplicate)

**Yêu cầu:**
1. Implement với INSERT ... ON CONFLICT
2. Test idempotency
3. Handle edge cases

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: INSERT với Subquery

**Yêu cầu:**
1. Insert users từ table `temp_users` vào `users`
2. Insert products với price từ table `pricing`
3. Insert orders với user_id và product_id từ joins

**Câu hỏi:**
- Khi nào dùng INSERT với subquery?
- Performance considerations?

---

### Câu 4.2: Conditional INSERT

**Yêu cầu:**
1. Insert user chỉ nếu email chưa tồn tại
2. Insert product với giá trị mặc định nếu thiếu
3. Insert order chỉ nếu user và product tồn tại

**Câu hỏi:**
- Làm thế nào implement conditional INSERT?
- Trade-offs với application logic?

---

**Sau khi hoàn thành, xem `solutions.md`!** 🚀

