# Day-035: Date & Time Functions

## 🎯 MỤC TIÊU HỌC TẬP

Sau Day này, bạn sẽ hiểu:
- DATE functions (EXTRACT, DATE_TRUNC, AGE)
- TIMESTAMP arithmetic
- Timezone handling
- Khi nào dùng date/time functions?

---

## 1️⃣ DATE FUNCTIONS

**EXTRACT:**
```sql
SELECT EXTRACT(YEAR FROM created_at) as year FROM orders;
```

**DATE_TRUNC:**
```sql
SELECT DATE_TRUNC('month', created_at) as month FROM orders;
```

**AGE:**
```sql
SELECT AGE(created_at) as age FROM orders;
```

---

## 2️⃣ TIMESTAMP ARITHMETIC

**Add/Subtract:**
```sql
SELECT created_at + INTERVAL '1 day' FROM orders;
SELECT created_at - INTERVAL '1 month' FROM orders;
```

---

## 3️⃣ TIMEZONE HANDLING

**Convert timezone:**
```sql
SELECT created_at AT TIME ZONE 'UTC' FROM orders;
```

---

## 4️⃣ TÓM TẮT

**Key Takeaways:**
1. Date functions: EXTRACT, DATE_TRUNC, AGE
2. Timestamp arithmetic: Add/subtract intervals
3. Timezone: Convert timezones
4. Best practice: Dùng TIMESTAMPTZ cho global apps

---

**Chuẩn bị cho Phase 2.5!** 🚀


**Chuẩn bị cho [Day-036: Window-Functions-Intro](../Day-036-Window-Functions-Intro/theory.md)** 🚀
