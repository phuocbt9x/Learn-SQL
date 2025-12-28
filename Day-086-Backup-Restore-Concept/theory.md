# Day-086: Backup & Restore - Concept

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Backup strategies
- Point-in-time recovery
- RTO và RPO
- Khi nào cần backup?
- Hậu quả nếu không có backup

---

## 1️⃣ BACKUP LÀ GÌ?

**Backup** là **copy dữ liệu** để phục hồi khi có sự cố:

```sql
-- PostgreSQL: pg_dump
pg_dump -U username -d database_name > backup.sql

-- MySQL: mysqldump
mysqldump -u username -p database_name > backup.sql

-- Restore
psql -U username -d database_name < backup.sql
mysql -u username -p database_name < backup.sql
```

**Đặc điểm:**
- Copy toàn bộ hoặc một phần database
- Có thể restore khi cần
- Cần lưu trữ an toàn

---

## 2️⃣ TẠI SAO TỒN TẠI BACKUP?

**Backup tồn tại để:**
- **Disaster recovery**: Phục hồi sau sự cố
- **Data loss prevention**: Tránh mất data
- **Compliance**: Yêu cầu pháp lý
- **Peace of mind**: Yên tâm khi có backup

**Nếu không có backup:**
- Mất data vĩnh viễn khi có sự cố
- Không thể phục hồi
- Rủi ro cao

---

## 3️⃣ BACKUP STRATEGIES

### **Full Backup**

**Full backup** là backup toàn bộ database:

- Backup tất cả data
- Chậm, tốn storage
- Cần cho initial restore

### **Incremental Backup**

**Incremental backup** chỉ backup thay đổi:

- Chỉ backup data mới/thay đổi
- Nhanh hơn, ít storage hơn
- Cần full backup trước

### **Point-in-Time Recovery**

**Point-in-time recovery** restore đến thời điểm cụ thể:

- Restore đến bất kỳ thời điểm nào
- Cần transaction logs
- Phức tạp hơn nhưng linh hoạt

---

## 4️⃣ RTO VÀ RPO

**RTO (Recovery Time Objective):**
- Thời gian tối đa để restore
- Ví dụ: RTO = 1 giờ → phải restore trong 1 giờ

**RPO (Recovery Point Objective):**
- Data loss tối đa chấp nhận được
- Ví dụ: RPO = 15 phút → mất tối đa 15 phút data

**Ví dụ:**
- RTO = 1 giờ, RPO = 15 phút
- → Phải restore trong 1 giờ, mất tối đa 15 phút data
- → Cần backup mỗi 15 phút, restore trong 1 giờ

---

## 5️⃣ KHI NÀO CẦN BACKUP?

**Luôn cần backup:**
- Production databases
- Critical data
- Compliance requirements

**Backup frequency:**
- **Critical**: Real-time hoặc mỗi giờ
- **Important**: Hàng ngày
- **Normal**: Hàng tuần

---

## 6️⃣ PRODUCTION STORY: RESTORE DATABASE SAU KHI XÓA NHẦM

**Context:**
Developer xóa nhầm table `users` trong production → mất 1 triệu users.

**Problem:**
- Không có backup gần đây
- Mất data vĩnh viễn
- Users không thể login

**Investigation:**
- Last backup: 1 tuần trước
- Data loss: 1 tuần data
- Không thể recover

**Root Cause:**
- Không có backup strategy
- Backup không đủ thường xuyên

**Fix:**
1. **Immediate**: Restore từ backup 1 tuần trước
2. **Long-term**: Implement backup strategy:
   - Full backup: Hàng ngày
   - Incremental backup: Mỗi 6 giờ
   - Transaction log backup: Real-time
   - RTO = 1 giờ, RPO = 15 phút

**Result:**
- Restore thành công từ backup
- Mất 1 tuần data (không thể recover)
- Implement backup strategy → không còn mất data

**Lesson Learned:**
- Luôn có backup strategy
- Backup đủ thường xuyên
- Test restore thường xuyên
- Monitor backup success

---

## 7️⃣ TÓM TẮT

**Key Takeaways:**
1. **Backup**: Copy dữ liệu để phục hồi
2. **Strategies**: Full, Incremental, Point-in-time
3. **RTO/RPO**: Recovery objectives
4. **Best practice**: Backup thường xuyên, test restore, monitor

---




**Chuẩn bị cho [Day-087: Monitoring-Slow-Query-Log](Day-087-Monitoring-Slow-Query-Log/theory.md)** 🚀
