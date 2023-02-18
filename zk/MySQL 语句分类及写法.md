---
title: MySQL 语句分类及写法
created: 2023/2/16 01:01:44
updated: 2023/2/16 01:01:44
tags: [MySQL]
---

SQL (Structured Query Language) 是用于操作数据库的领域特定语言，它可以对数据库以及其中的表（内容）进行操作，包括

- 创建和删除数据库
- 创建和删除表，创建、删除以及修改表的字段
- 对表的某一行进行操作，包括插入、删除、修改以及查询数据
- 数据库用户管理以及权限管理
- ...

按照功能，可以将 SQL 语句分为四类：

- DDL (Data Definition Language) ：用以定义以及数据库及其包含的对象（表、索引、视图、存储过程、触发器等）的结构，不会操作表中的数据，包括
  - CREATE
  - DROP
  - ALERT
  - TRUNCATE
  - COMMENT
  - RENAME
- DML (Data Manipulation Language)：用来操作数据库表中数据，包括
  - INSERT
  - UPDATE
  - DELETE
  - CALL
- DQL (Data Query Language)：用来查询数据库中的数据，包括
  - SELECE
- DCL (Data Control Language)：用于数据库的权限管理，用户创建，密码修改等，包括
  - GRANT
  - REVOKE

用来操作数据库的语句我们称为 SQL 语句，它具有如下特点

- 可单行或多行，以分号结尾表示结束，可以使用空格和缩进方便阅读
  
  ```sql
  SELECT * FROM student WHERE id = 1;
  ```

  ```sql
  SELECT * FROM student
    WHERE id = 1;
  ```

- 不区分大小写（推荐关键字使用大写，易于阅读及区分）
  
  ```sql
  SELECT * FROM student WHERE id = 1;
  ```
  
  ```sql
  select * from student where id = 1;
  ```

- 注释
  
  单行注释使用 `--` 或者 `#`

  ```sql
  -- 从 student 表中查询所有内容
  SELECT * FROM student; # 查询所有内容
  ```

  多行注释可以使用 `/* 注释内容 */`

  ```sql
  /*
  从 student 表中查询所有内容
  */
  SELECT * FROM student;
  ```