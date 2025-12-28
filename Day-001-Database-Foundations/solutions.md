# Day-001: Solutions - Database là gì? RDBMS là gì?

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Database là gì?

**Đáp án mẫu:**

Database là hệ thống quản lý dữ liệu có tổ chức, cho phép lưu trữ, truy xuất, cập nhật và bảo vệ dữ liệu một cách hiệu quả và an toàn.

**Tại sao cần database thay vì file text:**

1. **Tìm kiếm nhanh**: Database có index, tìm kiếm trong milliseconds. File text phải đọc toàn bộ, mất vài giây đến vài phút.

2. **Concurrent access**: Nhiều người cùng truy cập database an toàn nhờ transaction isolation. File text dễ bị conflict, corrupt.

3. **Data integrity**: Database có constraints (Foreign Key, Unique, Check) đảm bảo dữ liệu hợp lệ. File text không có validation.

4. **Transaction**: Database đảm bảo ACID - nếu một bước fail, toàn bộ rollback. File text không có cơ chế này.

5. **Query phức tạp**: Database có SQL để JOIN, aggregate, filter. File text phải tự code logic.

**Lưu ý production:**
- File text chỉ phù hợp cho dữ liệu tĩnh, nhỏ, không cần query
- Khi có > 100 records hoặc cần query → nên dùng database

---

### Câu 1.2: RDBMS vs NoSQL

**a) E-commerce website**

**Đáp án: RDBMS** (PostgreSQL, MySQL)

**Lý do:**
- ✅ Dữ liệu có cấu trúc rõ ràng (users, products, orders)
- ✅ Có mối quan hệ (user → orders → order_items)
- ✅ Cần ACID transactions (đặt hàng phải atomic: tạo order + trừ inventory + tạo payment)
- ✅ Cần query phức tạp (JOIN để lấy order details, aggregate để tính revenue)
- ✅ Cần data integrity (Foreign Key đảm bảo order phải có user hợp lệ)

**b) Social media app**

**Đáp án: NoSQL (Document DB)** hoặc **RDBMS + JSON column**

**Lý do:**
- Posts có format khác nhau (text post, image post, video post) → schema linh hoạt
- Cần scale ngang (hàng triệu posts)
- Không cần JOIN phức tạp (mỗi post là document độc lập)

**Trade-off:**
- NoSQL: Flexible, scale tốt, nhưng khó query phức tạp (ví dụ: "tất cả posts của user X có tag Y")
- RDBMS: Query mạnh, nhưng schema cứng nhắc

**c) Cache system**

**Đáp án: NoSQL (Key-Value DB)** - Redis, Memcached

**Lý do:**
- ✅ Dữ liệu đơn giản (key-value)
- ✅ Cần rất nhanh (in-memory)
- ✅ Không cần ACID (có thể mất data, sẽ reload)
- ✅ Không cần query phức tạp

**d) Analytics platform (logs)**

**Đáp án: Time-Series DB** (InfluxDB, TimescaleDB) hoặc **NoSQL** (Cassandra)

**Lý do:**
- ✅ Dữ liệu theo thời gian, chỉ append (ít update/delete)
- ✅ Volume rất lớn (hàng triệu records/ngày)
- ✅ Cần scale ngang
- ✅ Query chủ yếu là time-range queries

**e) Banking system**

**Đáp án: RDBMS** (PostgreSQL, Oracle, SQL Server)

**Lý do:**
- ✅ **Cần ACID tuyệt đối**: Chuyển tiền phải atomic, không được mất tiền
- ✅ **Cần data integrity**: Account balance phải chính xác, không được âm (trừ khi có overdraft)
- ✅ **Cần audit trail**: Biết mọi thay đổi (who, when, what)
- ✅ **Cần query phức tạp**: Báo cáo tài chính, reconciliation

**KHÔNG nên dùng NoSQL** vì:
- ❌ NoSQL thường không đảm bảo ACID mạnh (eventual consistency)
- ❌ Khó đảm bảo data integrity (không có Foreign Key, constraints)

---

### Câu 1.3: ACID Properties

**A - Atomicity (Tính nguyên tử)**

