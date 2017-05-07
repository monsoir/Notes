# 联结表

联结多个表返回一组输出，联结在运行时关联表中正确的行
DBMS 根据 `SELECT` 语句的需要建立联结，在查询期间一直存在

为什么？

- 数据分表存储的代价，导致简单的 `SELECT` 语句并不能检索出所有数据

## 创建联结

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;
```

> 笛卡尔积(cartesian product)

> 有没有联结条件的表关系返回的结果成为笛卡尔积。检索出的行数是 1st 表中的行数乘 2nd 表中的行数

这里使用了 `WHERE` 建立了联结

`FROM` 计算出了 Vendors 表与 Products 表的笛卡尔积，并通过了 `WHERE` 过滤了错误的联结

> 错误的联结

> 笛卡尔积，用 1st 表中的行与 2nd 表中的行结合，而且是那种 1st 表中的每一行跟 2nd 表中的每一行结合。因此，得出来的结果，就含有了 Vendors.vend_id 与 Products.vend_id 不相等的结果，这些结果，在这里（其实是我自己定义的）称为 错误的联结，准确定，是逻辑上的错误

## 内联结

上面例子中的联结，称为等值联结(equijoin)，是基于两个表之间的相等测试。也称为 **内联结(inner join)**

内联结有其特定的语法

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id = Products.vend_id;
```

规范来说的话，使用 `INNER JOIN` 会更好，但还是要看使用者的习惯


### 更多联结的例子

```sql
SELECT prod_name, vend_name, prod_price, quantity
FROM OrderItems, Products, Vendors
WHERE Products.vend_id = Vendors.vend_id
AND OrderItems.prod_id = Products.prod_id
AND order_num = 20007;
```

## 性能

DBMS 在运行时关联指定每个表来处理联结，这种处理可能非常耗资源。尽量不要关联不必要的表，关联的表越多，性能下降越厉害

