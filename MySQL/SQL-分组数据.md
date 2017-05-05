# 分组数据

- [GROUNP BY](#group-by)
- [HAVING](#having)
- [ORDER BY](#order-by)
- [SELECT 子句及其顺序](#select-子句及其顺序)

使用分组，可以将数据分为多个逻辑组，对每个组进行聚集计算

需要用到 `SELECT` 的字句：

- `GROUP BY`
- `HAVING`

## GROUP BY

分组通过 `SELECT` 子句 `GROUP BY` 建立

```sql
SELECT vend_id, COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id;
```

`GROUP BY` 子句，指示 DBMS 按 vend_id 排序并分组数据

这样，会对每个 vend_id 而不是整个表计算 num_prods 一次，同时，对每个分组（而不是整个结果）的结果集进行聚集（就是运用函数统计）

### GROUP BY 使用规定

- `GROUP BY` 子句可以包含任意数目的列，因而可以对分组进行嵌套，更细致地进行数据分组
	- 即， `GROUP BY` 后面可以继续再接 `SELECT` + `GROUP BY` 的那种查询语句
- 如果在 `GROUP BY` 子句中嵌套了分组，数据将在最后指定的分组上进行汇总
	- 即，在建立分组时，指定的所有列都一起计算
- `GROUP BY` 子句中列出的每一列都必须是检索列或有效的表达式（但不能是聚集函数），如果 `SELECT` 中使用表达式，则必须在 `GROUP BY` 子句中指定相同的表达式，不能使用别名
	- 即，`GROUP BY` 的依据，必须是 `SELECT` 中的列，或者使用的表达式，但不能是聚集函数
	- `GROUP BY` 中使用到的表达式，必须和 `SELECT` 中的表达式一样，不能使用别名
- `SELECT` 语句中的每一列，都必须在 `GROUP BY` 中作为分组依据，除聚集函数外
- 如果分组列中，包含具有 NULL 的行，则 NULL 将作为一个分组返回。如果列中有多行 NULL 值，则将它们分为一组
- `GROUP BY` 子句必须出现在 `WHERE` 子句之后，`ORDER BY` 子句之前
- 大多数 SQL 实现不允许 `GROUP BY` 列带有长度可变的数据类型，如文本类型

### Tricks

#### 通过相对位置指定列

部分 SQL 实现可用

`GROUP 2, 1` 按 `SELECT` 中的 2nd 列分组，再按 1st 列分组

## HAVING

过滤分组

基于分组的过滤，而不是基于行，但意思与 `WHERE` 一样

前置语句 | 过滤 | 备注
------- | -----| -----
SELECT | WHERE | 过滤行
GROUP BY | HAVING | 过滤分组

`WHERE` 在数据分组前进行过滤，`HAVING` 在分组后进行过滤，`WHERE` 排除的行的数据，不会参与到 `HAVING` 中的计算 

```sql
-- example
SELECT cust_id, COUNT(*) AS orders
FROM Orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;
```

过滤了统计的 orders < 2 的分组

## ORDER BY

对分组进行排序

ORDER BY 与 GROUP BY 的差别

ORDER BY | GROUP BY
-------- | --------
对产生的输出进行排序 | 对行分组，但输出可能不是分组顺序
任意列都可以使用 | 只可能使用选择列或表示列，而且必须使用每个选择列
不一定需要 | 与聚集函数一起使用列或表达式，则必须使用

使用 `GROUP BY` 的时候，应该使用 `ORDER BY` 给出排列的依据，保证数据正确排序，DBMS 自身的输出排序具有不确定性

```sql
-- example
SELECT order_num, COUNT(*) AS items
FROM OrderItems
GROUP BY order_num
HAVING COUNT(*) >= 3
ORDER BY items, order_num;
```

## SELECT 子句及其顺序

子句 | 说明 | 是否必须使用
---- | ---- | ----------
SELECT | 要返回的列或表达式 | 是
FROM | 从中检索数据的表 | 仅在从表选择数据时使用
WHERE | 行级过滤 | 否
GROUP BY | 分组说明 | 仅在按组计算聚集时使用
HAVING | 组级过滤 | 否
ORDER BY | 输出排序顺序 | 否，但应配合 GROUP BY 一并使用

