# Day-001: Database là gì? RDBMS là gì?

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Database là gì và tại sao cần database
- RDBMS (Relational Database Management System) là gì
- Sự khác biệt giữa Database và File System
- Các loại database phổ biến
- Khi nào nên chọn loại database nào

---

## 1️⃣ DATABASE LÀ GÌ?

### **Nó là gì?**

**Database** (Cơ sở dữ liệu) là một hệ thống lưu trữ và quản lý dữ liệu có tổ chức, cho phép:
- **Lưu trữ** dữ liệu một cách có cấu trúc
- **Truy xuất** dữ liệu nhanh chóng và chính xác
- **Cập nhật** và **xóa** dữ liệu an toàn
- **Bảo vệ** dữ liệu khỏi mất mát và truy cập trái phép
- **Đảm bảo tính nhất quán** của dữ liệu

Database không chỉ là "nơi chứa dữ liệu" - nó là một **hệ thống quản lý** với nhiều tính năng phức tạp.

### **Tại sao tồn tại?**

Trước khi có database, người ta lưu dữ liệu trong:
- **File text** (`.txt`, `.csv`)
- **Excel spreadsheets**
- **File system** (thư mục và file)

**Vấn đề với cách lưu trữ cũ:**

1. **Khó tìm kiếm**: Muốn tìm một record cụ thể, phải đọc toàn bộ file
2. **Khó cập nhật**: Sửa một record có thể ảnh hưởng đến nhiều file khác
3. **Dữ liệu trùng lặp**: Cùng một thông tin lưu ở nhiều nơi
4. **Không đảm bảo tính nhất quán**: Dữ liệu ở file A và file B có thể không khớp
5. **Không có transaction**: Nếu lỗi giữa chừng, dữ liệu có thể bị corrupt
6. **Không có concurrent access**: Nhiều người cùng sửa → conflict
7. **Không có security**: Ai cũng có thể đọc/sửa file

**Database giải quyết tất cả các vấn đề trên.**

### **Khi nào dùng trong production?**

Bạn **PHẢI dùng database** khi:

✅ **Dữ liệu có cấu trúc** (users, orders, products, etc.)
✅ **Cần truy vấn phức tạp** (tìm kiếm, filter, aggregate)
✅ **Nhiều người cùng truy cập** (concurrent access)
✅ **Cần đảm bảo tính nhất quán** (consistency)
✅ **Cần transaction** (all-or-nothing operations)
✅ **Dữ liệu quan trọng** (không thể mất)
✅ **Cần bảo mật** (access control, encryption)

**KHÔNG nên dùng database** khi:

❌ Dữ liệu là **static files** (images, videos, documents) → dùng Object Storage
❌ Dữ liệu là **logs tạm thời** → có thể dùng file system
❌ Dữ liệu **không có cấu trúc** và chỉ cần lưu trữ đơn giản

### **Hậu quả nếu không dùng database?**

**Tình huống thực tế:**

Một startup nhỏ ban đầu lưu user data trong Excel file. Khi có 1000 users, họ vẫn dùng Excel. Khi có 10,000 users:

- ❌ Excel file quá lớn, mở mất 5 phút
- ❌ Không thể tìm user nhanh (phải scroll thủ công)
- ❌ Nhiều người cùng mở → file bị lock
- ❌ Không có backup tự động → mất dữ liệu khi máy tính hỏng
- ❌ Không có validation → nhập sai dữ liệu (email trùng, thiếu thông tin)
- ❌ Không thể query phức tạp (ví dụ: "tìm tất cả users đã mua hàng trong tháng này")

**Kết quả**: Phải migrate sang database, mất 2 tháng và nhiều bugs.

---

## 2️⃣ RDBMS LÀ GÌ?

### **Nó là gì?**

**RDBMS** (Relational Database Management System) là một loại database dựa trên **mô hình quan hệ** (Relational Model).

**Đặc điểm chính:**

1. **Dữ liệu được tổ chức thành bảng (Tables)**
   - Mỗi bảng có các cột (Columns) và hàng (Rows)
   - Ví dụ: Bảng `users` có cột `id`, `name`, `email`

