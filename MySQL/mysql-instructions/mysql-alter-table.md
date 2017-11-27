# MySQL 修改表结构

`ALTER TABLE [table name] [changes];`

- [table name] 作出修改的表
- [changes] 更改命令

## 创建一个与原有表相同结构的新表

```sql
CREATE TABLE [database name].[new table name] LIKE [old table name];
```

> 只是复制了表的结构，并没有把数据也复制的

随后把数据也复制一下

```sql
INSERT INTO [new table name]
SELECT * FROM [database name].[old table name];
```

---

复制表结构的同时，也复制数据

```sql
CREATE TABLE [new table name]
SELECT * FROM [database name].[old table name];
```

## 删除表

```sql
DROP TABLE [database name].[table name];
```

## 添加列

```sql
ALTER TABLE [table name]
ADD COLUMN [column name] [type];
```

例子

```sql
ALTER TABLE bird_families
ADD COLUMN order_id INT;
```

## 删除列

```sql
ALTER TABLE [table name]
DROP COLUMN [column name];
```

> 删除列会将列的 **结构和数据** 一并删除

## 指定新列的显示位置

MySQL 不关心显示表结构时，列的位置，而指定新列的显示位置只是方便开发者查看

- 指定新列排序在某一个列的后面

```sql
ALTER TABLE [table name]
ADD COLUMN [column name] [type, constraints]
AFTER [another column name];
```

- 指定新列排序在最前面

```sql
ALTER TABLE [table name]
ADD COLUMN [column name] [type, constraints]
FIRST;
```

## 一个例子

```sql
ALTER TABLE birds_new
ADD COLUMN body_id CHAR(2) AFTER wing_id,
ADD COLUMN bill_id CHAR(2) AFTER body_id,
ADD COLUMN endangered BIT DEFAULT b'1' AFTER bill_id,
CHANGE COLUMN common_name common_name VARCHAR(255);
```

- 添加了三个新列 `ADD COLUMN`
- 修改了一列的结构（具体是，字符串类型的宽度） `CHANGE COLUMN`
- 使用了一个 `BIT` 类型

解释：

- `CHANGE COLUMN` 输入了列名 common_name 两次
    1. 用于指明修改的是哪个列
    2. 用于指定该列的新名字，即使不打算改名，也需要指定，否则报错
- `ALTER TABLE` 时，会创建一个副本表，当所有操作合法时，才会替换掉原有的表
    - 所以，上面 bill_id 声明放在 body_id 后，即使原有的表并没有 body_id 这个列，这种骚操作也是合法的，因为所有操作都是现在副本表上操作

## 有一个例子

```sql
ALTER TABLE birds_new
MODIFY COLUMN endangered
ENUM('Extinct',
  'Extinct in Wild',
  'Threatened - Critically Endangered',
  'Threatened - Endangered',
  'Threatened - Vulnerable',
  'Lower Risk - Conservation Dependent',
  'Lower Risk - Near Threatened',
  'Lower Risk - Least Concern')
AFTER family_id;
```

修改表结构的另一个方法，使用 `MODIFY COLUMN`

- `MODIFY COLUMN` 只需要输入一次列名，因为这个命令不支持修改列名
    - 改列名需要使用 `CHANGE COLUMN`

## 修改列的默认值

- 使用 `CHANGE` 命令

    ```sql
    ALTER TABLE [table name]
    CHANGE COLUMN [column name] [new column name] [type, constraints] DEFAULT [default value];
    ```
    
    在修改默认值的同时，可以修改列名

- 使用 `ALTER` 命令

    ```sql
    ALTER TABLE [table name]
    ALTER [column name] SET DEFAULT [default value];
    ```

## 取消列的默认值

将自定义的默认值取消，重新设置会 NULL 或对应数据类型的默认值

```sql
ALTER TABLE [table name]
ALTER [column name] DROP DEFAULT;
```

- 不会删除列的数据
- 只会更改列的默认值

## 手动更改 AUTO_INCREMENT 的值

```sql
SELECT auto_increment
FROM information_schema.tables
WHERE table_name = [your table name];
```

这里输出的结果就是 [your table] 在下一次插入数据时，自增长的值

修改这个值

```sql
USE [your database]

ALTER TABLE [your table]
AUTO_INCREMENT = 10;
```

## 重命名一个表

```sql
RENAME TABLE [the table]
TO [that table];
```

稳妥的做法：

1. 将原表(table_name)重命名为另一个名字，如 table_name_old
2. 将新表(table_name_new)重命名为原表的名字，即 table_name
3. 最后，如果有需要，可以将原表删除

> `RENAME TABLE` 的操作同时可以实现数据库的移动，即实现从数据库 A 移动到数据库 B 中

```sql
RENAME TABLE B.the_table TO B.the_table_old,
A.the_table_new TO B.the_table;
```

> 在修改过程中若出现了问题，将会得到一个错误消息，并且不会作出任何修改


