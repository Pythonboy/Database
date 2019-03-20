# 一、SQL简介

## 1.1 SQL是什么

**SQL是结构化查询语言，它是一种用于存储，操作和检索存储在关系数据库中的数据的计算机语言**



## 1.2 SQL命令

#### 1）DDL——数据定义语言

| 序号 | 命令   | 描述                                         |
| ---- | ------ | -------------------------------------------- |
| 1    | CREATE | 用于在数据库中创建新表，表视图或其他对象     |
| 2    | ALTER  | 用于修改现有数据库对象，例如：表             |
| 3    | DROP   | 用于删除整个表，数据库中的表或其他对象的视图 |



#### 2） DML——数据操纵语言

| 序号 | 命令   | 描述                         |
| ---- | ------ | ---------------------------- |
| 1    | SELECT | 从一个或多个表中检索某些记录 |
| 2    | INSERT | 创建一条记录                 |
| 3    | UPDATE | 用于修改(更新)记录           |
| 4    | DELETE | 删除记录                     |



#### 3)DCL——数据控制语言

| 序号 | 命令   | 描述               |
| ---- | ------ | ------------------ |
| 1    | GRANT  | 为用户提供权限     |
| 2    | REVOKE | 撤销用户授予的权限 |



## 1.3 数据的完整性

- **实体完整性** - 表中没有重复的行。

- **域完整性** - 通过限制值的类型，格式或范围，为给定列强制执行有效条目。

- **参照完整性** - 其他记录使用(引用)导致这些行无法删除。

- **用户定义的完整性** - 实施一些不属于实体，域或参照完整性的特定业务规则



## 1.4 SQL约束

