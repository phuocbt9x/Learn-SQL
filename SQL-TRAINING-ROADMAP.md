# 🎯 SQL TRAINING ROADMAP: ZERO → SENIOR SQL ENGINEER

## 📋 TỔNG QUAN CHƯƠNG TRÌNH

Chương trình đào tạo SQL từ cơ bản đến nâng cao, tập trung vào **SQL thuần túy** (không có ORM, không có application code).

**Mục tiêu**: Biến một người chưa biết gì về database thành một **Senior SQL Engineer** có thể:
- Viết SQL đúng, nhanh, dễ đọc
- Hiểu query execution & performance
- Debug slow query trong production
- Phân tích trade-offs giữa các cách viết SQL
- Pass Mid → Senior SQL interviews

---

## 🗺️ CẤU TRÚC CHƯƠNG TRÌNH

### **PHASE 1: DATABASE FOUNDATIONS** (Day-001 → Day-015)

**Mục đích**: Xây dựng nền tảng vững chắc về database trước khi học SQL syntax.

#### Day-001: Database là gì? RDBMS là gì?
- Database là gì? Tại sao cần database?
- RDBMS (Relational Database Management System) là gì?
- So sánh Database vs File System
- Các loại database (RDBMS, NoSQL, NewSQL)
- **Production Story**: Tại sao startup chọn PostgreSQL thay vì Excel?

#### Day-002: Table, Row, Column - Kiến trúc cơ bản
- Table (Bảng) là gì?
- Row (Hàng/Dòng) và Column (Cột) là gì?
- Data types cơ bản (INTEGER, VARCHAR, DATE, TIMESTAMP)
- NULL là gì? Tại sao NULL quan trọng?
- **Production Story**: Lỗi do NULL gây ra trong hệ thống thanh toán

#### Day-003: Primary Key - Định danh duy nhất
- Primary Key là gì?
- Tại sao cần Primary Key?
- Single Key vs Composite Key
- Auto-increment vs UUID vs Natural Key
- **Production Story**: Vấn đề duplicate key trong production

#### Day-004: Foreign Key - Mối quan hệ giữa các bảng
- Foreign Key là gì?
- Referential Integrity là gì?
- ON DELETE CASCADE vs RESTRICT vs SET NULL
- Khi nào nên/nên không dùng Foreign Key?
- **Production Story**: Lỗi orphan records do thiếu Foreign Key constraint

#### Day-005: Normalization - Chuẩn hóa dữ liệu (1NF)
- 1NF (First Normal Form) là gì?
- Atomic values là gì?
- Tại sao cần 1NF?
- Ví dụ vi phạm 1NF và cách sửa
- **Production Story**: Dữ liệu bị duplicate 10x do vi phạm 1NF

#### Day-006: Normalization - 2NF (Second Normal Form)
- 2NF là gì?
- Partial dependency là gì?
- Khi nào cần 2NF?
- Trade-off: Normalization vs Denormalization
- **Production Story**: Query chậm do thiếu 2NF, fix bằng cách normalize

#### Day-007: Normalization - 3NF (Third Normal Form)
- 3NF là gì?
- Transitive dependency là gì?
- Khi nào dừng ở 2NF? Khi nào cần 3NF?
- **Production Story**: Update anomaly do vi phạm 3NF

#### Day-008: Data Types & Storage - Hiểu sâu về lưu trữ
- INTEGER types (SMALLINT, INT, BIGINT)
- VARCHAR vs CHAR vs TEXT
- DATE vs TIMESTAMP vs TIMESTAMPTZ
- NUMERIC vs FLOAT vs DOUBLE
- Storage size và performance impact
- **Production Story**: Query chậm do dùng VARCHAR(255) cho mọi cột

#### Day-009: Index - Cơ bản về chỉ mục
- Index là gì?
- Tại sao Index làm query nhanh hơn?
- B-Tree index là gì? (high-level)
- Index Scan vs Full Table Scan
- **Production Story**: Query từ 30s → 0.1s nhờ đúng index

#### Day-010: Logical vs Physical Design
- Logical design (ERD, relationships)
- Physical design (tables, indexes, partitions)
- Gap giữa logical và physical
- **Production Story**: ERD đẹp nhưng performance tệ do physical design sai

#### Day-011: SQL Execution Flow - High-level
- SQL query đi qua những bước nào?
- Parser → Planner → Executor
- Query Plan là gì?
- **Production Story**: Query "đơn giản" nhưng chậm - tại sao?

