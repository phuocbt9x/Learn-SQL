# Day-080: DML - UPDATE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- UPDATE với WHERE clause
- UPDATE với JOIN
- UPDATE ... RETURNING
- Update 1 triệu rows an toàn
- Khi nào dùng cách nào?

---

## 1️⃣ UPDATE LÀ GÌ?

**UPDATE** là câu lệnh DML để **sửa đổi data** trong table:

```sql
-- Update với WHERE clause
UPDATE users 
SET name = 'New Name', updated_at = CURRENT_TIMESTAMP 
WHERE id = 1;

-- Update nhiều rows
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1;
```

**Đặc điểm:**
- Sửa đổi existing rows
- Có thể rollback (DML trong transaction)
- Trigger được fire
- Constraints được check

---

## 2️⃣ TẠI SAO TỒN TẠI UPDATE?

**UPDATE tồn tại để:**
- **Sửa đổi data**: Update thông tin đã có
- **Bulk updates**: Update nhiều rows cùng lúc
- **Data correction**: Sửa lỗi data
- **Status changes**: Thay đổi trạng thái

**Nếu không có UPDATE:**
- Phải DELETE và INSERT → mất history
- Không thể sửa data đã có
- Phải recreate records

---

## 3️⃣ UPDATE VỚI WHERE CLAUSE

**UPDATE với WHERE clause** update rows thỏa mãn điều kiện:

```sql
-- Update single row
UPDATE users 
SET email = 'newemail@example.com' 
WHERE id = 1;

-- Update với điều kiện
UPDATE products 
SET stock = stock - 1 
WHERE id = 1 AND stock > 0;

-- Update với subquery
UPDATE orders 
SET status = 'cancelled' 
WHERE user_id IN (SELECT id FROM users WHERE status = 'inactive');
```

**Khi nào dùng:**
- Update rows cụ thể
- Conditional updates
- Bulk updates với điều kiện

**Hậu quả nếu thiếu WHERE:**
- Update tất cả rows → disaster!
- Luôn có WHERE clause (trừ khi cố ý update tất cả)

---

## 4️⃣ UPDATE VỚI JOIN

**UPDATE với JOIN** update dựa trên data từ table khác:

**PostgreSQL:**
```sql
-- Update với JOIN
UPDATE orders o
SET total = o.total + p.price
FROM products p
WHERE o.product_id = p.id AND p.category_id = 1;
```

**MySQL:**
```sql
-- Update với JOIN
UPDATE orders o
JOIN products p ON o.product_id = p.id
SET orders.total = orders.total + products.price
WHERE products.category_id = 1;
```

**Khi nào dùng:**
- Update dựa trên data từ table khác
- Sync data giữa tables
- Complex updates

---

## 5️⃣ UPDATE ... RETURNING

**UPDATE ... RETURNING** trả về **rows vừa update**:

```sql
-- Return updated rows
UPDATE users 
SET status = 'active', updated_at = CURRENT_TIMESTAMP 
WHERE id = 1
RETURNING id, email, status, updated_at;

-- Return chỉ id
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1
RETURNING id;
```

**Lợi ích:**
- Không cần query lại để verify
- Giảm round-trips
- Atomic operation

**Khi nào dùng:**
- Cần verify updated data
- Application logic cần updated values
- Audit logging

---

## 6️⃣ UPDATE 1 TRIỆU ROWS AN TOÀN

**Vấn đề:**
- UPDATE 1 triệu rows → lock table lâu
- Block other queries
- Timeout risk

**Solution: Update từng batch**

```sql
-- Update từng batch (10,000 rows)
BEGIN;
  UPDATE products 
  SET price = price * 1.1 
  WHERE category_id = 1 AND id BETWEEN 1 AND 10000;
  
  -- Check progress
  SELECT COUNT(*) FROM products WHERE category_id = 1 AND price_updated = false;
  
  COMMIT;
END;

-- Lặp lại cho đến hết
```

