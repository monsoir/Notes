# 创建高级联结

## 自联结 self-join

```sql
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM Customer AS c1, Customer AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones';
```

即表对自身进行 笛卡尔积 运算，然后使用 WHERE 语句进行数据过滤

> 多数 DBMS 中，处理联结的速度要不子查询快


## 自然联结 natural join

对表进行联结，总是会有多于一列出现(即 SELECT 的时候，总是会 SELECT 出相同数据的列)，自然联结就是消除这种重复

```sql
SELECT C.*, order_num, O.order_date,
	OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'RGAN01';
```

使用 `*` 没有明确指出列出，因此，不会显示重复的列

## 外联结 outer join

将一个表中的行，与另一个表中的行相关联，并且包含那些没有关联行的行

内联结的一个例子

```sql
SELECT Customer.cust_id, Orders.order_num
FROM Customers INNER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

得出的数据是 Customers 表与 Orders 表中共同含有的表，并且数据不为空

---

外联结

```sql
SELECT Customer.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

得出的数据是 Customers 中所有的数据与 Orders 中不为空的数据

使用 OUTER JOIN 时，须指明 LEFT 或者 RIGHT

SQLite 中不支持 RIGHT OUTER JOIN，但是可以通过调换表的位置，使用 LEFT OUTER JOIN 达到相同的效果

---

一个图跟好理解 OUTER JOIN 的关系

![](http://ww2.sinaimg.cn/large/006tNbRwgy1ffl5896990j30rl0kp42l.jpg)

## 使用带聚集函数的联结

```sql
SELECT Customer.cust_id, COUNT(Orders.order_num) AS num_ord
FROM Customers INNER JOIN Orders
ON Customers.cust_id = Orders.cust_id
GROUP BY Customers.cust_id;
```

```sql
SELECT Customers.cust_id, COUNT(Orders.cust_num) AS num_ord
FROM Customers LEFT OUTER JOIN Orders
ON Customers_cust_id = Orders.cust_id
GROUP BY Customers.cust_id;
```



