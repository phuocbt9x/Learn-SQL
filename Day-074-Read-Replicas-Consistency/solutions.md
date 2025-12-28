# Day-074: Solutions - Read Replicas & Consistency

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Read Replicas

**Read replica:** Copy database cho reads.

**Read-after-write:** Vấn đề consistency khi read từ replica.

**Eventual consistency:** Replica sẽ có data sau một thời gian.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Thiết kế Read Replica Strategy

**Strategy:**
- Write → Primary
- Read → Replica (nếu không cần immediate consistency)
- Read → Primary (nếu cần immediate consistency)

---

**Chúc mừng hoàn thành Day-074!** 🎉
