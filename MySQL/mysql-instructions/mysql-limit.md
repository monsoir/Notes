# MySQL - LIMIT

## 限定结果集

```sql
SELECT common_name, scientific_name, family_id
FROM birds
WHERE family_id IN(103, 160, 162, 164)
AND common_name != ''
ORDER BY common_name
LIMIT 3, 2;
```

上面语句显示筛选排序后的，从第三行开始，选取两行显示

```sh
LIMIT start, rows;
```

```sh
LIMIT 0, 3;

# 或

LIMIT 3;
```

从第一行开始，选取3行

