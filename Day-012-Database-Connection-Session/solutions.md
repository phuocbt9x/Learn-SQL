# Day-012: Solutions - Database Connection & Session

## 📋 HƯỚNG DẪN ĐỌC SOLUTIONS

Solutions này không chỉ đưa ra đáp án, mà còn giải thích **tại sao** và **cách suy nghĩ** như một Senior SQL Engineer.

---

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Connection vs Session

**Connection là gì?**

**Đáp án:** Connection (Kết nối) là một đường liên kết giữa application và database server, cho phép gửi SQL queries và nhận kết quả.

**Session là gì?**

**Đáp án:** Session (Phiên làm việc) là một context trong một connection, bao gồm session variables, transaction state, isolation level.

**Sự khác biệt:**

| Tiêu chí | Connection | Session |
|----------|------------|---------|
| **Level** | Network level | Application level |
| **Scope** | Physical link | Logical context |
| **Resource** | Network, memory | Memory, state |

**Mối quan hệ:** Một connection = Một session (thường)

---

### Câu 1.2: Connection Pool

**a) Connection Pool là gì?**

**Đáp án:** Connection Pool (Bể kết nối) là một cơ chế quản lý connections, cho phép reuse connections thay vì tạo mới.

**b) Tại sao quan trọng?**

**Lý do:**
- **Performance**: Tạo connection mới tốn thời gian (100-500ms)
- **Resource**: Giới hạn số connections → tránh overload
- **Efficiency**: Reuse connections → giảm overhead

**c) Khi nào nên dùng?**

**Đáp án: BẮT BUỘC trong production**

**Khi:**
- Web applications
- High-traffic systems
- Microservices
- Any production app

---

### Câu 1.3: Connection Lifecycle

**a) Connection lifecycle là gì?**

**Đáp án:**
1. **Create**: Tạo connection
2. **Use**: Sử dụng connection (execute queries)
3. **Close**: Đóng connection (trả về pool)

**b) Tại sao cần quản lý?**

**Lý do:**
- **Resource**: Connections tốn tài nguyên
- **Leak**: Không đóng → connection leak
- **Limit**: Database có giới hạn số connections

**c) Hậu quả nếu không đóng?**

**Hậu quả:**
- **Connection leak**: Connections không được giải phóng
- **Max connections**: Đạt max connections → không thể tạo mới
- **Database crash**: Database có thể crash

---

## 🔍 BÀI TẬP 2: PHÂN TÍCH VÀ SỬA LỖI

### Câu 2.1: Connection Leak

**a) Phân tích vấn đề:**

**Vấn đề:**
- Tạo connection mới mỗi lần gọi function
- Không đóng connection sau khi dùng
- Connection leak → database có thể crash

**b) Hậu quả:**

**Hậu quả:**
- **Connection leak**: Connections không được giải phóng
- **Max connections**: Đạt max connections
- **Database crash**: Database không thể accept connections mới
- **Application down**: Application hoàn toàn down

**c) Code đúng:**

