# Day-086: Solutions - Backup & Restore - Concept

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Backup là gì?

**Backup:** Copy dữ liệu để phục hồi khi có sự cố.

**Tại sao cần:** Disaster recovery, data loss prevention, compliance.

**Khi nào cần:** Production databases, critical data, compliance.

**Hậu quả nếu không có:** Mất data vĩnh viễn, không thể phục hồi.

---

### Câu 1.2: Backup Strategies

**Full backup:** Backup toàn bộ, chậm, tốn storage.

**Incremental backup:** Chỉ backup thay đổi, nhanh hơn.

**Point-in-time recovery:** Restore đến thời điểm cụ thể.

**RTO:** Recovery Time Objective - thời gian restore.

**RPO:** Recovery Point Objective - data loss tối đa.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Thiết kế Backup Strategy

**Solution:**

**E-commerce (Critical):**
- Full backup: Hàng ngày
- Incremental: Mỗi 6 giờ
- Transaction log: Real-time
- RTO: 1 giờ
- RPO: 15 phút

**Blog (Normal):**
- Full backup: Hàng tuần
- Incremental: Hàng ngày
- RTO: 4 giờ
- RPO: 24 giờ

**Analytics (Important):**
- Full backup: Hàng ngày
- Incremental: Mỗi 12 giờ
- RTO: 2 giờ
- RPO: 1 giờ

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Disaster Recovery Plan

**Solution:**

**Backup Strategy:**
- Full backup: Hàng ngày (2 AM)
- Incremental: Mỗi 6 giờ
- Transaction log: Real-time
- Retention: 30 ngày

**Restore Procedure:**
1. Identify point in time
2. Restore full backup gần nhất
3. Apply incremental backups
4. Apply transaction logs
5. Verify data

**Testing Plan:**
- Test restore hàng tháng
- Document procedure
- Train team

**Monitoring:**
- Monitor backup success
- Alert on failure
- Track RTO/RPO

---

**Chúc mừng hoàn thành Day-086!** 🎉

**Chuẩn bị cho Day-087: Monitoring - Slow Query Log** 🚀

