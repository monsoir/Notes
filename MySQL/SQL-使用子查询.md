# 使用子查询

子查询，即嵌套在其他查询中的查询

## 利用子查询进行过滤

```sql
SELECT cust_name, cust_contact FROM customer WHERE cust_id IN (
SELECT cust_id FROM Orders WHERE order_num IN (
SELECT order_num FROM OrderItems WHERE prod_id = 'RGAN01'));
```

DBMS 执行了三条 SELECT 语句

最里边的子查询，返回 prod_id 为 RGAN01 的数据，并将结果传递到第二条子查询中，作用于 WHERE 过滤条件中

第二条子查询，返回 cust_id 的数据，并将结果传递到最外层的查询中，作用于 WHERE 过滤条件中

最外层查询，返回最后的结果

因此，执行顺序是从里到外，一层一层返回

## 使用子查询作为计算字段

```sql
SELECT cust_name,
       cust_state,
       (SELECT COUNT(*)
        FROM Orders
        WHERE Orders.cust_id = Customer.cust_id) AS orders
FROM Customers
ORDER BY cust_name;
```

返回三列：cust_name, cust_state, orders，其中 orders 是计算字段，从子查询中获得数据

子查询对检索出的每个顾客执行一次

类似 Orders.cust_id, Customer.cust_id，形如 `表名.列名`，叫做**完全限定列名**，避免列名混淆冲突

## 注意

作为子查询的 SELECT 语句，只能查询单个列，即只能返回单个列，否则出错