```python
# ✅ ĐÚNG: Dùng connection pool
from psycopg2 import pool

# Tạo connection pool (1 lần khi app start)
connection_pool = psycopg2.pool.SimpleConnectionPool(
    minconn=1,
    maxconn=10,
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

---

### Câu 2.2: Too Many Connections

**a) Tại sao có quá nhiều connections?**

**Lý do:**
- **Connection leak**: Connections không được đóng
- **No connection pooling**: Mỗi request tạo connection mới
- **Multiple services**: Nhiều services cùng connect
- **High traffic**: Nhiều requests cùng lúc

**b) Hậu quả:**

**Hậu quả:**
- **Max connections**: Đạt max connections
- **Cannot accept new**: Database không thể accept connections mới
- **Application errors**: Application errors khi không lấy được connection
- **Database crash**: Database có thể crash

**c) Làm thế nào fix?**

**Fix:**
1. **Connection pooling**: Dùng connection pool
2. **Close connections**: Đảm bảo đóng connections
3. **Increase max connections**: Tăng max connections (temporary)
4. **Kill idle connections**: Kill idle connections

---

### Câu 2.3: Idle Connections

**a) Tại sao có nhiều idle connections?**

**Lý do:**
- **Connection leak**: Connections không được đóng
- **Long-lived connections**: Connections sống lâu nhưng không dùng
- **Connection pool không tối ưu**: Pool size quá lớn

**b) Hậu quả:**

**Hậu quả:**
- **Waste resources**: Lãng phí tài nguyên
- **Block new connections**: Chiếm slots → block connections mới
- **Max connections**: Có thể đạt max connections

**c) Làm thế nào fix?**

**Fix:**
1. **Close idle connections**: Đóng connections idle quá lâu
2. **Optimize pool size**: Giảm pool size
3. **Idle timeout**: Set idle timeout cho pool
4. **Kill idle connections**: Kill idle connections manually

```sql
-- Kill idle connections > 5 phút
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle'
AND state_change < now() - interval '5 minutes';
```

---

## 🧠 BÀI TẬP 3: THIẾT KẾ

### Câu 3.1: Connection Pool Configuration

**a) Cần bao nhiêu connections?**

**Tính toán:**
- 1000 requests/phút = ~17 requests/giây
- Average query time: 50ms
- Concurrent connections = 17 * 0.05 = ~1 connection

**Nhưng cần buffer:**
- Peak traffic: 2-3x average
- Connection overhead: 10-20% overhead
- **Recommended: 5-10 connections**

**b) Min connections? Max connections?**

**Min connections: 2-3**
- Giữ connections sẵn sàng
- Giảm latency khi có request

**Max connections: 10-15**
- Handle peak traffic
- Tránh quá nhiều connections

**c) Giải thích:**

**Lý do:**
- **Average load**: 1-2 connections đủ
- **Peak load**: 5-10 connections
- **Buffer**: 10-15 connections để handle spikes
- **Resource**: Không tốn quá nhiều resource

---

### Câu 3.2: Multi-Service Architecture

**a) Cần bao nhiêu connections cho mỗi service?**

**Tính toán:**
- Service A: 500 req/min = ~8 req/s → 8 * 0.05 = 0.4 → **5 connections**
- Service B: 300 req/min = ~5 req/s → 5 * 0.05 = 0.25 → **3 connections**
- Service C: 200 req/min = ~3 req/s → 3 * 0.05 = 0.15 → **3 connections**
- Service D: 100 req/min = ~2 req/s → 2 * 0.05 = 0.1 → **2 connections**
- Service E: 50 req/min = ~1 req/s → 1 * 0.05 = 0.05 → **2 connections**

**Total: 5 + 3 + 3 + 2 + 2 = 15 connections**

**b) Tổng số connections có vượt quá max không?**

**Đáp án: KHÔNG**

**Lý do:**
- Total: 15 connections
- Max: 200 connections
- Còn dư: 185 connections

**c) Làm thế nào optimize?**

**Optimize:**
1. **Connection pooling**: Dùng connection pool cho mỗi service
2. **Monitor**: Monitor connections của mỗi service
3. **Limit per service**: Giới hạn connections per service
4. **Scale**: Scale services nếu cần

---

### Câu 3.3: Connection Monitoring

**a) Metrics nào cần monitor?**

**Metrics:**
- **Total connections**: Tổng số connections
- **Active connections**: Connections đang active
- **Idle connections**: Connections idle
- **Connections by database**: Connections theo database
- **Long-running queries**: Queries chạy lâu

**b) Khi nào cần alert?**

**Alert khi:**
- **High connections**: Connections > 80% max
- **Connection errors**: Errors khi tạo/đóng connections
- **Idle connections**: Idle connections > 50% total
- **Long-running queries**: Queries > 30 giây

**c) Làm thế nào monitor?**

**PostgreSQL:**
```sql
-- Total connections
SELECT count(*) FROM pg_stat_activity;

-- Active vs Idle
SELECT state, count(*) 
FROM pg_stat_activity 
GROUP BY state;