**Giải thích:** Transaction là một đơn vị công việc không thể chia nhỏ. Hoặc tất cả các bước trong transaction thành công, hoặc tất cả đều rollback.

**Ví dụ:** Chuyển tiền từ account A ($1000) sang account B:
- Bước 1: Trừ $1000 từ A
- Bước 2: Cộng $1000 vào B

Nếu bước 2 fail (ví dụ: database crash), bước 1 phải rollback. Không được để A mất $1000 mà B không nhận.

**Hậu quả nếu không có Atomicity:** Dữ liệu không nhất quán, mất tiền, mất dữ liệu.

---

**C - Consistency (Tính nhất quán)**

**Giải thích:** Dữ liệu luôn ở trạng thái hợp lệ. Sau mỗi transaction, database phải tuân thủ tất cả constraints (Foreign Key, Check, Unique, etc.).

**Ví dụ:** 
- Constraint: `account.balance >= 0` (số dư không được âm)
- Transaction cố gắng trừ $1500 từ account có $1000
- Transaction phải fail (rollback) vì vi phạm constraint

**Hậu quả nếu không có Consistency:** Dữ liệu không hợp lệ (số dư âm, email trùng, Foreign Key không tồn tại).

---

**I - Isolation (Tính cô lập)**

**Giải thích:** Các transaction chạy đồng thời không ảnh hưởng lẫn nhau. Mỗi transaction thấy dữ liệu như thể chỉ có mình đang chạy.

**Ví dụ:**
- Transaction A đọc balance của account X = $1000
- Transaction B cập nhật balance của account X = $1500 và commit
- Transaction A đọc lại balance của account X = vẫn $1000 (hoặc $1500, tùy isolation level)

**Hậu quả nếu không có Isolation:** 
- Dirty Read: Đọc dữ liệu chưa commit
- Non-repeatable Read: Đọc 2 lần được 2 kết quả khác nhau
- Phantom Read: Thấy records mới xuất hiện

---

**D - Durability (Tính bền vững)**

**Giải thích:** Dữ liệu đã commit không bị mất, kể cả khi database crash hoặc power loss.

**Ví dụ:**
- Transaction commit thành công
- Ngay sau đó, database server crash
- Khi restart, dữ liệu vẫn còn (đã được ghi vào disk)

**Cơ chế:** Write-Ahead Logging (WAL) - ghi log trước khi commit, đảm bảo có thể recover.

**Hậu quả nếu không có Durability:** Mất dữ liệu sau khi commit, phải làm lại công việc.

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH TÌNH HUỐNG

### Câu 2.1: Startup đang dùng Excel

**a) Tại sao Excel không phù hợp?**

1. **Performance**: File lớn (5,000 rows) mở chậm, filter/search chậm
2. **Concurrent access**: Chỉ 1 người có thể edit, người khác chỉ đọc được
3. **Không có query language**: Khó tìm kiếm phức tạp (ví dụ: "tất cả customers ở thành phố X, đã mua hàng trong tháng này")
4. **Không có validation**: Có thể nhập dữ liệu sai (email trùng, thiếu thông tin)
5. **Không có transaction**: Nếu lỗi giữa chừng, dữ liệu có thể corrupt
6. **Khó scale**: Khi có 50,000 rows, Excel sẽ rất chậm hoặc không mở được
7. **Không có backup tự động**: Phải backup thủ công
8. **Không có access control**: Ai có file thì có thể sửa

**b) Nên migrate sang loại database nào?**

**Đáp án: RDBMS** (PostgreSQL hoặc MySQL)

**Lý do:**
- Customer data có cấu trúc rõ ràng (name, email, address, etc.)
- Có thể cần query phức tạp sau này (JOIN với orders, products)
- Cần ACID transactions (khi cập nhật customer, đảm bảo không mất dữ liệu)
- Cần data integrity (email không trùng, đảm bảo format đúng)
- Team quen với SQL (dễ hire, nhiều tools hỗ trợ)

**KHÔNG nên dùng NoSQL** vì:
- Customer data có cấu trúc cố định
- Có thể cần JOIN với các bảng khác sau này
- Cần ACID cho các operations quan trọng

