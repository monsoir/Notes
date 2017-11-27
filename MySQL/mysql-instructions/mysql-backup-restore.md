# MySQL 备份与恢复

## 备份

使用的是 `mysqldump` 命令

- 备份某个表

    ```sql
    mysqldump --user='[user name]' -p [database name] [table name] > /path/to/the/backup.sql 
    ```
    
    > 最简单的备份命令

- 备份某一整个数据库

    ```sql
    mysqldump --user='[user name]' -p [database name] > /path/to/the/backup.sql
    ```

## 恢复

使用的是 `mysql` 命令

- 恢复某一整个数据库

    ```sql
    mysql --user='[user name]' -p [database name] < /path/to/the/backup.sql
    ```
    
    > 最简单的恢复
    > 当备份文件被读进数据库时，会先删除原有数据库的表和数据，在用备份重置