- [NOT NULL约束](https://www.yiibai.com/sql/sql-not-null.html) - 确保列不能具有`NULL`值。

- [默认值约束](https://www.yiibai.com/sql/sql-default.html) - 在未指定列时为列提供默认值。

- [唯一约束](https://www.yiibai.com/sql/sql-unique.html)  - 确保列中的所有值都不同。

- [主键](https://www.yiibai.com/sql/sql-primary-key.html) - 唯一标识数据库表中的每一行/记录。

- [外键](https://www.yiibai.com/sql/sql-foreign-key.html)  - 唯一标识任何其他数据库表中的行/记录。

- [检查约束](https://www.yiibai.com/sql/sql-check.html) -  `CHECK`约束确保列中的所有值都满足特定条件。

- [索引](https://www.yiibai.com/sql/sql-index.html)  - 用于非常快速地从数据库创建和检索数据。



## 1.5 数据库范式

#### 1） 第一范式（1NF）

1. 定义所需要的数据项，因为它们成为在表中的列。放在一个表中的相关的数据项。

2. 确保有数据没有重复的组。

3. 确保有一个主键。



## 1.6 运算符

变量`a`的值是：`10`，变量`b`的值是：20

#### 1） SQL算术运算符

| 操作符 | 描述                               | 示例        |
| ------ | ---------------------------------- | ----------- |
| +      | 加法，执行加法运算。               | a + b = 30  |
| -      | 减法，执行减法运算。               | a + b = -10 |
| *      | 除法，执行除法运算                 | a * b = 200 |
| /      | 用左操作数除右手操作数             | b / a = 2   |
| %      | 用左手操作数除左手操作数并返回余数 | b % a = 0   |



## 1.7 在MySQL中创建表的示例

```sql
CREATE TABLE regions (
    region_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    region_name VARCHAR (25) DEFAULT NULL
);

CREATE TABLE countries (
    country_id CHAR (2) PRIMARY KEY,
    country_name VARCHAR (40) DEFAULT NULL,
    region_id INT (11) NOT NULL,
    FOREIGN KEY (region_id) REFERENCES regions (region_id) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE locations (
    location_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    street_address VARCHAR (40) DEFAULT NULL,
    postal_code VARCHAR (12) DEFAULT NULL,
    city VARCHAR (30) NOT NULL,
    state_province VARCHAR (25) DEFAULT NULL,
    country_id CHAR (2) NOT NULL,
    FOREIGN KEY (country_id) REFERENCES countries (country_id) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE jobs (
    job_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    job_title VARCHAR (35) NOT NULL,
    min_salary DECIMAL (8, 2) DEFAULT NULL,
    max_salary DECIMAL (8, 2) DEFAULT NULL
);

CREATE TABLE departments (
    department_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    department_name VARCHAR (30) NOT NULL,
    location_id INT (11) DEFAULT NULL,
    FOREIGN KEY (location_id) REFERENCES locations (location_id) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE employees (
    employee_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR (20) DEFAULT NULL,
    last_name VARCHAR (25) NOT NULL,
    email VARCHAR (100) NOT NULL,
    phone_number VARCHAR (20) DEFAULT NULL,
    hire_date DATE NOT NULL,
    job_id INT (11) NOT NULL,
    salary DECIMAL (8, 2) NOT NULL,
    manager_id INT (11) DEFAULT NULL,
    department_id INT (11) DEFAULT NULL,
    FOREIGN KEY (job_id) REFERENCES jobs (job_id) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (department_id) REFERENCES departments (department_id) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES employees (employee_id)
);

CREATE TABLE dependents (
    dependent_id INT (11) AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR (50) NOT NULL,
    last_name VARCHAR (50) NOT NULL,
    relationship VARCHAR (25) NOT NULL,
    employee_id INT (11) NOT NULL,
    FOREIGN KEY (employee_id) REFERENCES employees (employee_id) ON DELETE CASCADE ON UPDATE CASCADE
);


```



# 二、SQL数据库操作

## 2.1 Create Database 语句

SQL `CREATE DATABASE`语句用于创建新的SQL数据库：

```sql
CREATE DATABASE database_name

/*示例*/
CREATE DATABASE testdb;

/*查看*/
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sqldemo            |
| sys                |
| testdb             |
+--------------------+
6 rows in set
```

**数据库名称(`database_name`)在RDBMS中必须是唯一的**



## 2.2 Drop Database 语句

SQL `DROP DATABASE`语句用于删除SQL模式中已存在的数据库。

```sql
DROP DATABASE database_name;

/*示例*/
DROP DATABASE testdb;

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sqldemo            |
| sys                |
+--------------------+
5 rows in set
```



## 2.3 Rename Database 语句

当需要更改数据库的名称时，将使用SQL `RENAME DATABASE`

```mysql
RENAME DATABASE old_db_name TO new_db_name;
```



```sql
ALTER DATABASE old_name MODIFY NAME = new_name''
```



## 2.4 Use 语句

如果SQL模式中有多个数据库，那么在开始操作之前，需要选择一个将执行操作的数据库。

SQL `USE`语句用于选择SQL模式中的任何现有数据库。

```mysql
USE database_name;

/*示例*/
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sqldemo            |
| sys                |
+--------------------+
7 rows in set

USE sqldemo;
```



# 三、表操作

## 3.1 Create Table

```sql
CREATE TABLE table_name(
     column_name_1 data_type default value column_constraint,
     column_name_2 data_type default value column_constraint,
     ...,
     table_constraint
);


```

- 由`table_name`指定的表名在数据库中必须是唯一的
- 每个列定义由列名，列的[数据类型](http://www.yiibai.com/sql/sql-data-types.html)，默认值和一个或多个列约束组成
- 列的数据类型指定列可以存储的数据类型。 列的数据类型可以是数字，字符，日期等
- 列约束控制可以存储在列中的值的类型。 例如，`NOT NULL`约束确保列不包含`NULL`值。列可能有多个列约束。 例如，`users`表的`username`列可以同时具有`NOT NULL`和`UNIQUE`约束。
- 如果约束包含多个列，则使用表约束。 例如，如果表的主键包含两列，则在这种情况下，必须使用`PRIMARY KEY`表约束。



**示例：**

```sql
CREATE TABLE courses (
    course_id INT AUTO_INCREMENT PRIMARY KEY,
    course_name VARCHAR(50) NOT NULL
);

/*
courses表有两列：course_id和course_name;

course_id是课程表的主键列。 每个表都有一个且只有一个主键，用于唯一标识表中的每一行

course_id的数据类型是整数，由INT关键字表示。 此外，course_id列的值为AUTO_INCREMENT。表示当在courses表中插入新行而不提供course_id列的值时，数据库系统将为该列生成一个整数值

course_name存储课程名称。 其数据类型是最大长度为50的字符串(VARCHAR)。NOT NULL约束确保course_name列中不存储NULL值。

*/

```



## 3.2 Alter Table







