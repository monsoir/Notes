# SQLite 基本知识

## SQLite 基本类型

[👉 官方文档 https://sqlite.org/datatype3.html](https://sqlite.org/datatype3.html)

### SQLite 支持的存储类(Storage Classes) 和 数据类型(Datatypes)

类型 | 类型名称 | 说明
--- | --- | ---
NULL | 空值 |
INTEGER | 带符号的整数 | 存储使用的位数取决于值的大小
REAL | 浮点数 | 8 位的 IEEE 浮点数
TEXT | 字符串 | 使用的编码是数据库的编码类型
BLOB | 二进制数据 | 

> Storage class 比 data type 更加范型，如 INTEGER, 包含了 6 种不同存储长度的整数类型

### Boolean 类型

SQLite 并不包含单独的 Boolean 存储类，而是分别存储成 INTERGER 类型的 0(false) 和 1(true)


### 日期和时间类型

SQLite 并没有用于存储日期和时间的存储类。然而，内置的 [👉 Date Time 方法](https://sqlite.org/lang_datefunc.html) 可以将日期时间转换成 TEXT, REAL, INTEGER 类型进行存储

- TEXT 使用格式 `YYYY-MM-DD HH:MM:SS.SSS`
- REAL as Julian day numbers
- INTEGER 使用格式 Unix 时间戳


