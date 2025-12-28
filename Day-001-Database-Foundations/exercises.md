# Day-001: Bài Tập - Database là gì? RDBMS là gì?

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Database và RDBMS. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Database là gì?

**Câu hỏi:** Hãy giải thích bằng 2-3 câu: Database là gì? Tại sao cần database thay vì lưu trong file text?

**Gợi ý:** Nghĩ về các vấn đề khi lưu dữ liệu trong file text (tìm kiếm, cập nhật, concurrent access).

---

### Câu 1.2: RDBMS vs NoSQL

**Câu hỏi:** Trong các tình huống sau, nên dùng RDBMS hay NoSQL? Giải thích tại sao.

a) **E-commerce website** lưu thông tin users, products, orders, order_items

b) **Social media app** lưu posts của users (mỗi post có format khác nhau, có thể có images, videos, links)

c) **Cache system** lưu session data (key-value đơn giản, cần rất nhanh)

d) **Analytics platform** lưu logs từ hàng triệu devices (dữ liệu theo thời gian, chỉ cần append, không cần update)

e) **Banking system** lưu account balances, transactions (cần đảm bảo tính chính xác tuyệt đối)

---

### Câu 1.3: ACID Properties

**Câu hỏi:** Giải thích ngắn gọn từng property trong ACID và cho ví dụ thực tế:

- **A**tomicity
- **C**onsistency  
- **I**solation
- **D**urability

**Ví dụ:** Atomicity - Khi chuyển tiền từ account A sang account B, nếu một bước fail thì cả transaction phải rollback.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH TÌNH HUỐNG

### Câu 2.1: Startup đang dùng Excel

**Tình huống:**

Một startup nhỏ đang lưu customer data trong Excel file:
- File có 5,000 rows
- 3 người cùng làm việc với file này
- Mỗi ngày có khoảng 100 customers mới

**Vấn đề hiện tại:**
- File mở chậm (mất 10-15 giây)
- Khi 2 người cùng mở, một người bị "read-only"
- Khó tìm customer cụ thể (phải scroll hoặc dùng Find, mất thời gian)
- Không có validation (có thể nhập email trùng, thiếu thông tin)

**Câu hỏi:**

a) Tại sao Excel không phù hợp cho tình huống này?

b) Nên migrate sang loại database nào? Tại sao?

c) Liệt kê 3 lợi ích chính khi migrate sang database.

---

### Câu 2.2: Chọn Database cho Use Case

**Tình huống A: Real-time Chat App**

- Cần lưu messages giữa users
- Messages có thể có text, images, files
- Cần real-time, scale lên hàng triệu users
- Không cần JOIN phức tạp
- Có thể mất một vài messages (không critical)

**Câu hỏi:** Nên chọn RDBMS hay NoSQL? Giải thích.

---

**Tình huống B: Accounting Software**

- Lưu invoices, payments, accounts
- Cần đảm bảo tính chính xác tuyệt đối (không được mất tiền)
- Cần query phức tạp (ví dụ: "tổng revenue theo tháng, theo category")
- Cần audit trail (biết ai sửa gì, khi nào)

**Câu hỏi:** Nên chọn RDBMS hay NoSQL? Giải thích.

---

## 🧠 BÀI TẬP 3: SO SÁNH VÀ PHÂN TÍCH

### Câu 3.1: Database vs File System

**Câu hỏi:** So sánh 2 cách lưu trữ dữ liệu users sau:

**Cách 1: File System**
```
/users/
  ├── user_1.json
  ├── user_2.json
  └── ...
```

Mỗi file chứa:
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Cách 2: Database (RDBMS)**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

**Yêu cầu:** So sánh 2 cách trên theo các tiêu chí:
- Tìm kiếm user theo email
- Cập nhật thông tin user
- Xóa user
- Đảm bảo email không trùng
- Nhiều người cùng truy cập
- Backup và restore

---

### Câu 3.2: RDBMS vs Document Database

**Câu hỏi:** Một app lưu thông tin blog posts. Mỗi post có:
- Title, content, author
- Tags (mảng)
- Comments (mảng các objects)
- Metadata (JSON object, có thể thay đổi)

