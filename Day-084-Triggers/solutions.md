# Day-084: Solutions - Triggers

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Trigger là gì?

**Trigger:** Code tự động chạy khi có event.

**BEFORE vs AFTER:** BEFORE có thể modify, AFTER cho logging.

**Khi nào dùng:** Audit log, auto-update timestamps, validation.

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Trigger

**Solution:**

```sql
-- Auto-update updated_at
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

-- Audit log
CREATE TABLE user_audit_log (
  id SERIAL PRIMARY KEY,
  user_id INTEGER,
  action VARCHAR(20),
  old_data JSONB,
  new_data JSONB,
  changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE OR REPLACE FUNCTION log_user_changes()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'UPDATE' THEN
    INSERT INTO user_audit_log (user_id, action, old_data, new_data)
    VALUES (OLD.id, 'UPDATE', row_to_json(OLD), row_to_json(NEW));
    RETURN NEW;
  ELSIF TG_OP = 'DELETE' THEN
    INSERT INTO user_audit_log (user_id, action, old_data)
    VALUES (OLD.id, 'DELETE', row_to_json(OLD));
    RETURN OLD;
  END IF;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER user_audit_trigger
  AFTER UPDATE OR DELETE ON users
  FOR EACH ROW
  EXECUTE FUNCTION log_user_changes();
```

---

**Chúc mừng hoàn thành Day-084!** 🎉
