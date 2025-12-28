# Day-012: Database Connection & Session

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Connection là gì và tại sao quan trọng
- Session là gì và khác với Connection như thế nào
- Connection pool là gì (high-level)
- Hậu quả nếu không quản lý connections đúng cách
- Production issues liên quan đến connections

---

## 1️⃣ CONNECTION LÀ GÌ?

### **Nó là gì?**

**Connection** (Kết nối) là một **đường liên kết** giữa application và database server, cho phép:
- **Gửi SQL queries** từ application đến database
- **Nhận kết quả** từ database về application
- **Duy trì trạng thái** (state) giữa application và database

**Ví dụ:**

```
Application (Client)          Database Server
     │                              │
     │  ──── CONNECTION ────>       │
     │                              │
     │  SELECT * FROM users         │
     │  ────────────────────────> │
     │                              │
     │  <────────────────────────   │
     │  [Result: 100 rows]          │
     │                              │
```

**Đặc điểm:**

1. **Resource-intensive**: Mỗi connection tốn tài nguyên (memory, CPU)
2. **Limited**: Database server có giới hạn số connections
3. **Stateful**: Connection giữ state (transaction, session variables)
4. **Network**: Connection đi qua network (TCP/IP)

### **Tại sao tồn tại?**

Connection tồn tại để:

1. **Communication**: Application cần cách giao tiếp với database
2. **State management**: Giữ state (transaction, session variables)
3. **Security**: Authentication và authorization
4. **Performance**: Reuse connections thay vì tạo mới mỗi lần

### **Khi nào dùng trong production?**

Connection được dùng **MỖI LẦN** application tương tác với database:

✅ **Mỗi query**: Cần connection để gửi query
✅ **Mỗi transaction**: Cần connection để thực thi transaction
✅ **Mỗi operation**: INSERT, UPDATE, DELETE đều cần connection

**Lưu ý:** Không nên tạo connection mới cho mỗi query. Nên **reuse connections** (connection pooling).

---

## 2️⃣ SESSION LÀ GÌ?

### **Nó là gì?**

**Session** (Phiên làm việc) là một **context** trong một connection, bao gồm:
- **Session variables**: SET variables, transaction state
- **Temporary objects**: Temporary tables, cursors
- **Isolation level**: Transaction isolation level
- **User context**: Current user, permissions

**Ví dụ:**

```sql
-- Session 1 (Connection 1)
BEGIN;
SET my_var = 'value1';
SELECT * FROM users;

-- Session 2 (Connection 2)
BEGIN;
SET my_var = 'value2';  -- Khác với Session 1
SELECT * FROM users;
```

**Đặc điểm:**

1. **Per-connection**: Mỗi connection có một session
2. **Isolated**: Sessions độc lập với nhau
3. **Stateful**: Giữ state trong session
4. **Temporary**: Session kết thúc khi connection đóng

### **Tại sao tồn tại?**

Session tồn tại để:

1. **Isolation**: Mỗi connection có context riêng
2. **State management**: Giữ state (variables, transaction)
3. **Security**: User context, permissions
4. **Concurrency**: Nhiều sessions cùng chạy độc lập

### **Khi nào dùng trong production?**

Session được dùng **TỰ ĐỘNG** khi tạo connection:

✅ **Mỗi connection**: Tự động có một session
✅ **Transaction**: Session quản lý transaction state
✅ **Variables**: SET variables trong session
✅ **Temporary objects**: Temporary tables trong session

**Lưu ý:** Session không cần tạo thủ công - nó tự động tạo khi connection được thiết lập.

---

## 3️⃣ CONNECTION VS SESSION

### **Sự khác biệt**

| Tiêu chí | Connection | Session |
|----------|------------|---------|
| **Level** | Network level | Application level |
| **Scope** | Physical link | Logical context |
| **Resource** | Network, memory | Memory, state |
| **Lifetime** | Từ khi connect đến disconnect | Từ khi connect đến disconnect (thường = connection) |
| **Multiple** | Có thể có nhiều connections | Mỗi connection có một session |