2. **Các bảng có mối quan hệ với nhau**
   - Quan hệ 1-1, 1-nhiều, nhiều-nhiều
   - Ví dụ: Một `user` có nhiều `orders`

3. **Sử dụng SQL (Structured Query Language)**
   - Ngôn ngữ chuẩn để truy vấn và thao tác dữ liệu
   - Ví dụ: `SELECT * FROM users WHERE email = 'test@example.com'`

4. **Tuân thủ ACID properties**
   - **Atomicity**: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
   - **Consistency**: Dữ liệu luôn ở trạng thái hợp lệ
   - **Isolation**: Các transaction độc lập với nhau
   - **Durability**: Dữ liệu đã commit không bị mất

**Các RDBMS phổ biến:**
- **PostgreSQL** - Open source, mạnh mẽ, đầy đủ tính năng
- **MySQL** - Phổ biến nhất, dễ dùng
- **SQL Server** - Của Microsoft, mạnh cho enterprise
- **Oracle** - Enterprise-grade, đắt tiền
- **SQLite** - Nhẹ, embedded, dùng cho mobile apps

### **Tại sao tồn tại?**

RDBMS được thiết kế để giải quyết các vấn đề của database đơn giản:

1. **Tổ chức dữ liệu có cấu trúc**
   - Thay vì lưu mọi thứ trong một file lớn, chia thành nhiều bảng có quan hệ

2. **Tránh dữ liệu trùng lặp (Normalization)**
   - Thông tin user chỉ lưu một lần, các bảng khác reference đến

3. **Đảm bảo tính toàn vẹn dữ liệu (Data Integrity)**
   - Foreign Key đảm bảo không có "orphan records"
   - Constraints đảm bảo dữ liệu hợp lệ

4. **Truy vấn phức tạp**
   - JOIN nhiều bảng, aggregate, filter, sort - tất cả trong một câu SQL

5. **Concurrent access an toàn**
   - Nhiều users cùng đọc/ghi mà không conflict

### **Khi nào dùng trong production?**

RDBMS phù hợp khi:

✅ **Dữ liệu có cấu trúc rõ ràng** (structured data)
✅ **Có mối quan hệ giữa các entities** (users → orders → order_items)
✅ **Cần ACID transactions** (ví dụ: chuyển tiền, đặt hàng)
✅ **Cần truy vấn phức tạp** (JOIN, aggregate, subquery)
✅ **Cần đảm bảo tính nhất quán** (consistency)
✅ **Cần SQL** - ngôn ngữ chuẩn, dễ học, nhiều tools hỗ trợ

**KHÔNG nên dùng RDBMS** khi:

❌ **Dữ liệu không có cấu trúc** (JSON documents, logs) → dùng NoSQL (MongoDB)
❌ **Cần scale ngang (horizontal scaling)** dễ dàng → NoSQL
❌ **Dữ liệu là key-value đơn giản** → Redis, DynamoDB
❌ **Cần real-time analytics trên dữ liệu lớn** → Data warehouse (BigQuery, Snowflake)

### **Hậu quả nếu dùng sai loại database?**

**Tình huống 1: Dùng RDBMS cho use case không phù hợp**

Startup lưu **logs** (hàng triệu records mỗi ngày) vào PostgreSQL:
- ❌ Table quá lớn, query chậm
- ❌ Không cần JOIN, không cần transaction → lãng phí
- ❌ Khó scale → nên dùng Elasticsearch hoặc time-series DB

**Tình huống 2: Dùng NoSQL cho use case cần RDBMS**

E-commerce app dùng MongoDB để lưu orders:
- ❌ Không có Foreign Key → dữ liệu không nhất quán (order có user_id không tồn tại)
- ❌ Không có transaction → đặt hàng có thể bị mất một phần
- ❌ Query phức tạp khó viết → phải fetch nhiều lần

**Kết luận**: Chọn đúng loại database là quyết định quan trọng, ảnh hưởng lâu dài.

---

## 3️⃣ DATABASE VS FILE SYSTEM

### **So sánh chi tiết**