-- Connections by database
SELECT datname, count(*) 
FROM pg_stat_activity 
GROUP BY datname;

-- Long-running queries
SELECT pid, now() - query_start as duration, query
FROM pg_stat_activity
WHERE state = 'active'
AND now() - query_start > interval '30 seconds';
```

---

## 🎓 BÀI TẬP 4: TƯ DUY SENIOR

### Câu 4.1: Connection Pool vs New Connection

**a) So sánh:**

| Tiêu chí | Connection Pool | New Connection |
|----------|----------------|----------------|
| **Performance** | Nhanh (reuse) | Chậm (tạo mới 100-500ms) |
| **Resource** | Hiệu quả | Lãng phí |
| **Scalability** | Tốt | Kém |
| **Complexity** | Phức tạp hơn | Đơn giản hơn |

**b) Option nào tốt hơn?**

**Đáp án: Connection Pool**

**Lý do:**
- **Performance**: Nhanh hơn nhiều
- **Resource**: Hiệu quả hơn
- **Scalability**: Scale tốt hơn
- **Production**: Bắt buộc trong production

**c) Khi nào nên dùng Option B?**

**Đáp án: KHÔNG NÊN trong production**

**Chỉ dùng khi:**
- Development/testing
- One-off scripts
- Không có connection pool available

---

### Câu 4.2: Connection Pool Size

**a) Làm thế nào quyết định pool size?**

**Công thức:**
```
Pool size = (Requests/second) * (Average query time) * (Buffer factor)
```

**Ví dụ:**
- 10 requests/s
- 50ms average query time
- 2x buffer
- Pool size = 10 * 0.05 * 2 = 1 → **3-5 connections** (minimum)

**b) Trade-offs:**

**Small pool (5 connections):**
- ✅ Ít resource
- ❌ Có thể block khi peak traffic

**Large pool (50 connections):**
- ✅ Không block
- ❌ Tốn nhiều resource
- ❌ Có thể đạt max connections

**c) Best practices:**

**Best practices:**
- **Start small**: Bắt đầu với pool size nhỏ
- **Monitor**: Monitor connections và adjust
- **Buffer**: Thêm 20-50% buffer cho peak traffic
- **Limit per service**: Giới hạn connections per service

---

### Câu 4.3: Connection Leak Detection

**a) Làm thế nào phát hiện connection leak?**

**Methods:**
1. **Monitor connections**: Monitor số connections theo thời gian
2. **Check idle connections**: Check idle connections tăng dần
3. **Log connections**: Log khi tạo/đóng connections
4. **Alert on high connections**: Alert khi connections cao

**b) Tools/methods:**

**PostgreSQL:**
```sql
-- Check connections over time
SELECT count(*) as total_connections,
       count(*) FILTER (WHERE state = 'idle') as idle_connections
FROM pg_stat_activity;
```

**Application:**
- Log connection creation/destruction
- Monitor connection pool metrics
- Alert on connection leaks

**c) Best practices:**

**Best practices:**
1. **Always close**: Luôn đóng connections trong finally block
2. **Use connection pool**: Dùng connection pool
3. **Monitor**: Monitor connections
4. **Alert**: Alert khi có vấn đề
5. **Code review**: Review code để tìm connection leaks

---

## 🎯 BÀI TẬP 5: THỰC HÀNH

### Câu 5.1: Analyze Connection Usage

**a) Phân tích:**

**Tình trạng:**
- **Active: 10**: 10 connections đang active (OK)
- **Idle: 85**: 85 connections idle (VẤN ĐỀ - quá nhiều!)
- **Idle in transaction: 5**: 5 connections idle trong transaction (VẤN ĐỀ)

**b) Có vấn đề gì không?**

**Vấn đề:**
- **Too many idle**: 85 idle connections → lãng phí resource
- **Idle in transaction**: 5 connections idle trong transaction → có thể block
- **Total: 100**: Đạt max connections → không thể tạo mới

**c) Làm thế nào fix?**

**Fix:**
1. **Kill idle connections**: Kill idle connections > 5 phút
2. **Kill idle in transaction**: Kill idle in transaction
3. **Connection pooling**: Dùng connection pool
4. **Close connections**: Đảm bảo đóng connections

```sql
-- Kill idle connections
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle'
AND state_change < now() - interval '5 minutes';