**c) 3 lợi ích chính khi migrate:**

1. **Performance**: Query nhanh hơn 100-1000x nhờ index. Tìm customer theo email từ vài giây → vài milliseconds.

2. **Concurrent access**: Nhiều người cùng làm việc không conflict. Database có transaction isolation đảm bảo an toàn.

3. **Data integrity & Validation**: 
   - Unique constraint đảm bảo email không trùng
   - Check constraint đảm bảo dữ liệu hợp lệ (ví dụ: phone number đúng format)
   - Foreign Key đảm bảo relationships đúng (customer phải có address hợp lệ)

**Bonus lợi ích:**
- Backup tự động
- Access control (user roles, permissions)
- Audit trail (biết ai sửa gì, khi nào)
- Scalability (có thể handle hàng triệu customers)

---

### Câu 2.2: Chọn Database cho Use Case

**Tình huống A: Real-time Chat App**

**Đáp án: NoSQL (Document DB)** - MongoDB, hoặc **NoSQL (Key-Value)** - Redis cho real-time

**Lý do:**
- ✅ Messages có format khác nhau (text, image, file) → schema linh hoạt
- ✅ Cần scale ngang (hàng triệu users, hàng tỷ messages)
- ✅ Không cần JOIN phức tạp (mỗi message là document độc lập)
- ✅ Có thể mất một vài messages (không critical) → không cần ACID mạnh
- ✅ Cần real-time → có thể dùng Redis pub/sub

**Trade-off:**
- NoSQL: Scale tốt, flexible, nhưng khó query phức tạp (ví dụ: "tất cả messages của user X trong room Y có chứa keyword Z")
- Có thể dùng hybrid: MongoDB cho lưu trữ lâu dài, Redis cho real-time

---

**Tình huống B: Accounting Software**

**Đáp án: RDBMS** (PostgreSQL, SQL Server, Oracle)

**Lý do:**
- ✅ **Cần ACID tuyệt đối**: Không được mất tiền, không được sai số
- ✅ **Cần data integrity**: 
   - Invoice total = sum of line items
   - Account balance phải chính xác
   - Không được có orphan records
- ✅ **Cần query phức tạp**: 
   - "Tổng revenue theo tháng, theo category"
   - "So sánh revenue năm này vs năm trước"
   - JOIN nhiều bảng (invoices → line_items → products → categories)
- ✅ **Cần audit trail**: Biết mọi thay đổi (who, when, what) → có thể dùng triggers

**KHÔNG nên dùng NoSQL** vì:
- ❌ Không đảm bảo ACID mạnh (eventual consistency không đủ cho accounting)
- ❌ Khó đảm bảo data integrity (không có Foreign Key, constraints)
- ❌ Khó query phức tạp (JOIN, aggregate)

---

## 🧠 BÀI TẬP 3: SO SÁNH VÀ PHÂN TÍCH

### Câu 3.1: Database vs File System

**So sánh chi tiết:**

| Tiêu chí | File System | Database |
|----------|-------------|----------|
| **Tìm kiếm user theo email** | Phải đọc TẤT CẢ files, so sánh từng file → O(n), mất vài giây đến vài phút | Index trên cột email → O(log n), vài milliseconds |
| **Cập nhật thông tin user** | Phải tìm file → đọc → sửa → ghi lại → có thể corrupt nếu lỗi giữa chừng | UPDATE với WHERE → atomic, có transaction |
| **Xóa user** | Xóa file → không có rollback, không có cascade | DELETE với transaction → có thể rollback, có cascade options |
| **Đảm bảo email không trùng** | Phải tự code logic check → dễ có race condition | UNIQUE constraint → database tự đảm bảo |
| **Nhiều người cùng truy cập** | Conflict, file lock → chỉ 1 người edit được | Transaction isolation → nhiều người cùng làm việc an toàn |
| **Backup và restore** | Copy files thủ công → dễ quên, không có point-in-time recovery | Automated backup → có thể restore về bất kỳ thời điểm nào |

**Kết luận:** Database vượt trội ở mọi khía cạnh cho structured data.