#### Day-012: Database Connection & Session
- Connection là gì?
- Session là gì?
- Connection pool là gì? (high-level)
- **Production Story**: Database crash do connection leak

#### Day-013: ACID Properties - Nền tảng Transaction
- ACID là gì? (Atomicity, Consistency, Isolation, Durability)
- Tại sao ACID quan trọng?
- Ví dụ minh họa từng property
- **Production Story**: Mất tiền do thiếu Atomicity

#### Day-014: Transaction - Cơ bản
- Transaction là gì?
- BEGIN, COMMIT, ROLLBACK
- Tại sao cần transaction?
- **Production Story**: Double-spending bug do thiếu transaction

#### Day-015: Review Phase 1 - Tổng hợp Foundations
- Tổng hợp lại tất cả concepts
- Bài tập tổng hợp
- Chuẩn bị cho Phase 2

---

### **PHASE 2: CORE SQL QUERY LANGUAGE** (Day-016 → Day-040)

**Mục đích**: Học SQL syntax từ cơ bản đến nâng cao, hiểu sâu cách query hoạt động.

#### Day-016: SELECT - Câu lệnh cơ bản nhất
- SELECT là gì?
- SELECT * vs SELECT column_list
- FROM clause
- WHERE clause cơ bản
- **Production Story**: SELECT * làm query chậm 10x

#### Day-017: WHERE - Điều kiện lọc dữ liệu
- WHERE với operators (=, <>, >, <, >=, <=)
- AND, OR, NOT
- Operator precedence
- NULL handling (IS NULL, IS NOT NULL)
- **Production Story**: Bug do NULL trong WHERE clause

#### Day-018: ORDER BY - Sắp xếp kết quả
- ORDER BY là gì?
- ASC vs DESC
- Multi-column sorting
- NULLS FIRST vs NULLS LAST
- Performance impact của ORDER BY
- **Production Story**: Query timeout do ORDER BY không có index

#### Day-019: LIMIT & OFFSET - Giới hạn kết quả
- LIMIT là gì?
- OFFSET là gì?
- Pagination pattern
- Performance của OFFSET lớn
- **Production Story**: Pagination chậm với OFFSET 10000+

#### Day-020: DISTINCT - Loại bỏ trùng lặp
- DISTINCT là gì?
- DISTINCT vs GROUP BY
- Performance impact
- Khi nào dùng DISTINCT?
- **Production Story**: DISTINCT làm query chậm do sort

#### Day-021: Aggregate Functions - Tổng hợp dữ liệu
- COUNT, SUM, AVG, MIN, MAX
- COUNT(*) vs COUNT(column) vs COUNT(DISTINCT column)
- NULL handling trong aggregate
- **Production Story**: COUNT sai do NULL

#### Day-022: GROUP BY - Nhóm dữ liệu
- GROUP BY là gì?
- Tại sao cần GROUP BY?
- GROUP BY với multiple columns
- GROUP BY execution flow
- **Production Story**: Query chậm do GROUP BY không có index

#### Day-023: HAVING - Lọc sau GROUP BY
- HAVING là gì?
- HAVING vs WHERE
- Khi nào dùng HAVING?
- **Production Story**: Bug do dùng WHERE thay vì HAVING

#### Day-024: JOIN - INNER JOIN
- JOIN là gì? Tại sao cần JOIN?
- INNER JOIN là gì?
- JOIN syntax (explicit vs implicit)
- JOIN execution (nested loop, hash join, merge join - high-level)
- **Production Story**: Query timeout do JOIN sai thứ tự

#### Day-025: JOIN - LEFT JOIN, RIGHT JOIN
- LEFT JOIN là gì?
- RIGHT JOIN là gì?
- LEFT JOIN vs INNER JOIN
- NULL handling trong LEFT JOIN
- **Production Story**: Missing data do dùng INNER JOIN thay vì LEFT JOIN

#### Day-026: JOIN - FULL OUTER JOIN
- FULL OUTER JOIN là gì?
- Khi nào dùng FULL OUTER JOIN?
- FULL OUTER JOIN vs UNION
- **Production Story**: Data reconciliation với FULL OUTER JOIN

#### Day-027: JOIN - Multiple Tables
- JOIN 3+ tables
- JOIN order và performance
- JOIN conditions (equality vs inequality)
- **Production Story**: Query chậm do JOIN 5 bảng không có index

