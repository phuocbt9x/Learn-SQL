# Day-084: Triggers - Database Triggers

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Trigger là gì?
- BEFORE vs AFTER trigger
- Khi nào dùng trigger?
- Audit log với trigger

---

## 1️⃣ TRIGGER LÀ GÌ?

**Trigger** là **code tự động chạy** khi có event xảy ra:

```sql
-- Tạo trigger
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at();
```

**Đặc điểm:**
- Tự động chạy khi có event
- BEFORE hoặc AFTER
- FOR EACH ROW hoặc FOR EACH STATEMENT
- Có thể modify data (BEFORE)

---

## 2️⃣ BEFORE VS AFTER TRIGGER

**BEFORE trigger:**
- Chạy trước khi thực thi statement
- Có thể modify data
- Có thể prevent operation

**AFTER trigger:**
- Chạy sau khi thực thi statement
- Không thể modify data
- Dùng cho logging, notifications

---

## 3️⃣ PRODUCTION STORY: AUDIT LOG VỚI TRIGGER

**Context:**
Cần log mọi thay đổi trên table `users` → audit trail.

**Fix:**
Tạo AFTER trigger → log mọi UPDATE/DELETE → audit trail tự động.

**Result:**
- Audit trail tự động
- Không cần modify application code
- Đảm bảo log đầy đủ

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. **Trigger**: Code tự động chạy khi có event
2. **BEFORE vs AFTER**: BEFORE có thể modify, AFTER cho logging
3. **Khi nào dùng**: Audit log, auto-update timestamps, validation
4. **Best practice**: Dùng cẩn thận, tránh complex logic

---



**Chuẩn bị cho [Day-085: Views](../Day-085-Views/theory.md)** 🚀