**Best practices:**
1. **Batch updates**: Update từng batch (10,000-100,000 rows)
2. **Monitor locks**: Check lock time
3. **Progress tracking**: Track progress
4. **Rollback plan**: Có thể rollback nếu sai

---

## 7️⃣ PRODUCTION STORY: UPDATE 1 TRIỆU ROWS AN TOÀN

**Context:**
Cần update giá 1 triệu products (tăng 10%) không lock table lâu.

**Problem:**
- UPDATE tất cả cùng lúc → lock table 30 phút
- Users không thể access products
- Application timeout

**Investigation:**
- UPDATE 1 triệu rows → lock table lâu
- Block other SELECT queries
- High lock contention

**Root Cause:**
- Update tất cả cùng lúc
- Không có batching strategy

**Fix:**

**Option 1: Batch UPDATE**
```sql
-- Update từng batch
DO $$
DECLARE
  batch_size INTEGER := 10000;
  total_rows INTEGER;
  updated_rows INTEGER := 0;
BEGIN
  -- Get total rows
  SELECT COUNT(*) INTO total_rows 
  FROM products WHERE category_id = 1;
  
  -- Update từng batch
  WHILE updated_rows < total_rows LOOP
    BEGIN
      UPDATE products 
      SET price = price * 1.1,
          updated_at = CURRENT_TIMESTAMP
      WHERE category_id = 1 
        AND (updated_at IS NULL OR updated_at < CURRENT_TIMESTAMP - INTERVAL '1 minute')
      LIMIT batch_size;
      
      GET DIAGNOSTICS updated_rows = ROW_COUNT;
      
      COMMIT;
      
      -- Wait để giảm lock contention
      PERFORM pg_sleep(0.1);
    EXCEPTION
      WHEN OTHERS THEN
        ROLLBACK;
        RAISE;
    END;
  END LOOP;
END $$;
```

**Option 2: Update với WHERE incremental**
```sql
-- Update từng range
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1 AND id BETWEEN 1 AND 10000;

UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1 AND id BETWEEN 10001 AND 20000;
-- ... tiếp tục
```

**Result:**
- Không lock table lâu
- Users vẫn có thể access
- Update thành công trong 1 giờ (thay vì 30 phút lock)

**Lesson Learned:**
- Luôn batch large updates
- Monitor lock time
- Có progress tracking
- Test trên staging trước

---

## 8️⃣ SO SÁNH: UPDATE TẤT CẢ vs BATCH UPDATE

**Query A: UPDATE tất cả**
```sql
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1;
-- Lock table lâu
```

**Query B: Batch UPDATE**
```sql
UPDATE products 
SET price = price * 1.1 
WHERE category_id = 1 AND id BETWEEN 1 AND 10000;
-- Lock table ngắn
```

**So sánh (1 triệu rows):**

| Aspect | Query A | Query B |
|--------|---------|---------|
| **Lock time** | ~30 phút | ~0.1 giây per batch |
| **User impact** | ❌ Block users | ✅ Users vẫn access |
| **Timeout risk** | ❌ Cao | ✅ Thấp |
| **Rollback** | ❌ Khó | ✅ Dễ |

**Kết luận:**
- Query B tốt hơn cho production
- Batch update giảm lock time đáng kể
- Luôn batch large updates

---

## 9️⃣ TÓM TẮT

**Key Takeaways:**
1. **UPDATE**: Sửa đổi existing rows
2. **WHERE clause**: Luôn có WHERE (trừ khi cố ý)
3. **JOIN**: Update dựa trên data từ table khác
4. **RETURNING**: Lấy updated values
5. **Batch updates**: Update từng batch cho large updates

---

**Chuẩn bị cho Phase 5.2!** 🚀






**Chuẩn bị cho [Day-081: DML-DELETE](../Day-081-DML-DELETE/theory.md)** 🚀