#### Day-028: Subquery - Scalar Subquery
- Subquery là gì?
- Scalar subquery là gì?
- Subquery trong SELECT, WHERE
- Performance: Subquery vs JOIN
- **Production Story**: N+1 query problem do scalar subquery

#### Day-029: Subquery - EXISTS vs IN
- EXISTS là gì?
- IN là gì?
- EXISTS vs IN - khi nào dùng gì?
- Performance comparison
- **Production Story**: Query từ 10s → 0.5s nhờ đổi IN → EXISTS

#### Day-030: Subquery - Correlated Subquery
- Correlated subquery là gì?
- Execution flow của correlated subquery
- Khi nào dùng correlated subquery?
- Performance impact
- **Production Story**: Query timeout do correlated subquery

#### Day-031: CTE (Common Table Expression) - WITH clause
- CTE là gì?
- Tại sao dùng CTE?
- CTE vs Subquery
- Recursive CTE (giới thiệu)
- **Production Story**: Query dễ đọc hơn nhờ CTE

#### Day-032: UNION, INTERSECT, EXCEPT
- UNION là gì?
- UNION vs UNION ALL
- INTERSECT, EXCEPT
- Performance comparison
- **Production Story**: UNION ALL nhanh hơn UNION 5x

#### Day-033: CASE Expression - Logic điều kiện
- CASE WHEN là gì?
- Simple CASE vs Searched CASE
- CASE trong SELECT, WHERE, ORDER BY
- **Production Story**: Logic phức tạp được xử lý bằng CASE

#### Day-034: String Functions
- CONCAT, SUBSTRING, LENGTH, UPPER, LOWER
- LIKE, ILIKE, pattern matching
- Regular expressions (high-level)
- **Production Story**: Query chậm do LIKE '%pattern%'

#### Day-035: Date & Time Functions
- DATE functions (EXTRACT, DATE_TRUNC, AGE)
- TIMESTAMP arithmetic
- Timezone handling
- **Production Story**: Bug do timezone trong production

#### Day-036: Window Functions - Giới thiệu
- Window Functions là gì?
- Tại sao cần Window Functions?
- OVER clause
- **Production Story**: Query phức tạp đơn giản hóa bằng Window Functions

#### Day-037: Window Functions - ROW_NUMBER, RANK, DENSE_RANK
- ROW_NUMBER() là gì?
- RANK() vs DENSE_RANK()
- PARTITION BY trong Window Functions
- **Production Story**: Top N per group với ROW_NUMBER()

#### Day-038: Window Functions - Aggregate Window Functions
- SUM() OVER(), AVG() OVER()
- Running totals
- Moving averages
- **Production Story**: Analytics query nhanh hơn với Window Functions

#### Day-039: Window Functions - LAG, LEAD
- LAG() và LEAD() là gì?
- Khi nào dùng LAG/LEAD?
- **Production Story**: So sánh tháng này vs tháng trước

#### Day-040: Review Phase 2 - Tổng hợp Query Language
- Tổng hợp tất cả query patterns
- Bài tập tổng hợp phức tạp
- Chuẩn bị cho Phase 3

---

### **PHASE 3: ADVANCED SQL & PERFORMANCE** (Day-041 → Day-060)

**Mục đích**: Hiểu sâu về performance, execution plan, optimization.

#### Day-041: EXPLAIN - Đọc Execution Plan
- EXPLAIN là gì?
- Execution Plan là gì?
- Cách đọc plan (Seq Scan, Index Scan, Hash Join, etc.)
- **Production Story**: Debug query chậm bằng EXPLAIN

#### Day-042: EXPLAIN ANALYZE - Thực tế execution
- EXPLAIN ANALYZE là gì?
- Actual vs Estimated rows
- Timing information
- **Production Story**: Planner estimate sai → query chậm

#### Day-043: Index Types - B-Tree Index
- B-Tree index chi tiết
- Index Scan vs Index Only Scan
- Index trên single column
- **Production Story**: Query từ 5s → 0.05s nhờ index

#### Day-044: Index Types - Composite Index
- Composite index là gì?
- Column order trong composite index
- Left-prefix rule
- **Production Story**: Index không dùng được do column order sai

#### Day-045: Index Types - Partial Index
- Partial index là gì?
- Khi nào dùng partial index?
- **Production Story**: Index size giảm 80% với partial index

#### Day-046: Index Types - Unique Index
- Unique index là gì?
- Unique index vs Primary Key
- **Production Story**: Duplicate prevention với unique index

#### Day-047: Index - Covering Index
- Covering index là gì?
- Index Only Scan
- Trade-off: Index size vs Query speed
- **Production Story**: Query nhanh hơn 10x với covering index