### **Mối quan hệ**

```
Connection 1 ──> Session 1
Connection 2 ──> Session 2
Connection 3 ──> Session 3
```

**Một connection = Một session** (thường)

**Nhưng có thể có:**
- **Connection pooling**: Reuse connections → nhiều queries dùng chung connection
- **Multiple sessions**: Một số databases hỗ trợ multiple sessions per connection

---

## 4️⃣ CONNECTION POOL LÀ GÌ? (HIGH-LEVEL)

### **Nó là gì?**

**Connection Pool** (Bể kết nối) là một **cơ chế quản lý connections**, cho phép:
- **Reuse connections**: Tái sử dụng connections thay vì tạo mới
- **Limit connections**: Giới hạn số connections tối đa
- **Manage lifecycle**: Tạo, reuse, close connections

**Ví dụ:**

```
Application
     │
     │  Request 1: SELECT * FROM users
     │  ──────────────────────────────> Connection Pool
     │                                  │
     │                                  │ Get connection from pool
     │                                  │ Execute query
     │                                  │ Return connection to pool
     │  <───────────────────────────────│
     │  Result
     │
     │  Request 2: SELECT * FROM orders
     │  ──────────────────────────────> Connection Pool
     │                                  │
     │                                  │ Reuse same connection!
     │                                  │ Execute query
     │                                  │ Return connection to pool
     │  <───────────────────────────────│
     │  Result
```

**Đặc điểm:**

1. **Reuse**: Tái sử dụng connections → giảm overhead
2. **Limit**: Giới hạn số connections → tránh overload database
3. **Efficient**: Hiệu quả hơn tạo connection mới mỗi lần
4. **Manage**: Quản lý lifecycle của connections

### **Tại sao tồn tại?**

Connection Pool tồn tại để:

1. **Performance**: Tạo connection mới tốn thời gian (100-500ms)
2. **Resource**: Giới hạn số connections → tránh overload
3. **Efficiency**: Reuse connections → giảm overhead
4. **Scalability**: Hỗ trợ nhiều requests cùng lúc

### **Khi nào dùng trong production?**

Connection Pool **BẮT BUỘC** trong production:

✅ **Web applications**: Mỗi request cần connection
✅ **High-traffic**: Nhiều requests cùng lúc
✅ **Microservices**: Nhiều services cùng connect
✅ **Any production app**: Không nên tạo connection mới mỗi lần

**Lưu ý:** Hầu hết frameworks/drivers đều có connection pooling built-in.

---

## 5️⃣ PRODUCTION STORY: DATABASE CRASH DO CONNECTION LEAK

### **Context**

E-commerce platform có 10 microservices, mỗi service connect đến PostgreSQL database.

**Ban đầu (Tháng 1-2):**
- Mỗi service tạo connection mới mỗi request
- Không có connection pooling
- Database có 100 max connections
- Mọi thứ "hoạt động" với 50 requests/phút

### **Vấn đề xuất hiện (Tháng 3)**

Khi traffic tăng lên **1000 requests/phút**:

**Ngày 1: Database chậm**
- Queries mất 5-10 giây (thường < 100ms)
- Application timeouts

**Ngày 2: Database crash**
- Error: "too many connections"
- Database không thể accept connections mới
- Application hoàn toàn down

**Investigation:**

```sql
-- Check current connections
SELECT count(*) FROM pg_stat_activity;
-- Result: 100 (max connections reached!)

-- Check connections by state
SELECT state, count(*) 
FROM pg_stat_activity 
GROUP BY state;
-- Result:
-- active: 5
-- idle: 95  <-- 95 connections đang idle (không dùng!)
```

**Root cause:**

1. **Connection leak**: Services tạo connections nhưng không đóng
2. **No connection pooling**: Mỗi request tạo connection mới
3. **Idle connections**: 95 connections idle → chiếm slots
4. **Max connections**: Database chỉ có 100 max connections

