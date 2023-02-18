---
title: MySQL 操作数据库
created: 2023/2/16 01:03:12
updated: 2023/2/16 01:03:29
tags: [MySQL, DDL]
---

## 查看数据库

查看所有数据库

```sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

使用数据库

```sql
mysql> USE mysql;
Database changed
```

查看当前使用的数据库

```sql
mysql> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| mysql      |
+------------+
1 row in set (0.00 sec)
```

## 创建数据库

语法为 

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则]
```

其中 `[]` 中的语句为可选的。

```sql
# 创建一个名为 student 的数据库
mysql> CREATE DATABASE student;
Query OK, 1 row affected (0.06 sec)

# 查看所有数据库，可以看到 student 数据库在其中
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| student            |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

如果创建一个已经存在的数据库，语句执行会报错

```sql
mysql> CREATE DATABASE student;
ERROR 1007 (HY000): Can't create database 'student'; database exists
```

这时可以在语句中加入 `IF NOT EXISTS`，只有当数据库不存在时才会创建，这样就不会报错

```sql
mysql> CREATE DATABASE IF NOT EXISTS student;
Query OK, 1 row affected, 1 warning (0.04 sec)
```

在创建数据库时也可以指定数据库使用的字符集

```sql
mysql> CREATE DATABASE IF NOT EXISTS student DEFAULT CHARSET utfmb4;
Query OK, 1 row affected, 2 warnings (0.03 sec)
```

```note
看视频的时候推荐使用 `utfmb4` 字符集，它是 `utf-8` 的超集，相比于 `utf-8`，它可以使用四个字节来表示字符，因此它可以保存表情符号，而 `utf-8` 则不行。
```

可以通过 `SHOW CREATE DATABASE 数据库名;` 来查看建立数据库的语句，从中可以获得数据库的

```sql
mysql> SHOW CREATE DATABASE student;
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                                                   |
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
| student  | CREATE DATABASE `student` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

## 删除数据库

删除数据库的语法为

```sql
DROP DATABASE [IF EXISTS] 数据库名;
```

同样， `[]` 中的内容为可选的。

```sql
mysql> DROP DATABASE student;
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

如果删除一个不存在的数据库则会保存，再次执行删除 `student` 数据库的操作

```sql
mysql> DROP DATABASE student;
ERROR 1008 (HY000): Can't drop database 'student'; database doesn't exist
```

为了安全删除，此时可以添加 `IF EXISTS`，只有当数据库存在时才会执行删除操作

```sql
mysql> DROP DATABASE IF EXISTS student;
Query OK, 0 rows affected, 1 warning (0.03 sec)
```

## 更改数据库

修改数据库的默认字符集，语法为

```sql
ALERT DATABASE 数据库名 DEFAULT CHARSET 新的字符集;
```

```sql
mysql> CREATE DATABASE teacher DEFAULT CHARSET utf8;
Query OK, 1 row affected, 1 warning (0.04 sec)

mysql> SHOW CREATE DATABASE teacher;
+----------+--------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                        |
+----------+--------------------------------------------------------------------------------------------------------+
| teacher  | CREATE DATABASE `teacher` /*!40100 DEFAULT CHARACTER SET utf8mb3 */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+--------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> ALTER DATABASE teacher DEFAULT CHARSET utf8mb4;
Query OK, 1 row affected (0.04 sec)

mysql> SHOW CREATE DATABASE teacher;
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                                                   |
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
| teacher  | CREATE DATABASE `teacher` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+-----------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

上面的例子首先创建了一个数据库 `teacher`，默认字符集为 `utf8 (utf8mb3)`，然后我们通过 `ALTER DATABASE teacher DEFAULT CHARSET utf8mb4;` 将其字符集修改为了 `utf8mb4`。