#### Day-048: Query Performance - Full Table Scan
- Khi nào Full Table Scan xảy ra?
- Khi nào Full Table Scan là tốt?
- **Production Story**: Full Table Scan nhanh hơn Index Scan (edge case)

#### Day-049: Query Performance - Join Algorithms
- Nested Loop Join
- Hash Join
- Merge Join
- Khi nào dùng algorithm nào?
- **Production Story**: Query chậm do nested loop join với bảng lớn

#### Day-050: Query Performance - Sort & Aggregation
- Sort operations (External Sort)
- Hash Aggregation
- Group Aggregation
- **Production Story**: Query timeout do sort không fit memory

#### Day-051: Query Optimization - WHERE clause optimization
- Sargable vs Non-sargable predicates
- Function trong WHERE clause
- **Production Story**: Query chậm do function trong WHERE

#### Day-052: Query Optimization - Subquery to JOIN
- Khi nào rewrite subquery thành JOIN?
- Correlated subquery optimization
- **Production Story**: Query nhanh hơn 20x sau khi rewrite

#### Day-053: Query Optimization - Avoid SELECT *
- Tại sao tránh SELECT *?
- Impact lên network, memory, index usage
- **Production Story**: SELECT * làm query chậm và tốn bộ nhớ

#### Day-054: Query Optimization - LIMIT optimization
- LIMIT với ORDER BY
- Index cho LIMIT queries
- **Production Story**: Pagination nhanh hơn với index phù hợp

#### Day-055: Statistics & Query Planner
- Statistics là gì?
- ANALYZE command
- Planner dùng statistics như thế nào?
- **Production Story**: Query plan sai do statistics cũ

#### Day-056: Query Hints (nếu database hỗ trợ)
- Query hints là gì?
- Khi nào dùng hints?
- Trade-offs
- **Production Story**: Force index khi planner chọn sai

#### Day-057: Materialized Views
- Materialized View là gì?
- Khi nào dùng Materialized View?
- Refresh strategies
- **Production Story**: Report query từ 30s → 0.5s với Materialized View

#### Day-058: Partitioning - Concept
- Partitioning là gì?
- Tại sao cần partitioning?
- Partition pruning
- **Production Story**: Query nhanh hơn 100x nhờ partitioning

#### Day-059: Common Performance Anti-patterns
- N+1 queries
- Cartesian products
- Unnecessary DISTINCT
- **Production Story**: Tổng hợp các lỗi performance thường gặp

#### Day-060: Review Phase 3 - Performance Mastery
- Tổng hợp optimization techniques
- Bài tập debug performance
- Chuẩn bị cho Phase 4

---

### **PHASE 4: TRANSACTIONS & CONCURRENCY** (Day-061 → Day-075)

**Mục đích**: Hiểu sâu về transaction, isolation levels, locking, concurrency control.

#### Day-061: Transaction - Deep Dive
- Transaction lifecycle
- Savepoints
- Nested transactions (nếu hỗ trợ)
- **Production Story**: Transaction rollback do lỗi business logic

#### Day-062: Isolation Levels - READ UNCOMMITTED
- Isolation Level là gì?
- READ UNCOMMITTED là gì?
- Dirty Read là gì?
- **Production Story**: Đọc dữ liệu chưa commit → bug

#### Day-063: Isolation Levels - READ COMMITTED
- READ COMMITTED là gì?
- Non-repeatable Read là gì?
- **Production Story**: Inconsistent data do READ COMMITTED

#### Day-064: Isolation Levels - REPEATABLE READ
- REPEATABLE READ là gì?
- Phantom Read là gì?
- **Production Story**: Phantom records trong report

#### Day-065: Isolation Levels - SERIALIZABLE
- SERIALIZABLE là gì?
- Khi nào dùng SERIALIZABLE?
- Performance impact
- **Production Story**: Deadlock do SERIALIZABLE

#### Day-066: Lock - Row-level Lock
- Lock là gì?
- Row-level lock là gì?
- SELECT FOR UPDATE
- **Production Story**: Race condition do thiếu lock

#### Day-067: Lock - Table-level Lock
- Table lock là gì?
- SHARE LOCK vs EXCLUSIVE LOCK
- **Production Story**: Table lock làm toàn bộ app chậm

#### Day-068: Deadlock - Hiểu và xử lý
- Deadlock là gì?
- Tại sao deadlock xảy ra?
- Cách tránh deadlock
- **Production Story**: Deadlock trong production và cách fix

