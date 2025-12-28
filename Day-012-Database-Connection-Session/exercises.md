# Day-012: Bài Tập - Database Connection & Session

## 📋 HƯỚNG DẪN

Các bài tập này giúp bạn củng cố kiến thức về Database Connection & Session. Hãy suy nghĩ kỹ trước khi xem solutions.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Connection vs Session

**Câu hỏi:** Hãy giải thích ngắn gọn:
- Connection là gì?
- Session là gì?
- Sự khác biệt giữa Connection và Session?

---

### Câu 1.2: Connection Pool

**Câu hỏi:**

a) Connection Pool là gì?

b) Tại sao Connection Pool quan trọng?

c) Khi nào nên dùng Connection Pool?

---

### Câu 1.3: Connection Lifecycle

**Câu hỏi:**

a) Connection lifecycle là gì? (tạo, sử dụng, đóng)

b) Tại sao cần quản lý connection lifecycle?

c) Hậu quả nếu không đóng connections?

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Connection Leak

**Tình huống:**

Code hiện tại:

```python
def get_user(user_id):
    conn = psycopg2.connect(...)  # Tạo connection mới
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
    result = cursor.fetchone()
    # ❌ KHÔNG đóng connection!
    return result
```

**Câu hỏi:**

a) Phân tích vấn đề với code này.

b) Hậu quả nếu dùng code này trong production?

c) Viết lại code đúng (dùng connection pool).

---

### Câu 2.2: Too Many Connections

**Tình huống:**

Database có 100 max connections, nhưng có 150 connections đang active.

**Câu hỏi:**

a) Tại sao có quá nhiều connections?

b) Hậu quả của việc này?

c) Làm thế nào fix?

---

### Câu 2.3: Idle Connections

**Tình huống:**

Database có 100 connections, nhưng 90 connections đang idle (không dùng).

**Câu hỏi:**

a) Tại sao có nhiều idle connections?

b) Hậu quả của việc này?

c) Làm thế nào fix?

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Connection Pool Configuration

**Yêu cầu:**

Thiết kế connection pool cho web application:
- 1000 requests/phút
- Mỗi request cần 1 connection
- Average query time: 50ms

**Câu hỏi:**

a) Cần bao nhiêu connections trong pool?

b) Min connections? Max connections?

c) Giải thích lý do.

---

### Câu 3.2: Multi-Service Architecture

**Yêu cầu:**

Hệ thống có 5 microservices, mỗi service connect đến database:
- Service A: 500 requests/phút
- Service B: 300 requests/phút
- Service C: 200 requests/phút
- Service D: 100 requests/phút
- Service E: 50 requests/phút

Database có 200 max connections.

**Câu hỏi:**

a) Cần bao nhiêu connections cho mỗi service?

b) Tổng số connections có vượt quá max không?

c) Làm thế nào optimize?

---

### Câu 3.3: Connection Monitoring

**Yêu cầu:**

Thiết kế monitoring cho connections.

**Câu hỏi:**

a) Metrics nào cần monitor?

b) Khi nào cần alert?

c) Làm thế nào monitor connections?

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Connection Pool vs New Connection

**Tình huống:**

Bạn có 2 options:

**Option A: Connection Pool**
- Reuse connections
- Overhead: Quản lý pool
- Performance: Nhanh (reuse)

**Option B: New Connection mỗi lần**
- Tạo connection mới mỗi request
- Overhead: Tạo connection mới (100-500ms)
- Performance: Chậm (tạo mới)

**Câu hỏi:**

a) So sánh 2 options về:
   - Performance
   - Resource usage
   - Scalability
   - Complexity

b) Option nào tốt hơn? Tại sao?

c) Khi nào nên dùng Option B?

---

### Câu 4.2: Connection Pool Size

**Tình huống:**

Bạn cần quyết định connection pool size:
- Small pool (5 connections): Ít resource, nhưng có thể block
- Large pool (50 connections): Nhiều resource, nhưng không block

**Câu hỏi:**

a) Làm thế nào quyết định pool size?

b) Trade-offs của small vs large pool?

c) Best practices cho pool size?

---

### Câu 4.3: Connection Leak Detection

**Câu hỏi:**

a) Làm thế nào phát hiện connection leak?

b) Tools/methods để detect leak?

c) Best practices để tránh connection leak?

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Analyze Connection Usage

**Tình huống:**

Database có 100 max connections, hiện tại:

```sql
SELECT state, count(*) 
FROM pg_stat_activity 
GROUP BY state;
```

**Kết quả:**
- active: 10
- idle: 85
- idle in transaction: 5

**Yêu cầu:**

a) Phân tích tình trạng connections.

b) Có vấn đề gì không?

c) Làm thế nào fix?

---

### Câu 5.2: Connection Pool Implementation

**Yêu cầu:**

Viết code (pseudo-code) để implement connection pool:

**Features:**
- Get connection from pool
- Return connection to pool
- Handle connection errors
- Limit max connections

---

### Câu 5.3: Connection Monitoring Query

**Yêu cầu:**

Viết SQL query để monitor connections:

**Metrics:**
- Total connections
- Active connections
- Idle connections
- Connections by database
- Long-running queries

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

Hãy tự trả lời các câu hỏi sau (không xem lại lý thuyết):

1. Connection là gì? Session là gì?

2. Sự khác biệt giữa Connection và Session?

3. Connection Pool là gì? Tại sao quan trọng?

4. Hậu quả nếu không quản lý connections đúng cách?

5. Làm thế nào tránh connection leak?

---

### Câu 6.2: Áp dụng thực tế

Tưởng tượng bạn đang thiết kế connection pool cho web application:

**Yêu cầu:**

a) Tính toán pool size cần thiết.

b) Thiết kế connection pool configuration.

c) Thiết kế monitoring và alerting.

---

## 🎯 BÀI TẬP NÂNG CAO (TÙY CHỌN)

### Câu A.1: Connection Pool Patterns

**Câu hỏi:**

a) Connection pool patterns phổ biến là gì? (Singleton, Factory, etc.)

b) Trade-offs của mỗi pattern?

c) Best practices cho connection pool implementation?

---

### Câu A.2: Database Connection Limits

**Câu hỏi:**

a) Tại sao databases có giới hạn số connections?

b) Làm thế nào tăng max connections?

c) Trade-offs của việc tăng max connections?

---

## 📝 LƯU Ý

- Hãy suy nghĩ kỹ trước khi xem solutions
- Connection management rất quan trọng trong production
- Senior SQL Engineer hiểu connection lifecycle và biết tránh connection leak

**Sau khi hoàn thành, xem `solutions.md` để so sánh đáp án!** 🚀

