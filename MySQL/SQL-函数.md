# SQL-函数

[TOC]

> 各个 DBMS 的 SQL 函数不相同，因此 SQL 函数是不可移植的

大多支持的函数：

- 处理文本字符串
	- 删除，填充值，转换大小写
- 在数值数据上进行算术操作
	- 绝对值，代数运算
- 处理日期和时间值，并从这些值中提取特定成分
	- 返回两个日期之差，检查日期有效性

## 文本处理函数

```sql
-- 文本转大写
SELECT vend_name, UPPER(vend_name) AS vend_name_upcase
FROM Vendors
ORDER BY vend_name;
```

常用文本处理函数

函数 | 说明
---- | ----
LEFT(<处理的字符串>, <返回的字符数>) | 返回字符串左边的字符
LENGTH / DATALENGTH / LEN(<处理的字符串>) | 返回字符串的长度
LOWER(<处理的字符串>) | 将字符串转换为小写
LTRIM(<处理的字符串>) | 去掉字符串左边的空格
RIGHT(<处理的字符串>, <返回的字符数>) | 返回字符串右边的字符
RTRIM(<处理的字符串>) | 去掉字符串右边的空格
SOUNDEX(<处理的字符串>) | 返回字符串的 SOUNDEX 值
UPPER(<处理的字符串>) | 将字符串转大写

SOUNDEX 是一个将任何文本字符串转换为描述其语音表示的字母数字模式算法（即，音标吧）

在 SQLite 中，在创建时使用了 SQLite_SOUNDEX 编译时选项，那 SOUNDEX() 函数就可以使用了，SQLite_SOUNDEX 不是默认选项

```sql
SELECT cust_name, cust_contact
FROM Customers
WHERE SOUNDEX(cust_contact) = SOUNDEX('Michael Green');

-- 将会读音与 Michael Green 相近或相同的读音的值，如：Michelle Green
```

## 日期和时间处理函数

> 日期和时间采用相应的数据类型存储在表中，每种 DBMS 都有自己的特殊形式。
> 日期和时间值以特殊的格式存储，以便能快速和有效地排序或过滤，并且节省物理存储空间

从日期中提取年份

```sql
-- MySQL / MariaDB
SELECT order_num
FROM Orders
WHERE YEAR(order_date) = 2012;
```

```sql
SELECT order_num
FROM Orders
WHERE strftime('%Y', order_date) = '2012';
```

由于不同 DBMS 的日期时间函数不同，所以需要查看相应 DBMS 的文档

## 数值处理函数

不同的 DBMS 数值函数是最一致的

函数 | 说明 | 例子
---- | ---  | ---
ABS() | 绝对值 | 
COS() | 角度的余弦 | 
EXP() | e的指数值 | EXP(2) = e^2
PI() | 圆周率
SIN() | 一个角度的正弦
SQRT() | 一个数的平方根
TAN() | 一个数的正切