---

### Câu 3.2: RDBMS vs Document Database

**a) So sánh:**

| Tiêu chí | RDBMS (Option A) | Document DB (Option B) |
|----------|------------------|------------------------|
| **Lưu một post mới** | Phải INSERT vào 3-4 bảng (posts, post_tags, comments) → cần transaction | INSERT 1 document → đơn giản hơn |
| **Query "tất cả posts có tag X"** | JOIN posts → post_tags → tags → nhanh nhờ index | Query trên array field → có thể chậm nếu không có index phù hợp |
| **Query "tất cả comments của user Y"** | JOIN comments → users → nhanh | Phải scan tất cả posts, tìm trong comments array → chậm |
| **Thay đổi schema (thêm field)** | ALTER TABLE → có thể lock table, chậm | Chỉ cần thêm field vào document → linh hoạt |

**b) Khi nào chọn Option A (RDBMS)?**

✅ Khi cần query phức tạp:
- "Tất cả posts có tag X và có comment từ user Y"
- "Top 10 users có nhiều comments nhất"
- JOIN, aggregate phức tạp

✅ Khi cần data integrity:
- Đảm bảo tag phải tồn tại trong bảng tags
- Đảm bảo comment phải có author hợp lệ

✅ Khi cần ACID transactions:
- Tạo post + tags + comments phải atomic

✅ Khi cần normalize (tránh duplicate):
- Tag "SQL" chỉ lưu 1 lần, nhiều posts reference đến

**Khi nào chọn Option B (Document DB)?**

✅ Khi posts là documents độc lập:
- Ít cần JOIN
- Mỗi post là self-contained

✅ Khi cần schema linh hoạt:
- Posts có format khác nhau
- Thường xuyên thêm field mới

✅ Khi cần scale ngang:
- Hàng triệu posts
- Write-heavy workload

✅ Khi không cần ACID mạnh:
- Có thể mất một vài posts (không critical)

**Hybrid approach (Senior thinking):**

Có thể dùng cả 2:
- **RDBMS** cho structured data (users, tags) và queries phức tạp
- **Document DB** cho posts (lưu post_id trong RDBMS, full content trong Document DB)
- **Search engine** (Elasticsearch) cho full-text search

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Trade-offs

**a) RDBMS (PostgreSQL)**

**Ưu điểm:**
- ✅ ACID transactions → đảm bảo data consistency
- ✅ SQL mạnh mẽ → query phức tạp dễ viết
- ✅ Data integrity → Foreign Key, constraints
- ✅ Mature, stable → nhiều tools, community lớn
- ✅ JOIN, aggregate mạnh → phù hợp cho analytics

**Nhược điểm:**
- ❌ Schema cứng nhắc → khó thay đổi
- ❌ Scale ngang khó → thường scale dọc (vertical scaling)
- ❌ JOIN nhiều bảng có thể chậm
- ❌ Overhead cho simple operations

**Khi nào nên dùng:**
- Structured data
- Cần ACID
- Cần query phức tạp
- Cần data integrity

---

**b) NoSQL (MongoDB)**

**Ưu điểm:**
- ✅ Schema linh hoạt → dễ thay đổi
- ✅ Scale ngang dễ → sharding
- ✅ Write performance tốt → phù hợp write-heavy
- ✅ Document model phù hợp với application objects

**Nhược điểm:**
- ❌ Không có ACID mạnh → eventual consistency
- ❌ Không có Foreign Key → phải tự đảm bảo data integrity
- ❌ Query phức tạp khó → không có JOIN mạnh
- ❌ Có thể duplicate data → không normalize

**Khi nào nên dùng:**
- Unstructured/semi-structured data
- Cần scale ngang
- Schema thay đổi thường xuyên
- Không cần ACID mạnh

---

### Câu 4.2: Production Decision

**a) Trả lời CEO:**

"PostgreSQL phù hợp với use case hiện tại vì:

1. **Dữ liệu có cấu trúc rõ ràng**: Users, products, orders có relationships rõ ràng → RDBMS phù hợp.