#### Day-069: MVCC (Multi-Version Concurrency Control)
- MVCC là gì?
- Versioning trong MVCC
- **Production Story**: Hiểu tại sao PostgreSQL không cần READ UNCOMMITTED

#### Day-070: Long-running Transactions
- Vấn đề của long-running transaction
- Lock duration
- **Production Story**: Transaction 10 phút làm block toàn bộ system

#### Day-071: Lock Contention
- Lock contention là gì?
- Cách detect lock contention
- **Production Story**: High lock wait time trong production

#### Day-072: Optimistic vs Pessimistic Locking
- Optimistic locking là gì?
- Pessimistic locking là gì?
- Khi nào dùng gì?
- **Production Story**: Conflict resolution với optimistic locking

#### Day-073: Transaction Best Practices
- Keep transactions short
- Avoid locks in transactions
- Error handling trong transactions
- **Production Story**: Tổng hợp best practices từ production

#### Day-074: Read Replicas & Consistency
- Read replica là gì?
- Read-after-write consistency
- **Production Story**: Đọc stale data từ read replica

#### Day-075: Review Phase 4 - Concurrency Mastery
- Tổng hợp transaction & concurrency
- Bài tập về deadlock, lock contention
- Chuẩn bị cho Phase 5

---

### **PHASE 5: PRODUCTION SQL & INTERVIEW PATTERNS** (Day-076 → Day-100+)

**Mục đích**: Áp dụng kiến thức vào production scenarios và interview questions.

#### Day-076: DDL - CREATE TABLE
- CREATE TABLE syntax
- Constraints (PRIMARY KEY, FOREIGN KEY, CHECK, UNIQUE, NOT NULL)
- Default values
- **Production Story**: Thiết kế table cho hệ thống e-commerce

#### Day-077: DDL - ALTER TABLE
- ALTER TABLE ADD/DROP/MODIFY column
- ALTER TABLE ADD constraint
- Online DDL (nếu hỗ trợ)
- **Production Story**: Migrate schema không downtime

#### Day-078: DDL - DROP TABLE, TRUNCATE, DELETE
- DROP TABLE vs TRUNCATE vs DELETE
- CASCADE options
- **Production Story**: Xóa nhầm dữ liệu production (và cách phòng tránh)

#### Day-079: DML - INSERT
- INSERT single row, multiple rows
- INSERT ... ON CONFLICT (UPSERT)
- INSERT ... RETURNING
- **Production Story**: Bulk insert optimization

#### Day-080: DML - UPDATE
- UPDATE với WHERE clause
- UPDATE với JOIN
- UPDATE ... RETURNING
- **Production Story**: Update 1 triệu rows an toàn

#### Day-081: DML - DELETE
- DELETE với WHERE clause
- DELETE với JOIN
- Soft delete pattern
- **Production Story**: Xóa dữ liệu lớn không lock table

#### Day-082: Stored Procedures - Giới thiệu
- Stored Procedure là gì?
- Khi nào dùng Stored Procedure?
- **Production Story**: Business logic trong database vs application

#### Day-083: Functions - User-defined Functions
- Function là gì?
- Function vs Stored Procedure
- **Production Story**: Reusable logic với functions

#### Day-084: Triggers - Database Triggers
- Trigger là gì?
- BEFORE vs AFTER trigger
- Khi nào dùng trigger?
- **Production Story**: Audit log với trigger

#### Day-085: Views - Virtual Tables
- View là gì?
- View vs Table
- Updatable views
- **Production Story**: Security với views

#### Day-086: Backup & Restore - Concept
- Backup strategies
- Point-in-time recovery
- **Production Story**: Restore database sau khi xóa nhầm

#### Day-087: Monitoring - Slow Query Log
- Slow query log là gì?
- Cách identify slow queries
- **Production Story**: Tìm và fix top 10 slow queries

#### Day-088: Monitoring - Query Metrics
- Execution time
- Rows examined vs rows returned
- **Production Story**: Query scan 1M rows nhưng chỉ return 10 rows

#### Day-089: SQL Injection - Security
- SQL Injection là gì?
- Cách phòng tránh (parameterized queries)
- **Production Story**: Security breach do SQL injection

#### Day-090: Data Quality - NULL Handling
- NULL best practices
- COALESCE, NULLIF
- **Production Story**: Bug do NULL không được xử lý đúng

