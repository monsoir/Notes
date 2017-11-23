# MySQL 中的类型问题

## 类型设置

```sql
CREATE TABLE status_names (status_id INT, status_name CHAR(8));
```

上面使用 `CHAR(8)` 而不是 `TEXT`, 可以限制字符串长度，提高数据库性能，需要在设计表时考虑好