**Vấn đề code:**

```python
# ❌ SAI: Tạo connection mới mỗi lần, không đóng
def get_user(user_id):
    conn = psycopg2.connect(...)  # Tạo connection mới
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
    result = cursor.fetchone()
    # ❌ KHÔNG đóng connection!
    return result
```

### **Fix**

**Fix 1: Connection Pooling**

```python
# ✅ ĐÚNG: Dùng connection pool
from psycopg2 import pool

# Tạo connection pool (1 lần khi app start)
connection_pool = psycopg2.pool.SimpleConnectionPool(
    minconn=1,
    maxconn=10,  # Max 10 connections per service
    ...
)

def get_user(user_id):
    conn = connection_pool.getconn()  # Lấy từ pool
    try:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
        result = cursor.fetchone()
        return result
    finally:
        connection_pool.putconn(conn)  # Trả về pool
```

**Fix 2: Increase Max Connections (temporary)**

```sql
-- Tăng max connections (temporary fix)
ALTER SYSTEM SET max_connections = 200;
SELECT pg_reload_conf();
```

**Fix 3: Kill Idle Connections**

```sql
-- Kill idle connections > 5 phút
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle'
AND state_change < now() - interval '5 minutes';
```

### **Kết quả**

✅ **Connection pooling**: Mỗi service chỉ dùng 10 connections (thay vì 100+)
✅ **No connection leak**: Connections được trả về pool
✅ **Stable**: Database không còn crash
✅ **Performance**: Queries nhanh lại (< 100ms)

### **Lesson Learned**

1. **Connection leak nguy hiểm**: Có thể crash database
2. **Connection pooling bắt buộc**: Phải dùng connection pooling trong production
3. **Monitor connections**: Monitor số connections và state
4. **Limit connections**: Giới hạn số connections per service
5. **Close connections**: Luôn đóng connections sau khi dùng

---

## 6️⃣ BEST PRACTICES

### **6.1. Connection Management**

✅ **Use connection pooling**: Dùng connection pool trong production
✅ **Limit connections**: Giới hạn số connections per service
✅ **Close connections**: Luôn đóng connections sau khi dùng
✅ **Monitor connections**: Monitor số connections và state

### **6.2. Connection Pool Configuration**

✅ **Min connections**: Giữ một số connections luôn sẵn sàng
✅ **Max connections**: Giới hạn số connections tối đa
✅ **Idle timeout**: Đóng connections idle quá lâu
✅ **Connection timeout**: Timeout khi không lấy được connection

### **6.3. Monitoring**

✅ **Monitor active connections**: Số connections đang active
✅ **Monitor idle connections**: Số connections idle
✅ **Monitor connection errors**: Errors khi tạo/đóng connections
✅ **Alert on high connections**: Alert khi connections gần max

---

## 7️⃣ TÓM TẮT

### **Key Takeaways**

1. **Connection**: Physical link giữa application và database
2. **Session**: Logical context trong connection
3. **Connection Pool**: Cơ chế quản lý connections, reuse connections
4. **Connection leak**: Nguy hiểm, có thể crash database
5. **Connection pooling bắt buộc**: Phải dùng trong production

### **Best Practices**

✅ **Use connection pooling**: Dùng connection pool
✅ **Limit connections**: Giới hạn số connections
✅ **Close connections**: Luôn đóng connections
✅ **Monitor connections**: Monitor số connections và state
✅ **Alert on issues**: Alert khi có vấn đề

### **Câu hỏi tự kiểm tra**

1. Connection là gì? Session là gì?
2. Sự khác biệt giữa Connection và Session?
3. Connection Pool là gì? Tại sao quan trọng?
4. Hậu quả nếu không quản lý connections đúng cách?
5. Làm thế nào tránh connection leak?

---




**Chuẩn bị cho [Day-013: ACID-Properties](../Day-013-ACID-Properties/theory.md)** 🚀
