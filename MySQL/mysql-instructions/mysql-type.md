# MySQL 中的类型问题

## 字符串类型

```sql
CREATE TABLE status_names (status_id INT, status_name CHAR(8));
```

上面使用 `CHAR(8)` 而不是 `TEXT`, 可以限制字符串长度，提高数据库性能，需要在设计表时考虑好

---

变长字符类型 `VARCHAR`

`VARCHAR(255)` 表明最大长度为 255

> `CHAR` 与 `VARCHAR`
> 
> `CHAR` 分配到的空间总是最大长度，不会因内容少而减少，但表的运行速度会更快
> `VARCHAR` 存储引擎为根据字符分配的空间根据长度而定

---

`TEXT` 长度可变，最多为 65535 字节

## BIT 数据类型

- 这个类型只占一位，即可以当 `true`, `false` 使用
    - 1 有设定值
    - 0 没有设定值
- 要为给类型赋值，需要将值用引号包围，并在前面加上字母 b

`endangerd BIT DEFAULT b'1'`

## ENUM 枚举

```sql
ENUM('Extinct',
  'Extinct in Wild',
  'Threatened - Critically Endangered',
  'Threatened - Endangered',
  'Threatened - Vulnerable',
  'Lower Risk - Conservation Dependent',
  'Lower Risk - Near Threatened',
  'Lower Risk - Least Concern')
```

- 枚举所有可接受的值
- 值放在一对括号中
- 每个值用引号包围
- 值之间用逗号分隔

