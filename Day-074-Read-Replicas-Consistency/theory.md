# Day-074: Read Replicas & Consistency

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Read replica là gì?
- Read-after-write consistency
- Eventual consistency
- Khi nào dùng read replicas?

---

## 1️⃣ READ REPLICA LÀ GÌ?

**Read replica** là **copy của database** chỉ dùng để đọc:

- **Primary**: Write operations
- **Replica**: Read operations
- **Replication**: Data được replicate từ primary sang replica

**Lợi ích:**
- Scale reads
- Reduce load trên primary
- Better performance

---

## 2️⃣ READ-AFTER-WRITE CONSISTENCY

**Read-after-write consistency:**
- Write vào primary
- Read từ replica
- **Vấn đề**: Replica có thể chưa có data mới

**Solution:**
- Read từ primary sau khi write
- Hoặc wait replication lag

---

## 3️⃣ EVENTUAL CONSISTENCY

**Eventual consistency:**
- Replica sẽ có data mới sau một thời gian
- Không đảm bảo immediate consistency
- Acceptable cho một số use cases

---

## 4️⃣ PRODUCTION STORY: ĐỌC STALE DATA TỪ READ REPLICA

**Context:**
Read từ replica → đọc stale data → user confusion.

**Fix:**
Read từ primary sau khi write → đảm bảo consistency.

---

## 5️⃣ TÓM TẮT

**Key Takeaways:**
1. Read replica: Copy database cho reads
2. Read-after-write: Vấn đề consistency
3. Eventual consistency: Replica sẽ có data sau một thời gian
4. Best practice: Hiểu trade-offs của read replicas

---

**Chuẩn bị cho Day-075: Review Phase 4** 🚀
