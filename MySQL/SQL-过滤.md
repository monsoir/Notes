# SQL-过滤

[TOC]

## 操作符(谓词) LIKE

指示 DBMS，后跟的搜索模式利用通配符而不是简单的相等匹配进行比较

## 通配符

### 百分号 %

表示任何字符(0个字符，1个字符，多个字符)出现任意次数

> 例外
> % 不会匹配 NULL

### 下划线 _

匹配单个字符

### 方括号 []

用于指定一个**字符集**，匹配指定位置（通配符位置）的**一个字符**

```sql
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[JM]%'
ORDER BY cust_contact;
```

匹配第一个字符为 J 或 M 的字符串，只匹配第一个字符，后面可以跟任何和任意个字符

```sql
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[^JM]%'
ORDER BY cust_contact;
```
                                    
匹配第一个字符**不为** J 或 M 的字符串，只匹配第一个字符，后面可以跟任何和任意个字符


### 注意

通配符搜索一般比其他搜索耗费更多的时间，因此不能过度使用通配符。若其他操作符能达到相同的目的，应使用其他操作符                     

---



