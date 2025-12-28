# Day-082: Solutions - Stored Procedures - Giới thiệu

## 🎯 BÀI TẬP 1: HIỂU BIẾT CƠ BẢN

### Câu 1.1: Stored Procedure là gì?

**Stored Procedure:** Code được lưu trong database, có thể gọi từ application.

**Tại sao cần:** Encapsulate logic, performance, security, consistency.

**Khi nào dùng:** Complex logic, performance-critical, security, consistency.

**Hậu quả nếu dùng sai:**
- Khó test và maintain
- Database-specific
- Khó version control

---

### Câu 1.2: Stored Procedure vs Application Logic

**Stored Procedure:**
- Pros: Performance tốt, security, consistency
- Cons: Khó test, khó version control, database-specific

**Application Logic:**
- Pros: Dễ test, dễ version control, portable
- Cons: Nhiều round-trips, logic có thể không nhất quán

**Khi nào dùng:**
- Stored Procedure: Performance-critical, data-intensive
- Application Logic: Business logic, testability

---

## 🔍 BÀI TẬP 2: THỰC HÀNH

### Câu 2.1: Tạo Stored Procedure Đơn giản

**Solution:**

```sql
-- Get user orders
CREATE OR REPLACE PROCEDURE get_user_orders(
  p_user_id INTEGER
)
LANGUAGE plpgsql
AS $$
BEGIN
  SELECT * FROM orders WHERE user_id = p_user_id ORDER BY created_at DESC;
END;
$$;

-- Call
CALL get_user_orders(1);

-- Update product price với log
CREATE OR REPLACE PROCEDURE update_product_price(
  p_product_id INTEGER,
  p_new_price DECIMAL(10, 2)
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_old_price DECIMAL(10, 2);
BEGIN
  -- Lấy old price
  SELECT price INTO v_old_price FROM products WHERE id = p_product_id;
  
  -- Update price
  UPDATE products SET price = p_new_price WHERE id = p_product_id;
  
  -- Log change
  INSERT INTO price_changes (product_id, old_price, new_price, changed_at)
  VALUES (p_product_id, v_old_price, p_new_price, CURRENT_TIMESTAMP);
  
  COMMIT;
END;
$$;
```

---

### Câu 2.2: Stored Procedure với Transaction

**Solution:**

```sql
-- Transfer balance
CREATE OR REPLACE PROCEDURE transfer_balance(
  p_from_user_id INTEGER,
  p_to_user_id INTEGER,
  p_amount DECIMAL(10, 2)
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_from_balance DECIMAL(10, 2);
BEGIN
  -- Check balance
  SELECT balance INTO v_from_balance FROM users WHERE id = p_from_user_id;
  
  IF v_from_balance < p_amount THEN
    RAISE EXCEPTION 'Insufficient balance';
  END IF;
  
  -- Transfer
  UPDATE users SET balance = balance - p_amount WHERE id = p_from_user_id;
  UPDATE users SET balance = balance + p_amount WHERE id = p_to_user_id;
  
  -- Log transaction
  INSERT INTO transactions (from_user_id, to_user_id, amount, created_at)
  VALUES (p_from_user_id, p_to_user_id, p_amount, CURRENT_TIMESTAMP);
  
  COMMIT;
EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END;
$$;

-- Create order with items
CREATE OR REPLACE PROCEDURE create_order_with_items(
  p_user_id INTEGER,
  p_items JSONB
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_order_id INTEGER;
  v_item JSONB;
  v_total DECIMAL(10, 2) := 0;
BEGIN
  -- Create order
  INSERT INTO orders (user_id, status, total)
  VALUES (p_user_id, 'pending', 0)
  RETURNING id INTO v_order_id;
  
  -- Insert items
  FOR v_item IN SELECT * FROM jsonb_array_elements(p_items)
  LOOP
    INSERT INTO order_items (order_id, product_id, quantity, price)
    VALUES (
      v_order_id,
      (v_item->>'product_id')::INTEGER,
      (v_item->>'quantity')::INTEGER,
      (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER)
    );
    
    v_total := v_total + 
      (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER) * 
      (v_item->>'quantity')::INTEGER;
  END LOOP;
  
  -- Update total
  UPDATE orders SET total = v_total WHERE id = v_order_id;
  
  COMMIT;
END;
$$;
```

---

## 🎯 BÀI TẬP 3: PRODUCTION SCENARIOS

### Câu 3.1: Performance Optimization

**Solution:**

**Before (Application Logic - 10 round-trips):**
```python
# 10 queries
user = get_user(user_id)
products = [get_product(id) for id in product_ids]  # 5 queries
order = create_order(user_id)
for product in products:
    create_order_item(order.id, product.id, quantity)  # 5 queries
update_order_total(order.id)
```

