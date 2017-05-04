# 聚集函数 Aggregate function

- 为什么需要？
	- 汇总表的数据，而不是实际数据本身

- 做什么
	- 对某些行运行的函数，计算并返回一个值

- [SQL聚集函数们](#sql聚集函数们)
- [AVG()](#avg)
- [COUNT()](#count)
- [MAX()](#max)
- [MIN()](#min)
- [SUM()](#sum)
- [DISTINCT()](#distinct)
- [使用多个聚集函数](#使用多个聚集函数)

## SQL聚集函数们

函数 | 说明
---- | ----
AVG() | 返回某列的平均值
COUNT() | 返回某列的行数
MAX() |	 返回某列的最大值
MIN() | 返回某列的最小值
SUM() |	返回某列值之和

## AVG()

对表中行数计数并计算其列值之和，求得改列的平均值

```sql
SELECT AVG(prod_price) AS avg_price
FROM Products
WHERE vend_id = 'DLL01';
```

只能用来确定特定数值列的平均值，，列名必须作为函数参数给出

求多个列的平均值，必须用多个 `AVG()` 函数

`AVG()` 函数忽略值为 `NULL` 的行

## COUNT()

两种使用方式

- 使用 `COUNT(*)` 对表中行的数目进行技术，**包括空值(NULL)和非空值**
- 使用 `COUNT(column)` 对特定列中具有值的行进行计算，忽略空值


```sql
--- example 1
SELECT COUNT(*) AS num_cust
FROM Customers;


--- example 2
SELECT COUNT(cust_email) AS num_cust
FROM Customers;
```

## MAX()

```sql
--- example
SELECT MAX(prod_price) AS max_price
FROM Products;
```

`MAX()` 一般用于找出**最大的数值**或**日期值**，但多数 DBMS 也支持查找任意列的最大值，如文本。当用于文本时，`MAX()` 返回改列排序后的最后一行

`MAX()` 忽略空值(NULL)


## MIN()

```sql
--- example
SELECT MIN(prod_price) AS min_price
FROM Products;
```

`MIN()` 函数用法同理于 `MAX()`，当用于文本时，返回改列排序后的最前面的行

`MIN()` 忽略空值(NULL)

## SUM()

计算单个列的数值之和

```sql
SELECT SUM(quantity) AS items_ordered
FROM OrderItems
WHERE order_num = 20005;
```

计算多个列的合计值

```sql
SELECT SUM(item_price*quantity) AS items_ordered
FROM OrderItems
WHERE order_num = 20005;
```

**所有聚集函数，都能执行多个列上的计算，像上面的列子**

`SUM()` 忽略空值(NULL)

## DISTINCT

- 对所有行执行计算，指定 `ALL` 参数或者不指定参数(ALL 参数是默认行为)
- 只计算不同的值，指定 `DISTINCT` 参数

⬆️ 即：可能有多个相同的数据，此时，使用了 `DISTINCT` 后，则相同的数据，计算时只会使用一次；否则，则全部都用语计算

```sql
-- 这里，如果 prod_price 有多个相同的值的话，则只会使用一次，因此，这里平均值可能会提供，因为排出了多个最小值
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM Products
WHERE vend_id = 'DLL01';
```

`DISTINCT` 不能用于 `COUNT(*)`，可以用于 `COUNT(column)`，这道理上冲突啊

`DISTINCT` 可以用在 `MIN()` 和 `MAX()` 上，只是，没什么意义

## 使用多个聚集函数

```sql
SELECT COUNT(*) AS num_items,
       MIN(prod_price) AS price_min,
       MAX(prod_price) AS price_max,
       AVG(prod_price) AS price_avg
FROM Products;
```

使用聚集函数时，推荐使用 别名

聚集函数高效，一般比客户端中处理得要快