2. **Cần ACID transactions**: Khi đặt hàng, phải:
   - Tạo order
   - Trừ inventory
   - Tạo payment record
   → Tất cả phải atomic. MongoDB không đảm bảo ACID mạnh.

3. **Cần query phức tạp**: 
   - 'Tổng revenue theo tháng, theo category'
   - 'Top 10 products bán chạy nhất'
   → SQL với JOIN, aggregate mạnh hơn MongoDB query.

4. **Data integrity**: Foreign Key đảm bảo order phải có user hợp lệ, order_item phải có product hợp lệ.

5. **Team đã quen SQL**: Migrate sang MongoDB cần học lại, tốn thời gian.

**Khi nào nên xem xét MongoDB:**
- Khi cần scale ngang (hàng trăm triệu orders)
- Khi có use case mới cần schema linh hoạt (ví dụ: user preferences là JSON động)
- Khi có write-heavy workload mà không cần ACID mạnh

**Recommendation:** Giữ PostgreSQL cho core business logic, có thể thêm MongoDB cho use cases cụ thể (ví dụ: product catalog cache)."

---

**b) Khi nào chuyển sang MongoDB hợp lý?**

✅ Khi scale ngang là ưu tiên:
- Hàng trăm triệu orders
- Write-heavy (hàng triệu writes/giây)
- PostgreSQL không scale được nữa

✅ Khi có use case mới cần schema linh hoạt:
- User-generated content (posts, comments có format khác nhau)
- Product attributes động (mỗi category có attributes khác)

✅ Khi không cần ACID mạnh:
- Analytics data (có thể mất một vài records)
- Cache data
- Logs

**KHÔNG nên chuyển** nếu:
- ❌ Core business logic cần ACID (orders, payments)
- ❌ Cần query phức tạp (JOIN, aggregate)
- ❌ Team không có experience với MongoDB

---

**c) Có thể dùng cả 2 không?**

**Đáp án: CÓ - Polyglot Persistence**

**Ví dụ architecture:**

```
┌─────────────────┐
│   Application   │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼────┐
│PostgreSQL│ │MongoDB│
│(Core DB) │ │(Docs) │
└─────────┘ └───────┘
    │
┌───▼────┐
│  Redis │
│ (Cache)│
└────────┘
```

**PostgreSQL cho:**
- Users, orders, payments (ACID, integrity)
- Analytics queries (JOIN, aggregate)

**MongoDB cho:**
- Product catalog (schema linh hoạt)
- User-generated content (posts, reviews)

**Redis cho:**
- Session storage
- Cache (hot products, user sessions)

**Lưu ý:**
- Phải sync data giữa các databases (ví dụ: user update trong PostgreSQL → update cache trong Redis)
- Phức tạp hơn, nhưng linh hoạt hơn

---

### Câu 4.3: Migration Strategy

**a) Các bước migrate:**

1. **Planning:**
   - Thiết kế schema (tables, columns, constraints)
   - Map Google Sheets columns → database columns
   - Identify data quality issues (duplicates, invalid data)

2. **Setup:**
   - Setup PostgreSQL database
   - Tạo tables với schema
   - Tạo indexes (nếu cần)

3. **Data Export:**
   - Export Google Sheets → CSV
   - Validate CSV (check format, encoding)

4. **Data Import:**
   - Import CSV vào PostgreSQL (có thể dùng `COPY` command)
   - Validate imported data (row count, sample records)

5. **Data Validation:**
   - So sánh data giữa Sheets và database
   - Check constraints (unique, foreign key)
   - Fix data quality issues

6. **Application Update:**
   - Update application code để dùng database thay vì Sheets
   - Test thoroughly

7. **Cutover:**
   - Deploy application mới
   - Monitor for issues
   - Keep Sheets as backup trong vài ngày

8. **Cleanup:**
   - Archive Google Sheets
   - Document migration process

---

**b) Rủi ro:**

1. **Mất dữ liệu:**
   - Export/import lỗi
   - Data corruption
   - **Mitigation:** Backup trước, validate sau import

2. **Downtime:**
   - Application không hoạt động trong quá trình migrate
   - **Mitigation:** Migrate vào off-peak hours, có rollback plan