-- Kill idle in transaction
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle in transaction'
AND state_change < now() - interval '5 minutes';
```

---

### Câu 5.2: Connection Pool Implementation

**Pseudo-code:**

```python
class ConnectionPool:
    def __init__(self, min_conn, max_conn, connection_string):
        self.min_conn = min_conn
        self.max_conn = max_conn
        self.connection_string = connection_string
        self.pool = []
        self.active_connections = 0
        
        # Tạo min connections
        for _ in range(min_conn):
            conn = create_connection(connection_string)
            self.pool.append(conn)
    
    def get_connection(self):
        if self.pool:
            # Lấy từ pool
            conn = self.pool.pop()
            self.active_connections += 1
            return conn
        elif self.active_connections < self.max_conn:
            # Tạo connection mới
            conn = create_connection(connection_string)
            self.active_connections += 1
            return conn
        else:
            # Pool đầy, wait hoặc error
            raise Exception("Connection pool exhausted")
    
    def return_connection(self, conn):
        # Trả về pool
        self.pool.append(conn)
        self.active_connections -= 1
```

---

### Câu 5.3: Connection Monitoring Query

**SQL queries:**

```sql
-- Total connections
SELECT count(*) as total_connections
FROM pg_stat_activity;

-- Active vs Idle
SELECT 
    state,
    count(*) as count
FROM pg_stat_activity
GROUP BY state;

-- Connections by database
SELECT 
    datname,
    count(*) as connections
FROM pg_stat_activity
GROUP BY datname
ORDER BY connections DESC;

-- Long-running queries
SELECT 
    pid,
    datname,
    usename,
    now() - query_start as duration,
    state,
    query
FROM pg_stat_activity
WHERE state = 'active'
AND now() - query_start > interval '30 seconds'
ORDER BY duration DESC;
```

---

## ✅ BÀI TẬP 6: TỰ ĐÁNH GIÁ

### Câu 6.1: Kiến thức cốt lõi

**Đáp án tham khảo:**

1. **Connection**: Physical link giữa application và database
2. **Session**: Logical context trong connection
3. **Connection Pool**: Cơ chế quản lý connections, reuse connections
4. **Hậu quả**: Connection leak → database crash
5. **Tránh leak**: Dùng connection pool, luôn đóng connections

---

### Câu 6.2: Áp dụng thực tế

**a) Tính toán pool size:**

**Tính toán:**
- Requests/second: X
- Average query time: Y seconds
- Pool size = X * Y * 2 (buffer)

**b) Connection pool configuration:**

```python
connection_pool = psycopg2.pool.SimpleConnectionPool(
    minconn=2,
    maxconn=10,
    ...
)
```

**c) Monitoring và alerting:**

**Metrics:**
- Total connections
- Active connections
- Idle connections
- Connection errors

**Alerts:**
- Connections > 80% max
- Connection errors
- Idle connections > 50%

---

## 📝 TÓM TẮT

### Key Learnings

1. **Connection**: Physical link giữa application và database
2. **Session**: Logical context trong connection
3. **Connection Pool**: Cơ chế quản lý connections, reuse connections
4. **Connection leak**: Nguy hiểm, có thể crash database
5. **Connection pooling bắt buộc**: Phải dùng trong production

### Best Practices

✅ **Use connection pooling**: Dùng connection pool
✅ **Limit connections**: Giới hạn số connections
✅ **Close connections**: Luôn đóng connections
✅ **Monitor connections**: Monitor số connections và state
✅ **Alert on issues**: Alert khi có vấn đề

---

**Chúc mừng hoàn thành Day-012!** 🎉

**Chuẩn bị cho Day-013: ACID Properties - Nền tảng Transaction** 🚀