#### Day-091: Data Quality - Data Validation
- CHECK constraints
- Data type validation
- **Production Story**: Invalid data vào database

#### Day-092: Interview Pattern - Top N per Group
- Pattern: Lấy top N records mỗi group
- ROW_NUMBER() vs RANK()
- **Production Story**: Top 3 sản phẩm bán chạy mỗi category

#### Day-093: Interview Pattern - Running Totals
- Running totals với Window Functions
- Performance optimization
- **Production Story**: Financial report với running balance

#### Day-094: Interview Pattern - Gap Analysis
- Tìm gaps trong dữ liệu
- LAG/LEAD patterns
- **Production Story**: Tìm missing dates trong time series

#### Day-095: Interview Pattern - Self JOIN
- Self JOIN là gì?
- Khi nào dùng self JOIN?
- **Production Story**: Hierarchical data (employee-manager)

#### Day-096: Interview Pattern - Pivot/Unpivot
- Pivot data (rows → columns)
- Unpivot data (columns → rows)
- **Production Story**: Report format với pivot

#### Day-097: Interview Pattern - Recursive Queries
- Recursive CTE
- Hierarchical queries
- **Production Story**: Organization tree với recursive CTE

#### Day-098: Interview Pattern - Complex Joins
- Multiple JOINs với complex conditions
- JOIN optimization
- **Production Story**: Data warehouse query với 10+ JOINs

#### Day-099: Interview Pattern - Data Deduplication
- Tìm và xóa duplicates
- Keep one, delete others
- **Production Story**: Cleanup duplicate users

#### Day-100: Final Review - Senior SQL Engineer Checklist
- Tổng hợp toàn bộ kiến thức
- Senior SQL Engineer skills checklist
- Bài tập tổng hợp cuối cùng
- Interview preparation guide

---

## 📚 CẤU TRÚC MỖI DAY

Mỗi Day gồm 3 files:

```
Day-XXX-[Topic]/
├── theory.md      # Lý thuyết chi tiết (4-question framework)
├── exercises.md   # Bài tập thực hành
└── solutions.md   # Giải thích solutions
```

### **theory.md** phải có:
1. **Nó là gì?** - Định nghĩa rõ ràng
2. **Tại sao tồn tại?** - Lý do thiết kế
3. **Khi nào dùng trong production?** - Use cases thực tế
4. **Hậu quả nếu dùng sai / không dùng?** - Trade-offs và risks
5. **Production Story** - Câu chuyện thực tế (nếu có)
6. **Ví dụ SQL** - Code examples
7. **So sánh với alternatives** - Senior thinking

### **exercises.md** phải có:
1. Bài tập từ dễ đến khó
2. Bài tập kiểm tra hiểu biết
3. Bài tập refactor SQL xấu
4. Bài tập đọc và phân tích query
5. Bài tập performance optimization

### **solutions.md** phải có:
1. Giải thích tại sao đúng
2. Giải thích tại sao nhanh/chậm
3. Cách khác có thể làm
4. Lỗi thường gặp
5. Lưu ý production

---

## 🎯 TIÊU CHUẨN CHẤT LƯỢNG

Mỗi Day PHẢI đạt:

✅ **Giải thích bằng tiếng Việt** (chỉ SQL keywords và database terms bằng tiếng Anh)

✅ **4-Question Framework** đầy đủ

✅ **Production Stories** hợp lý, thực tế

✅ **Trade-offs** và so sánh

✅ **Senior-level thinking** - không chỉ syntax

✅ **Exercises** có độ khó phù hợp

✅ **Solutions** giải thích sâu, không chỉ đáp án

---

## 🚀 CÁCH SỬ DỤNG

1. **Bắt đầu**: Yêu cầu "DAY-001" để bắt đầu từ Day đầu tiên
2. **Tiếp tục**: Sau mỗi Day, gõ "NEXT" hoặc "DAY-XXX" để tiếp tục
3. **Nhảy cóc**: Có thể yêu cầu Day cụ thể nếu đã nắm vững phần trước
4. **Review**: Quay lại Day trước nếu cần ôn tập

---

## 📝 LƯU Ý

- **Không giới hạn số Day**: Chương trình có thể mở rộng thêm
- **Độ sâu > Tốc độ**: Quan trọng là hiểu sâu, không phải học nhanh
- **SQL-only**: Không có ORM, không có application code
- **Production-focused**: Mọi concept đều gắn với production scenarios

---

**Sẵn sàng bắt đầu? Gõ "DAY-001" để bắt đầu!** 🚀

