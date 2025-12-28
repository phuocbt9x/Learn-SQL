# Day-081: DML - DELETE

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- DELETE với WHERE clause
- DELETE với JOIN
- Soft delete pattern
- Xóa dữ liệu lớn không lock table
- Khi nào dùng cách nào?

---

## 1️⃣ DELETE LÀ GÌ? (REVIEW)

**DELETE** là câu lệnh DML để **xóa rows theo điều kiện**:

```sql
-- Xóa rows theo điều kiện
DELETE FROM users WHERE id = 1;

-- Xóa tất cả rows (NGUY HIỂM!)
DELETE FROM users;
```

**Đặc điểm:**
- Xóa rows theo WHERE clause
- Có thể rollback (DML trong transaction)
- Log từng row → chậm hơn TRUNCATE
- Trigger được fire

**Lưu ý:** Luôn có WHERE clause (trừ khi cố ý xóa tất cả)!

---

## 2️⃣ TẠI SAO TỒN TẠI DELETE?

**DELETE tồn tại để:**
- **Xóa rows có điều kiện**: Xóa rows cụ thể, không phải tất cả
- **Có thể rollback**: An toàn hơn TRUNCATE
- **Trigger được fire**: Có thể log, cleanup, etc.
- **Soft delete**: Có thể implement soft delete pattern

**Nếu không có DELETE:**
- Phải dùng TRUNCATE → mất tất cả data
- Không thể xóa có điều kiện
- Không có rollback capability

---

## 3️⃣ DELETE VỚI WHERE CLAUSE

**DELETE với WHERE clause** xóa rows thỏa mãn điều kiện:

```sql
-- Xóa single row
DELETE FROM users WHERE id = 1;

-- Xóa với điều kiện
DELETE FROM orders 
WHERE status = 'cancelled' AND created_at < CURRENT_DATE - INTERVAL '1 year';

-- Xóa với subquery
DELETE FROM products 
WHERE category_id IN (
  SELECT id FROM categories WHERE status = 'inactive'
);
```

**Khi nào dùng:**
- Xóa rows cụ thể
- Conditional deletes
- Bulk deletes với điều kiện

**Hậu quả nếu thiếu WHERE:**
- Xóa tất cả rows → disaster!
- **LUÔN có WHERE clause** (trừ khi cố ý)

---

## 4️⃣ DELETE VỚI JOIN

**DELETE với JOIN** xóa rows dựa trên data từ table khác:

**PostgreSQL:**
```sql
-- DELETE với JOIN (dùng USING)
DELETE FROM orders o
USING users u
WHERE o.user_id = u.id AND u.status = 'deleted';
```

**MySQL:**
```sql
-- DELETE với JOIN
DELETE o FROM orders o
JOIN users u ON o.user_id = u.id
WHERE u.status = 'deleted';
```

**Khi nào dùng:**
- Xóa dựa trên data từ table khác
- Cleanup orphan records
- Complex delete conditions

---

## 5️⃣ SOFT DELETE PATTERN

**Soft delete** là pattern **không xóa thật** mà đánh dấu deleted:

```sql
-- Thêm column deleted_at
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMP;

-- Soft delete
UPDATE users 
SET deleted_at = CURRENT_TIMESTAMP 
WHERE id = 1;

-- Query (bỏ qua deleted)
SELECT * FROM users WHERE deleted_at IS NULL;

-- Hard delete (nếu cần)
DELETE FROM users WHERE deleted_at IS NOT NULL AND deleted_at < CURRENT_DATE - INTERVAL '1 year';
```

**Lợi ích:**
- Có thể recover
- Audit trail
- Không mất data
- Không ảnh hưởng foreign keys

**Trade-off:**
- Tốn storage
- Cần filter deleted trong queries
- Cần index cho performance

---

## 6️⃣ XÓA DỮ LIỆU LỚN KHÔNG LOCK TABLE

**Vấn đề:**
- DELETE 1 triệu rows → lock table lâu
- Block other queries
- Timeout risk

**Solution: Delete từng batch**

