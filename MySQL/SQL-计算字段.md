# SQL-计算字段

[TOC]

部分数据，并没有存储在数据表中，但需要在读取的同时，顺便计算出来，这个时候，就需要计算字段

例如，数据表中只有单价和数量两个列，但需要在读取单价和数量的同时计算出总价，此时，可以使用计算字段来实现

计算字段的工作，其实可以在客户端处完成，但数据库服务器完成这些工作，一般会比较快

## 拼接 concatenate

将值连结到一起，构成单个值

## 拼接的操作符

在 Oracle, PostgreSQL, SQLite 中，使用 `||`

在 MySQL, MariaDB 中，需要用到特殊的函数 Concat，将字符串的各个对应参数用 `,` 隔开

```sql
SELECT vend_name || '(' || vend_country || ')'
FROM Vendors
ORDER BY vend_name;
```

从 Vendors 表中，读取 vend_name 和 vend_country 列的值，并使用左右括号等字符将两个值拼接起来，组成一个字符串值，形成一个新列（这就是一个计算字段）

部分数据库可能会在结果中添加若干个字符来形成等宽的字符串，但我们往往不需要，因此需要手动去除

```sql
SELECT RTRIM(vend_name) || '(' || RTRIM(vend_country) || ')'
FROM Vendors
ORDER BY vend_name;
```

函数名 | 作用
----  | ---
LTRIM | 去掉字符串左边的空格
TRIM | 去掉字符串两边的空格
RTRIM | 去掉字符串右边的空格

## 使用别名

实际上，上面列子得出来的计算字段，并没有命名，因此，在客户端处不能引用到，通过别名 alias (`AS` 操作符)，可以为其命名

```sql
-- Oracle, PostgreSQL, SQLite
SELECT RTRIM(vend_name) || '(' || RTRIM(vend_country) || ')' AS vend_title
FROM Vendors
ORDER BY vend_name;
```

```sql
-- MySQL, MariaDB
SELECT Concat(vend_name, '(', vend_country, ')') AS vend_title
FROM Vendors
ORDER BY vend_name;
```

> 使用别名，是一条 最佳实践

## 执行算数计算

利用计算字段，可以执行简单的算术计算

```sql
SELECT prod_id, quantity, item_price, quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;
```

OrderItems 表中存在 prod_id, quantity, item_price 列
从这3列中读取数据，并计算出 quantity*item_price 这个新列
同时，命名为 expanded_price

---


