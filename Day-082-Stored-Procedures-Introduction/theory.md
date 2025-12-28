# Day-082: Stored Procedures - Giới thiệu

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- Stored Procedure là gì?
- Khi nào dùng Stored Procedure?
- Stored Procedure vs Application Logic
- Trade-offs và best practices

---

## 1️⃣ STORED PROCEDURE LÀ GÌ?

**Stored Procedure** là **code được lưu trữ trong database** và có thể được gọi từ application:

```sql
-- Tạo stored procedure
CREATE OR REPLACE PROCEDURE create_order(
  p_user_id INTEGER,
  p_product_id INTEGER,
  p_quantity INTEGER
)
LANGUAGE plpgsql
AS $$
DECLARE
  v_price DECIMAL(10, 2);
  v_total DECIMAL(10, 2);
BEGIN
  -- Lấy price
  SELECT price INTO v_price FROM products WHERE id = p_product_id;
  
  -- Tính total
  v_total := v_price * p_quantity;
  
  -- Insert order
  INSERT INTO orders (user_id, product_id, quantity, total)
  VALUES (p_user_id, p_product_id, p_quantity, v_total);
  
  COMMIT;
END;
$$;

-- Gọi stored procedure
CALL create_order(1, 100, 2);
```

**Đặc điểm:**
- Code được lưu trong database
- Có thể nhận parameters
- Có thể return values
- Có thể có transaction logic

---

## 2️⃣ TẠI SAO TỒN TẠI STORED PROCEDURE?

**Stored Procedure tồn tại để:**
- **Encapsulate logic**: Đóng gói business logic trong database
- **Performance**: Giảm round-trips, execute gần data
- **Security**: Control access, prevent SQL injection
- **Consistency**: Đảm bảo logic nhất quán

**Nếu không có Stored Procedure:**
- Logic phải ở application → nhiều round-trips
- Logic có thể không nhất quán giữa applications
- Khó control access

---

## 3️⃣ KHI NÀO DÙNG STORED PROCEDURE?

**Dùng khi:**
- **Complex business logic**: Logic phức tạp cần nhiều queries
- **Performance critical**: Cần giảm round-trips
- **Security**: Cần control access, prevent SQL injection
- **Consistency**: Cần đảm bảo logic nhất quán

**KHÔNG nên dùng khi:**
- **Simple queries**: Queries đơn giản
- **Application logic**: Logic thuộc về application
- **Portability**: Cần portability giữa databases
- **Version control**: Khó version control

---

## 4️⃣ STORED PROCEDURE VS APPLICATION LOGIC

**Stored Procedure:**
- ✅ Performance tốt (ít round-trips)
- ✅ Security (control access)
- ✅ Consistency
- ❌ Khó test
- ❌ Khó version control
- ❌ Database-specific

**Application Logic:**
- ✅ Dễ test
- ✅ Dễ version control
- ✅ Portable
- ❌ Nhiều round-trips
- ❌ Logic có thể không nhất quán

**Best practice:**
- **Database logic**: Dùng Stored Procedure (data integrity, performance)
- **Business logic**: Dùng Application Logic (testability, maintainability)

---

## 5️⃣ PRODUCTION STORY: BUSINESS LOGIC TRONG DATABASE VS APPLICATION

**Context:**
Team debate: Nên đặt business logic ở đâu? Database hay Application?

**Problem:**
- Logic ở application → nhiều round-trips → chậm
- Logic ở database → khó test, khó maintain

**Investigation:**
- Application logic: 10 round-trips cho 1 order → 100ms
- Stored procedure: 1 call → 10ms

**Root Cause:**
- Logic phức tạp cần nhiều queries
- Nhiều round-trips làm chậm

**Fix:**

**Option 1: Stored Procedure (cho performance-critical)**
```sql
CREATE OR REPLACE PROCEDURE create_order_complex(
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
  -- Tạo order
  INSERT INTO orders (user_id, status, total)
  VALUES (p_user_id, 'pending', 0)
  RETURNING id INTO v_order_id;
  
  -- Insert order items
  FOR v_item IN SELECT * FROM jsonb_array_elements(p_items)
  LOOP
    INSERT INTO order_items (order_id, product_id, quantity, price)
    VALUES (
      v_order_id,
      (v_item->>'product_id')::INTEGER,
      (v_item->>'quantity')::INTEGER,
      (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER)
    );
    
    v_total := v_total + (SELECT price FROM products WHERE id = (v_item->>'product_id')::INTEGER) * (v_item->>'quantity')::INTEGER;
  END LOOP;
  
  -- Update total
  UPDATE orders SET total = v_total WHERE id = v_order_id;
  
  COMMIT;
END;
$$;
```

**Option 2: Application Logic (cho maintainability)**
```python
# Application code
def create_order(user_id, items):
    with transaction.atomic():
        order = Order.objects.create(user_id=user_id, status='pending', total=0)
        
        total = 0
        for item in items:
            product = Product.objects.get(id=item['product_id'])
            OrderItem.objects.create(
                order_id=order.id,
                product_id=product.id,
                quantity=item['quantity'],
                price=product.price
            )
            total += product.price * item['quantity']
        
        order.total = total
        order.save()
```

**Result:**
- Stored Procedure: 10ms (1 round-trip)
- Application Logic: 100ms (10 round-trips)

**Decision:**
- **Performance-critical operations**: Stored Procedure
- **Business logic**: Application Logic
- **Hybrid approach**: Stored Procedure cho data operations, Application Logic cho business rules

**Lesson Learned:**
- Dùng Stored Procedure cho performance-critical, data-intensive operations
- Dùng Application Logic cho business logic, testability
- Balance giữa performance và maintainability

---

## 6️⃣ SO SÁNH: STORED PROCEDURE vs APPLICATION LOGIC

**Query A: Application Logic (10 round-trips)**
```python
# 10 queries
order = create_order()
for item in items:
    product = get_product(item.id)
    create_order_item(order.id, product.id, item.quantity)
update_order_total(order.id)
```

**Query B: Stored Procedure (1 round-trip)**
```sql
CALL create_order_complex(user_id, items);
```

**So sánh:**

| Aspect | Application Logic | Stored Procedure |
|--------|------------------|------------------|
| **Round-trips** | 10 | 1 |
| **Performance** | ~100ms | ~10ms |
| **Testability** | ✅ Dễ test | ❌ Khó test |
| **Version control** | ✅ Dễ | ❌ Khó |
| **Maintainability** | ✅ Dễ | ❌ Khó |
| **Portability** | ✅ Portable | ❌ Database-specific |

**Kết luận:**
- Stored Procedure: Performance tốt hơn
- Application Logic: Maintainability tốt hơn
- Chọn dựa trên requirements

---

## 7️⃣ TÓM TẮT

**Key Takeaways:**
1. **Stored Procedure**: Code lưu trong database, giảm round-trips
2. **Khi nào dùng**: Performance-critical, complex logic, security
3. **vs Application Logic**: Trade-off giữa performance và maintainability
4. **Best practice**: Hybrid approach - Stored Procedure cho data operations, Application Logic cho business logic

---




**Chuẩn bị cho [Day-083: Functions-User-defined](../Day-083-Functions-User-defined/theory.md)** 🚀