| Tiêu chí | File System | Database |
|----------|-------------|----------|
| **Tổ chức dữ liệu** | Thư mục và file | Tables với schema |
| **Tìm kiếm** | Phải đọc toàn bộ file | Index → tìm nhanh |
| **Cập nhật** | Ghi đè file | Update tại chỗ (in-place) |
| **Concurrent access** | Conflict, file lock | Transaction isolation |
| **Data integrity** | Không có | Constraints, Foreign Keys |
| **Backup** | Copy file thủ công | Automated backup |
| **Security** | File permissions cơ bản | User roles, encryption |
| **Query** | Không có | SQL - mạnh mẽ, linh hoạt |
| **Transaction** | Không có | ACID transactions |
| **Scalability** | Khó scale | Có nhiều options |

### **Ví dụ cụ thể**

**Tình huống: Lưu thông tin users**

**File System approach:**
```
/users/
  ├── user_1.txt
  ├── user_2.txt
  └── ...
```

Để tìm user có email "test@example.com":
- Phải đọc TẤT CẢ files
- Mất vài giây với 10,000 users
- Mất vài phút với 1 triệu users

**Database approach:**
```sql
SELECT * FROM users WHERE email = 'test@example.com';
```

Với index trên cột `email`:
- Tìm trong vài milliseconds
- Không phụ thuộc vào số lượng users

---

## 4️⃣ CÁC LOẠI DATABASE

### **1. RDBMS (Relational Database Management System)**

**Đặc điểm:**
- Dữ liệu trong tables
- Sử dụng SQL
- ACID transactions
- Schema rõ ràng

**Ví dụ:** PostgreSQL, MySQL, SQL Server, Oracle

**Dùng khi:** Dữ liệu có cấu trúc, cần ACID, cần JOIN

---

### **2. NoSQL Databases**

#### **2.1. Document Database**
- Lưu dữ liệu dạng JSON documents
- Schema linh hoạt (flexible schema)
- **Ví dụ:** MongoDB, CouchDB

**Dùng khi:** Dữ liệu không có cấu trúc cố định, cần scale ngang

#### **2.2. Key-Value Database**
- Lưu dữ liệu dạng key-value
- Rất nhanh cho read/write đơn giản
- **Ví dụ:** Redis, DynamoDB, Memcached

**Dùng khi:** Cache, session storage, real-time data

#### **2.3. Column-Family Database**
- Lưu dữ liệu theo cột thay vì hàng
- Tối ưu cho analytics
- **Ví dụ:** Cassandra, HBase

**Dùng khi:** Big data, time-series data, analytics

#### **2.4. Graph Database**
- Lưu dữ liệu dạng nodes và edges (đồ thị)
- Tối ưu cho queries về relationships
- **Ví dụ:** Neo4j, Amazon Neptune

**Dùng khi:** Social networks, recommendation systems, fraud detection

---

### **3. NewSQL Databases**

- Kết hợp ACID của RDBMS và scalability của NoSQL
- **Ví dụ:** CockroachDB, Google Spanner

**Dùng khi:** Cần cả ACID và scale ngang

---

### **4. Time-Series Databases**

- Tối ưu cho dữ liệu theo thời gian
- **Ví dụ:** InfluxDB, TimescaleDB

**Dùng khi:** IoT data, monitoring, metrics

---

## 5️⃣ PRODUCTION STORY: TẠI SAO STARTUP CHỌN POSTGRESQL THAY VÌ EXCEL?

### **Context**

Một startup fintech nhỏ, team 3 người, đang xây dựng app quản lý tài chính cá nhân.

**Ban đầu (Tháng 1-2):**
- Lưu user data trong **Google Sheets**
- Lưu transactions trong **CSV files**
- Mọi thứ "hoạt động" với 50 users

### **Vấn đề xuất hiện (Tháng 3-4)**

Khi có **500 users**:

1. **Google Sheets quá chậm**
   - Mở sheet mất 30 giây
   - Filter/search mất 10-20 giây
   - Nhiều người cùng mở → conflict

2. **CSV files không thể query**
   - Muốn tìm "tất cả transactions > $1000 trong tháng này"
   - Phải export CSV → mở Excel → filter thủ công
   - Mất 5-10 phút mỗi lần

