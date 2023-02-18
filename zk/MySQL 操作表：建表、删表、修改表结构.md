---
title: MySQL 操作表：建表、删表、修改表结构
created: 2023/2/16 01:04:40
updated: 2023/2/16 01:04:40
tags: [MySQL, DDL]
---

## 查看表

通过 `SHOW TABLES;` 可以查看该数据库下所有的表，我们查看查看一下系统的数据库

```sql
mysql> USE mysql;
Database changed
mysql> SHOW tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| 节省篇幅 省略部分 ...                                   |
| time_zone_name                                       |
| time_zone_transition                                 |
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.03 sec)
```

通过 `DESC 表名;` 可以查看表的结构

```sql
mysql> DESC user;
+--------------------------+-----------------------------------+------+-----+-----------------------+-------+
| Field                    | Type                              | Null | Key | Default               | Extra |
+--------------------------+-----------------------------------+------+-----+-----------------------+-------+
| Host                     | char(255)                         | NO   | PRI |                       |       |
| User                     | char(32)                          | NO   | PRI |                       |       |
| Select_priv              | enum('N','Y')                     | NO   |     | N                     |       |
| Insert_priv              | enum('N','Y')                     | NO   |     | N                     |       |
| 节省篇幅，省略部分 ...
| password_lifetime        | smallint unsigned                 | YES  |     | NULL                  |       |
| account_locked           | enum('N','Y')                     | NO   |     | N                     |       |
| Create_role_priv         | enum('N','Y')                     | NO   |     | N                     |       |
| Drop_role_priv           | enum('N','Y')                     | NO   |     | N                     |       |
| Password_reuse_history   | smallint unsigned                 | YES  |     | NULL                  |       |
| Password_reuse_time      | smallint unsigned                 | YES  |     | NULL                  |       |
| Password_require_current | enum('N','Y')                     | YES  |     | NULL                  |       |
| User_attributes          | json                              | YES  |     | NULL                  |       |
+--------------------------+-----------------------------------+------+-----+-----------------------+-------+
51 rows in set (0.05 sec)
```

## 创建表

创建表的语法为

```sql
CREATE TABLE [IF NOT EXISTS] 表名 (
  字段1 类型,
  字段2 类型,
  ...
) COMMENT 表描述;
```

例如创建一个学生表，包含有 `id`, `name`, `age` 三个属性

```sql
mysql> CREATE TABLE student (
    ->   id int,
    ->   name varchar(10),
    ->   age tinyint unsigned
    -> ) COMMENT '学生信息表';
Query OK, 0 rows affected (0.06 sec)

mysql> DESC student;
+-------+------------------+------+-----+---------+-------+
| Field | Type             | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| id    | int              | YES  |     | NULL    |       |
| name  | varchar(10)      | YES  |     | NULL    |       |
| age   | tinyint unsigned | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
3 rows in set (0.05 sec)
```

通过 `SHOW CREATE TABLE 表名;` 可以查看建表语句

```sql
mysql> SHOW CREATE TABLE student;
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                                                                                                            |
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| student | CREATE TABLE `student` (
  `id` int DEFAULT NULL,
  `name` varchar(10) DEFAULT NULL,
  `age` tinyint unsigned DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='学生信息表'      |
+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

## 修改表

这里的修改表并不是修改表中的数据，而是指修改表的结构，包括增删表中的字段，修改字段名称，字段类型，以及修改表名。

#### 添加字段

通过 `ALTER ... ADD ...` 命令向表中添加字段

```sql
ALTER TABLE 表名 ADD 字段名 类型;
```

例如向 `student` 表中添加一个 `score` 字段

```sql
mysql> ALTER TABLE student ADD score double(5,2);
Query OK, 0 rows affected, 1 warning (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> DESC student;
+-------+------------------+------+-----+---------+-------+
| Field | Type             | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| id    | int              | YES  |     | NULL    |       |
| name  | varchar(10)      | YES  |     | NULL    |       |
| age   | tinyint unsigned | YES  |     | NULL    |       |
| score | double(5,2)      | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

### 修改字段类型

通过 `ALTER ... MODIFY ... ` 命令来修改字段的类型 

```sql
ALTER TABLE 表名 MODIFY 字段 新类型;
```

例如将 `student` 表中的 `name` 的类型修改为 `varchar(20)`

```sql
mysql> ALTER TABLE student MODIFY name varchar(20);
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC student;
+-------+------------------+------+-----+---------+-------+
| Field | Type             | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| id    | int              | YES  |     | NULL    |       |
| name  | varchar(20)      | YES  |     | NULL    |       |
| age   | tinyint unsigned | YES  |     | NULL    |       |
| score | double(5,2)      | YES  |     | NULL    |       |
+-------+------------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

### 修改字段名称

通过 `ALTER ... CHANGE ...` 命令修改字段的名称

```sql
ALTER TABLE student CHANGE 旧字段 新字段 类型;
```

例如修改 `student` 表中的 `name` 为 `username`

```sql
mysql> ALTER TABLE student CHANGE name username varchar(20);
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC student;
+----------+------------------+------+-----+---------+-------+
| Field    | Type             | Null | Key | Default | Extra |
+----------+------------------+------+-----+---------+-------+
| id       | int              | YES  |     | NULL    |       |
| username | varchar(20)      | YES  |     | NULL    |       |
| age      | tinyint unsigned | YES  |     | NULL    |       |
| score    | double(5,2)      | YES  |     | NULL    |       |
+----------+------------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

## 删除表

通过 `DROP` 命令来删除表

```sql
DROP TABLE [IF EXISTS] 表名;
```

例如删除学生表

```sql
mysql> DROP TABLE IF EXISTS student;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES;
Empty set (0.00 sec)
```

另外也可以通过 `TRUNCATE` 命令来删除表

```sql
TRUNCATE TABLE 表名;
```

与 `DROP` 不同的是，删除表之后它还会创建一个同名的新表，其中没有任何内容，执行这条命令就相当于是清空了表中的所有数据。
