---

### 🚀 New Feature: Byte Size & Time Duration Parsers with `aggregate-stats` Directive

---

#### 📌 Overview

This enhancement adds native support in Wrangler for:

- 📦 **Byte Sizes** like `10KB`, `1.5MB`, `2GB`
- ⏱ **Time Durations** like `150ms`, `2s`, `1min`

It also introduces a new directive:

> `aggregate-stats` — to compute aggregated values (like total size or total time) effortlessly in transformation recipes.

---

---
#### 🛠️ Implementation Summary
---
1. **✅ Grammar Changes** *(in `Directives.g4`)*
   - Added lexer rules:
     - `BYTE_SIZE`, `TIME_DURATION`
   - Added helper fragments:
     - `BYTE_UNIT`, `TIME_UNIT`
   - Updated parser rules to recognize these tokens in directive arguments.

2. **✅ API Additions** *(in `wrangler-api`)*
   - `ByteSize.java`: Parses inputs like `1.5MB`, `200KB` into bytes.
   - `TimeDuration.java`: Parses inputs like `2s`, `150ms` into nanoseconds.
   - Registered both in Wrangler’s token system:
     ```java
     TokenType.BYTE_SIZE
     TokenType.TIME_DURATION
     ```

3. **✅ Core Parser Updates** *(in `wrangler-core`)*
   - Implemented:
     - `visitByteSizeArg`
     - `visitTimeDurationArg`
   - Extracted values using `ctx.getText()` and added to `TokenGroup`.

4. **✅ New Directive: `aggregate-stats`**
---
   **Syntax:**
   ---
  
   ```text
---
   aggregate-stats :<byte_column> :<time_column> <output_byte_column> <output_time_column>
Arguments:
---
:byte_column: Input column with byte size values (e.g. data_transfer_size)

:time_column: Input column with time duration values (e.g. response_time)

:output_byte_column: Output column for aggregated byte size

:output_time_column: Output column for aggregated time
---
✅ Sample Recipe Usage
JAVA
String[] recipe = new String[] {
  "aggregate-stats :data_transfer_size :response_time total_size_mb total_time_sec"
};
---

🧪 Sample Input
data_transfer_size                    	response_time
1.5MB	                                    200ms
2MB	                                      300ms
512KB	                                    100ms
---
🎯 Expected Output
total_size_mb	                         total_time_sec
4.00	                                    0.60
---
✅ Testing Highlights

✔️ Unit tests for ByteSize and TimeDuration classes

✔️ Grammar parser tests for valid and invalid tokens

✔️ Directive-level unit tests using TestingRig.execute(recipe, rows)

✔️ Output assertions via Assert.assertEquals() with tolerance for precision
---
💡 Future Enhancements
---
📏 Output unit customization (e.g., MB → GB, ms → minutes)

📊 Support for statistical aggregations: average, p95, p99