**Option A: RDBMS (PostgreSQL)**
- Bảng `posts` (id, title, content, author_id)
- Bảng `tags` (id, name)
- Bảng `post_tags` (post_id, tag_id) - many-to-many
- Bảng `comments` (id, post_id, content, author_id)
- Metadata lưu trong cột JSONB

**Option B: Document Database (MongoDB)**
- Collection `posts`, mỗi document:
```json
{
  "_id": "...",
  "title": "...",
  "content": "...",
  "author": "...",
  "tags": ["tag1", "tag2"],
  "comments": [
    {"author": "...", "content": "..."}
  ],
  "metadata": {...}
}
```

**Yêu cầu:**
a) So sánh 2 cách trên về:
   - Độ phức tạp khi lưu một post mới
   - Độ phức tạp khi query "tất cả posts có tag X"
   - Độ phức tạp khi query "tất cả comments của user Y"
   - Khả năng thay đổi schema (thêm field mới)

b) Trong tình huống nào nên chọn Option A? Option B?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Trade-offs

**Câu hỏi:** Khi chọn database, luôn có trade-offs. Hãy phân tích trade-offs của:

a) **RDBMS (PostgreSQL)**
   - Ưu điểm?
   - Nhược điểm?
   - Khi nào nên dùng?

b) **NoSQL (MongoDB)**
   - Ưu điểm?
   - Nhược điểm?
   - Khi nào nên dùng?

---

### Câu 4.2: Production Decision

**Tình huống:**

Bạn là tech lead của một startup. Hiện tại app đang dùng **PostgreSQL** để lưu:
- Users
- Products  
- Orders
- Order items

CEO đề xuất: "Tại sao không chuyển sang MongoDB? Nó scale tốt hơn và flexible hơn."

**Câu hỏi:**

a) Bạn sẽ trả lời CEO như thế nào? (Giải thích tại sao nên giữ PostgreSQL)

b) Trong tình huống nào thì việc chuyển sang MongoDB là hợp lý?

c) Có thể dùng cả 2 không? (PostgreSQL cho một số use cases, MongoDB cho use cases khác)

---

### Câu 4.3: Migration Strategy

**Tình huống:**

Startup đang lưu data trong **Google Sheets** (10,000 rows). Muốn migrate sang **PostgreSQL**.

**Câu hỏi:**

a) Liệt kê các bước cần làm khi migrate (từ planning đến execution)

b) Những rủi ro gì có thể xảy ra khi migrate?

c) Làm thế nào để đảm bảo không mất dữ liệu?

d) Làm thế nào để đảm bảo app vẫn hoạt động trong quá trình migrate? (zero downtime)

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Database là gì? 3 lý do chính tại sao cần database.

2. RDBMS là gì? 3 đặc điểm chính của RDBMS.

3. ACID là gì? Giải thích từng chữ cái.

4. Khi nào nên dùng RDBMS? Khi nào nên dùng NoSQL?

5. Sự khác biệt chính giữa Database và File System là gì?

---

### Câu 5.2: Áp dụng thực tế

Tưởng tượng bạn đang xây dựng một **task management app** (như Trello):

- Users có thể tạo boards
- Mỗi board có nhiều lists
- Mỗi list có nhiều cards
- Mỗi card có comments, attachments, due dates

**Câu hỏi:**

a) Bạn sẽ chọn loại database nào? Tại sao?

b) Thiết kế high-level schema (chỉ cần liệt kê các bảng chính, chưa cần chi tiết columns)

c) Nếu sau này cần thêm tính năng "real-time collaboration" (nhiều người cùng edit), database choice có thay đổi không?

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Polyglot Persistence

**Câu hỏi:** "Polyglot Persistence" là gì? Cho ví dụ một hệ thống sử dụng nhiều loại database khác nhau, giải thích tại sao cần nhiều loại.

**Gợi ý:** Nghĩ về một hệ thống lớn như e-commerce platform (users, products, orders, cache, search, analytics).

---

### Câu A.2: CAP Theorem

**Câu hỏi:** CAP Theorem là gì? Giải thích ngắn gọn:
- **C**onsistency
- **A**vailability  
- **P**artition tolerance

RDBMS thường chọn gì? NoSQL thường chọn gì?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Không có đáp án "đúng tuyệt đối" - quan trọng là lý luận
- Senior SQL Engineer không chỉ biết syntax, mà còn hiểu trade-offs và biết khi nào dùng gì

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