```sql
-- Delete từng batch (10,000 rows)
BEGIN;
  DELETE FROM logs 
  WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
  AND id IN (
    SELECT id FROM logs 
    WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
    ORDER BY id
    LIMIT 10000
  );
  
  -- Check progress
  SELECT COUNT(*) FROM logs WHERE created_at < CURRENT_DATE - INTERVAL '1 year';
  
  COMMIT;
END;

-- Lặp lại cho đến hết
```

**Best practices:**
1. **Batch deletes**: Delete từng batch (10,000-100,000 rows)
2. **Monitor locks**: Check lock time
3. **Progress tracking**: Track progress
4. **Rollback plan**: Có thể rollback nếu sai

---

## 7️⃣ PRODUCTION STORY: XÓA DỮ LIỆU LỚN KHÔNG LOCK TABLE

**Context:**
Cần xóa logs cũ hơn 1 năm (10 triệu rows) không lock table.

**Problem:**
- DELETE tất cả cùng lúc → lock table 2 giờ
- Users không thể access logs
- Application timeout

**Investigation:**
- DELETE 10 triệu rows → lock table lâu
- Block other SELECT queries
- High lock contention

**Root Cause:**
- Delete tất cả cùng lúc
- Không có batching strategy

**Fix:**

**Option 1: Batch DELETE**
```sql
-- Delete từng batch
DO $$
DECLARE
  batch_size INTEGER := 10000;
  total_rows INTEGER;
  deleted_rows INTEGER := 0;
  batch_count INTEGER := 0;
BEGIN
  -- Get total rows
  SELECT COUNT(*) INTO total_rows 
  FROM logs WHERE created_at < CURRENT_DATE - INTERVAL '1 year';
  
  -- Delete từng batch
  WHILE deleted_rows < total_rows LOOP
    BEGIN
      DELETE FROM logs 
      WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
        AND id IN (
          SELECT id FROM logs 
          WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
          ORDER BY id
          LIMIT batch_size
        );
      
      GET DIAGNOSTICS deleted_rows = ROW_COUNT;
      batch_count := batch_count + 1;
      
      COMMIT;
      
      -- Log progress
      RAISE NOTICE 'Batch %: Deleted % rows', batch_count, deleted_rows;
      
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

**Option 2: Partitioning + DROP PARTITION**
```sql
-- Nếu dùng partitioning, có thể DROP partition thay vì DELETE
-- Nhanh hơn nhiều!
ALTER TABLE logs DROP PARTITION logs_2023;
```

**Result:**
- Không lock table lâu
- Users vẫn có thể access
- Delete thành công trong 3 giờ (thay vì 2 giờ lock)

**Lesson Learned:**
- Luôn batch large deletes
- Monitor lock time
- Có progress tracking
- Consider partitioning cho time-based data

---

## 8️⃣ SO SÁNH: DELETE TẤT CẢ vs BATCH DELETE

**Query A: DELETE tất cả**
```sql
DELETE FROM logs 
WHERE created_at < CURRENT_DATE - INTERVAL '1 year';
-- Lock table lâu
```

**Query B: Batch DELETE**
```sql
DELETE FROM logs 
WHERE created_at < CURRENT_DATE - INTERVAL '1 year'
AND id IN (SELECT id FROM logs ... LIMIT 10000);
-- Lock table ngắn
```

**So sánh (10 triệu rows):**

| Aspect | Query A | Query B |
|--------|---------|---------|
| **Lock time** | ~2 giờ | ~0.1 giây per batch |
| **User impact** | ❌ Block users | ✅ Users vẫn access |
| **Timeout risk** | ❌ Cao | ✅ Thấp |
| **Rollback** | ❌ Khó | ✅ Dễ |

**Kết luận:**
- Query B tốt hơn cho production
- Batch delete giảm lock time đáng kể
- Luôn batch large deletes

---

## 9️⃣ TÓM TẮT

**Key Takeaways:**
1. **DELETE**: Xóa rows theo điều kiện
2. **WHERE clause**: Luôn có WHERE (trừ khi cố ý)
3. **JOIN**: Xóa dựa trên data từ table khác
4. **Soft delete**: Pattern không xóa thật, đánh dấu deleted
5. **Batch deletes**: Delete từng batch cho large deletes

---







**Chuẩn bị cho [Day-082: Stored-Procedures-Introduction](../Day-082-Stored-Procedures-Introduction/theory.md)** 🚀