3. **Data quality issues:**
   - Duplicates, invalid data
   - **Mitigation:** Clean data trước khi import

4. **Performance issues:**
   - Database chậm hơn expected
   - **Mitigation:** Test với production-like data, optimize queries

5. **Application bugs:**
   - Code mới có bugs
   - **Mitigation:** Thorough testing, gradual rollout

---

**c) Đảm bảo không mất dữ liệu:**

1. **Backup trước khi migrate:**
   - Export Google Sheets → CSV, lưu nhiều nơi
   - Snapshot database sau import

2. **Validate data:**
   - So sánh row count
   - So sánh sample records
   - Check constraints

3. **Test import process:**
   - Test trên staging environment trước
   - Test với subset data trước

4. **Keep backup:**
   - Giữ Google Sheets làm backup trong vài ngày/ tuần
   - Có thể rollback nếu cần

---

**d) Zero downtime migration:**

**Strategy: Dual-write (Ghi kép):**

1. **Phase 1: Dual-write**
   - Application ghi vào CẢ Google Sheets VÀ PostgreSQL
   - Đọc vẫn từ Google Sheets
   - Kéo dài vài ngày để đảm bảo data sync

2. **Phase 2: Backfill**
   - Import historical data từ Sheets vào PostgreSQL
   - Validate data

3. **Phase 3: Switch read**
   - Application đọc từ PostgreSQL
   - Vẫn ghi vào cả 2 (dual-write)

4. **Phase 4: Cutover**
   - Application chỉ dùng PostgreSQL
   - Stop writing to Sheets

5. **Phase 5: Cleanup**
   - Archive Sheets

**Lưu ý:**
- Phức tạp hơn, nhưng không có downtime
- Cần handle conflicts (nếu có data khác nhau giữa 2 nguồn)

---

## ✅ BÀI TẬP 5: TỰ ĐÁNH GIÁ

### Câu 5.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Database là gì?**
   - Hệ thống quản lý dữ liệu có tổ chức
   - Cho phép lưu trữ, truy xuất, cập nhật dữ liệu hiệu quả
   - Đảm bảo tính nhất quán, bảo mật, concurrent access

2. **RDBMS là gì?**
   - Database dựa trên mô hình quan hệ (tables)
   - Sử dụng SQL
   - Đảm bảo ACID properties

3. **ACID:**
   - **A**tomicity: Transaction hoặc thành công hoàn toàn, hoặc thất bại hoàn toàn
   - **C**onsistency: Dữ liệu luôn ở trạng thái hợp lệ
   - **I**solation: Các transaction độc lập với nhau
   - **D**urability: Dữ liệu đã commit không bị mất

4. **Khi nào dùng RDBMS?**
   - Structured data, cần ACID, cần JOIN, cần data integrity

5. **Khi nào dùng NoSQL?**
   - Unstructured data, cần scale ngang, schema linh hoạt

6. **Database vs File System:**
   - Database: Index → tìm nhanh, có transaction, có integrity
   - File System: Đơn giản nhưng không phù hợp cho structured data

---

### Câu 5.2: Áp dụng thực tế

**a) Chọn database:**

**Đáp án: RDBMS** (PostgreSQL)

**Lý do:**
- ✅ Dữ liệu có cấu trúc rõ ràng (users, boards, lists, cards)
- ✅ Có mối quan hệ (user → boards → lists → cards)
- ✅ Cần ACID (tạo card phải atomic: tạo card + update list count)
- ✅ Cần query phức tạp (JOIN để lấy tất cả cards trong board, aggregate để đếm)
- ✅ Cần data integrity (card phải có list hợp lệ, list phải có board hợp lệ)

---

**b) High-level schema:**

```
users
  - id (PK)
  - email
  - name

boards
  - id (PK)
  - user_id (FK → users.id)
  - name
  - created_at

lists
  - id (PK)
  - board_id (FK → boards.id)
  - name
  - position

cards
  - id (PK)
  - list_id (FK → lists.id)
  - title
  - description
  - due_date
  - position

comments
  - id (PK)
  - card_id (FK → cards.id)
  - user_id (FK → users.id)
  - content
  - created_at

attachments
  - id (PK)
  - card_id (FK → cards.id)
  - file_path
  - file_name
```