**After (Stored Procedure - 1 round-trip):**
```sql
CREATE OR REPLACE PROCEDURE create_order_optimized(
  p_user_id INTEGER,
  p_items JSONB
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_order_id INTEGER;
  v_item JSONB;
  v_total DECIMAL(10, 2) := 0;
BEGIN
  -- Create order
  INSERT INTO orders (user_id, status, total)
  VALUES (p_user_id, 'pending', 0)
  RETURNING id INTO v_order_id;
  
  -- Insert items và tính total
  FOR v_item IN SELECT * FROM jsonb_array_elements(p_items)
  LOOP
    INSERT INTO order_items (order_id, product_id, quantity, price)
    SELECT 
      v_order_id,
      (v_item->>'product_id')::INTEGER,
      (v_item->>'quantity')::INTEGER,
      p.price
    FROM products p
    WHERE p.id = (v_item->>'product_id')::INTEGER;
    
    v_total := v_total + 
      (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER) * 
      (v_item->>'quantity')::INTEGER;
  END LOOP;
  
  -- Update total
  UPDATE orders SET total = v_total WHERE id = v_order_id;
  
  COMMIT;
END;
$$;
```

**Performance comparison (Illustrative / approximate for educational purposes):**
- Application Logic: ~100ms (10 round-trips)
- Stored Procedure: ~10ms (1 round-trip)
- **Improvement: 10x faster**

---

### Câu 3.2: Security với Stored Procedure

**Solution:**

```sql
-- Update product price (chỉ admin)
CREATE OR REPLACE PROCEDURE update_product_price_secure(
  p_product_id INTEGER,
  p_new_price DECIMAL(10, 2),
  p_user_id INTEGER
)
LANGUAGE plpgsql
SECURITY DEFINER
AS $$
DECLARE
  v_user_role VARCHAR(20);
BEGIN
  -- Check user role
  SELECT role INTO v_user_role FROM users WHERE id = p_user_id;
  
  IF v_user_role != 'admin' THEN
    RAISE EXCEPTION 'Only admin can update product price';
  END IF;
  
  -- Update price
  UPDATE products SET price = p_new_price WHERE id = p_product_id;
  
  COMMIT;
END;
$$;

-- Cancel order (chỉ owner)
CREATE OR REPLACE PROCEDURE cancel_order_secure(
  p_order_id INTEGER,
  p_user_id INTEGER
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_order_user_id INTEGER;
BEGIN
  -- Check ownership
  SELECT user_id INTO v_order_user_id FROM orders WHERE id = p_order_id;
  
  IF v_order_user_id != p_user_id THEN
    RAISE EXCEPTION 'Only order owner can cancel order';
  END IF;
  
  -- Cancel order
  UPDATE orders SET status = 'cancelled' WHERE id = p_order_id;
  
  COMMIT;
END;
$$;
```

---

## 🚀 BÀI TẬP 4: ADVANCED

### Câu 4.1: Stored Procedure với Error Handling

**Solution:**

```sql
CREATE OR REPLACE PROCEDURE create_order_safe(
  p_user_id INTEGER,
  p_items JSONB
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_order_id INTEGER;
  v_item JSONB;
  v_total DECIMAL(10, 2) := 0;
  v_error_message TEXT;
BEGIN
  BEGIN
    -- Create order
    INSERT INTO orders (user_id, status, total)
    VALUES (p_user_id, 'pending', 0)
    RETURNING id INTO v_order_id;
    
    -- Insert items
    FOR v_item IN SELECT * FROM jsonb_array_elements(p_items)
    LOOP
      BEGIN
        INSERT INTO order_items (order_id, product_id, quantity, price)
        VALUES (
          v_order_id,
          (v_item->>'product_id')::INTEGER,
          (v_item->>'quantity')::INTEGER,
          (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER)
        );
        
        v_total := v_total + 
          (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER) * 
          (v_item->>'quantity')::INTEGER;
      EXCEPTION
        WHEN OTHERS THEN
          -- Log error
          INSERT INTO error_logs (procedure_name, error_message, created_at)
          VALUES ('create_order_safe', SQLERRM, CURRENT_TIMESTAMP);
          RAISE;
      END;
    END LOOP;
    
    -- Update total
    UPDATE orders SET total = v_total WHERE id = v_order_id;
    
    COMMIT;
  EXCEPTION
    WHEN OTHERS THEN
      ROLLBACK;
      v_error_message := SQLERRM;
      
      -- Log error
      INSERT INTO error_logs (procedure_name, error_message, created_at)
      VALUES ('create_order_safe', v_error_message, CURRENT_TIMESTAMP);
      
      RAISE;
  END;
END;
$$;
```

---

### Câu 4.2: Stored Procedure vs Function

**Solution:**

**Stored Procedure:**
```sql
CREATE OR REPLACE PROCEDURE update_status(
  p_id INTEGER,
  p_status VARCHAR(20)
)
LANGUAGE plpgsql
AS $$
BEGIN
  UPDATE orders SET status = p_status WHERE id = p_id;
  COMMIT;
END;
$$;

-- Call
CALL update_status(1, 'processed');
```

**Function:**
```sql
CREATE OR REPLACE FUNCTION update_status(
  p_id INTEGER,
  p_status VARCHAR(20)
)
RETURNS VOID
LANGUAGE plpgsql
AS $$
BEGIN
  UPDATE orders SET status = p_status WHERE id = p_id;
END;
$$;

-- Call
SELECT update_status(1, 'processed');
```

**So sánh:**
- Stored Procedure: Không return value, dùng CALL
- Function: Có thể return value, dùng SELECT

**Khi nào dùng:**
- Stored Procedure: Khi không cần return value
- Function: Khi cần return value, dùng trong queries

---

**Chúc mừng hoàn thành Day-082!** 🎉

**Chuẩn bị cho Day-083: Functions - User-defined Functions** 🚀