3. **Dữ liệu không nhất quán**
   - User có thể xóa nhầm dòng trong Sheet
   - CSV files ở máy A và máy B khác nhau
   - Không biết version nào là đúng

4. **Không có backup tự động**
   - Một lần xóa nhầm → mất 100 transactions
   - Phải restore từ backup cũ → mất 2 ngày data

5. **Không có validation**
   - User nhập email sai format → không phát hiện
   - Transaction amount = -1000 (âm) → không có check

### **Quyết định: Migrate sang PostgreSQL**

**Lý do chọn PostgreSQL:**
- ✅ Open source, miễn phí
- ✅ Mạnh mẽ, đầy đủ tính năng
- ✅ Hỗ trợ tốt JSON (cần cho một số features)
- ✅ Community lớn, tài liệu tốt
- ✅ Có managed service (AWS RDS, Heroku Postgres)

### **Quá trình Migration (Tháng 5)**

1. **Thiết kế schema**
   - Bảng `users` (id, email, name, created_at)
   - Bảng `transactions` (id, user_id, amount, description, date)
   - Foreign Key: `transactions.user_id` → `users.id`

2. **Import data**
   - Export từ Google Sheets → CSV
   - Import vào PostgreSQL
   - Validate dữ liệu (tìm duplicates, invalid data)

3. **Viết queries**
   - Thay vì filter trong Excel, viết SQL:
   ```sql
   SELECT * FROM transactions 
   WHERE user_id = 123 
     AND amount > 1000 
     AND date >= '2024-01-01';
   ```
   - Từ 10 phút → 0.1 giây

### **Kết quả**

**Sau migration:**

✅ **Performance**
- Query nhanh hơn 100-1000x
- Có thể handle 10,000+ users dễ dàng

✅ **Data integrity**
- Foreign Key đảm bảo không có orphan transactions
- Constraints đảm bảo email đúng format, amount > 0

✅ **Concurrent access**
- Nhiều users cùng truy cập không conflict
- Transaction đảm bảo data consistency

✅ **Backup tự động**
- Daily backup tự động
- Point-in-time recovery nếu cần

✅ **Scalability**
- Dễ dàng scale lên khi có nhiều users hơn
- Có thể thêm read replicas nếu cần

### **Lesson Learned**

1. **Chọn đúng tool ngay từ đầu**
   - Excel/Sheets chỉ phù hợp cho prototype
   - Khi có > 100 records, nên dùng database

2. **Migration sớm tốt hơn muộn**
   - Migrate 500 records dễ hơn 50,000 records
   - Càng để lâu, càng khó migrate

3. **Database không chỉ là "lưu trữ"**
   - Nó là foundation của toàn bộ hệ thống
   - Chọn sai database → phải rebuild sau này

---

## 6️⃣ TÓM TẮT

### **Key Takeaways**

1. **Database** là hệ thống quản lý dữ liệu có tổ chức, không chỉ là nơi lưu trữ

2. **RDBMS** là loại database dùng mô hình quan hệ (tables), sử dụng SQL, đảm bảo ACID

3. **Database vs File System:**
   - Database: Tìm kiếm nhanh, có transaction, có integrity
   - File System: Đơn giản nhưng không phù hợp cho structured data

4. **Chọn đúng loại database:**
   - RDBMS: Structured data, cần ACID, cần JOIN
   - NoSQL: Unstructured data, cần scale ngang
   - Time-Series: Dữ liệu theo thời gian

5. **Production lesson:** Chọn database sớm, đừng dùng Excel/Sheets cho production data

### **Câu hỏi tự kiểm tra**

1. Database khác File System ở điểm nào?
2. Tại sao cần RDBMS thay vì lưu trong file?
3. Khi nào nên dùng RDBMS? Khi nào nên dùng NoSQL?
4. ACID là gì? Tại sao quan trọng?
5. Tại sao startup trong story chọn PostgreSQL?

---




**Chuẩn bị cho [Day-002: Table-Row-Column](../Day-002-Table-Row-Column/theory.md)** 🚀