---

**c) Real-time collaboration:**

**Database choice KHÔNG thay đổi** (vẫn RDBMS cho core data)

**Nhưng có thể thêm:**
- **WebSocket server** cho real-time updates
- **Redis pub/sub** cho message broadcasting
- **Operational Transform (OT)** hoặc **CRDT** cho conflict resolution

**Architecture:**
```
Client → WebSocket Server → Redis pub/sub
                          ↓
                    PostgreSQL (source of truth)
```

**Lưu ý:**
- PostgreSQL vẫn là source of truth
- Real-time chỉ là layer trên, không thay đổi database choice

---

## 🎯 BÀI TẬP NÂNG CAO

### Câu A.1: Polyglot Persistence

**Polyglot Persistence** là việc sử dụng nhiều loại database khác nhau trong cùng một hệ thống, mỗi loại phù hợp với use case cụ thể.

**Ví dụ: E-commerce Platform**

```
┌─────────────────────┐
│  E-commerce App     │
└──────────┬──────────┘
           │
    ┌──────┴──────┐
    │             │
┌───▼────┐   ┌───▼──────┐   ┌──────┐   ┌─────────┐
│PostgreSQL│   │  MongoDB  │   │ Redis │   │Elasticsearch│
│(Orders)  │   │(Products) │   │(Cache)│   │  (Search)   │
└─────────┘   └───────────┘   └──────┘   └─────────┘
```

**PostgreSQL cho:**
- Orders, payments (ACID, integrity)
- Users, addresses (structured data)

**MongoDB cho:**
- Product catalog (schema linh hoạt, mỗi category có attributes khác)

**Redis cho:**
- Session storage
- Shopping cart (temporary data)
- Cache (hot products)

**Elasticsearch cho:**
- Product search (full-text search)
- Analytics (aggregations)

**Lý do:**
- Mỗi database tối ưu cho use case cụ thể
- Không có "one-size-fits-all" database
- Trade-off: Phức tạp hơn, nhưng performance tốt hơn

---

### Câu A.2: CAP Theorem

**CAP Theorem** nói rằng trong distributed system, không thể đảm bảo cả 3 properties cùng lúc:
- **C**onsistency: Tất cả nodes thấy cùng data
- **A**vailability: System luôn available (có thể đọc/ghi)
- **P**artition tolerance: System vẫn hoạt động khi network partition

**Phải chọn 2 trong 3:**

**RDBMS thường chọn: CP (Consistency + Partition tolerance)**
- Đảm bảo consistency (ACID)
- Có thể mất availability khi network partition
- Ví dụ: PostgreSQL, MySQL

**NoSQL thường chọn: AP (Availability + Partition tolerance)**
- Đảm bảo availability (luôn có thể đọc/ghi)
- Chấp nhận eventual consistency
- Ví dụ: MongoDB, Cassandra

**Lưu ý:**
- Trong thực tế, không có system "pure CP" hay "pure AP"
- Thường là trade-off, ưu tiên một property hơn property khác

---

## 📝 TÓM TẮT

### Key Learnings

1. **Database vs File System:** Database vượt trội cho structured data ở mọi khía cạnh (performance, integrity, concurrent access)

2. **RDBMS vs NoSQL:** 
   - RDBMS: Structured data, ACID, query phức tạp
   - NoSQL: Unstructured data, scale ngang, schema linh hoạt

3. **ACID là nền tảng** của RDBMS, đảm bảo data consistency và reliability

4. **Chọn database là quyết định quan trọng**, ảnh hưởng lâu dài. Phải hiểu trade-offs.

5. **Polyglot Persistence** là pattern phổ biến - dùng nhiều databases cho use cases khác nhau

6. **Senior thinking:** Không chỉ biết "dùng gì", mà còn biết "tại sao" và "trade-offs"

---

**Chúc mừng hoàn thành Day-001!** 🎉

**Chuẩn bị cho Day-002: Table, Row, Column - Kiến trúc cơ bản** 🚀

