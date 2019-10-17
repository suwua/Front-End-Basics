# Mysql 必知必会

## 术语

不同的人可能会使用相同的数据库术语表示不同的事物，会造成一些混乱，下面是一张重要的数据库术语清单。

### 数据库（database）
**数据库是保存有组织的数据的容器（通常是一个文件或一组文件）。**

> 易混点：人们经常用“数据库”这个词代表他们使用的数据库软件。数据库软件是 DBMS(数据库管理系统)，例如 MySQL 就是一种 DBMS ，而数据库是通过 DBMS 创建和操纵的容器。我们通常不直接访问数据库，而是通过使用 DBMS 来访问数据库。

### 表（table）
**表是某种特定类型数据的结构化清单。**

数据库中的每个表都有一个名字，用来标识自己，称之为“表名”。此名字是唯一的，在相同的数据库中不能使用重复的表名，但是在不同的数据库中可以使用。

### 模式（schema）
**模式是关于数据库和表的布局及特性的信息。**

### 列（column）
**列是表中的一个字段。所有的表都是由一个或多个列组成的。**

### 数据类型（datatype）
**数据类型是所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据类型。**

### 行（row）
**行是表中的一个记录。**

有人会把行（row）称之为数据库记录（record），这两个数据是可以互相代替的，但是从技术上说，行才是正确的术语。

### 主键（primary key）
**主键是一列（或一组列），其值能够唯一区分表中每个行。**

表中的任何列只要满足以下条件，都可以作为主键：
* 任意两行都不具有相同的主键值；
* 每个行都必须具有一个主键值（主键列不允许 NULL 值）

此外还有几个主键的最佳实践：
* 不更新主键列中的值；
* 不重用主键列的值；
* 不在主键列中使用可能会更改的值。（例如，如果使用一个名字作为主键以标识某个供应商，当该供应商合并和更改其名字时，就得必须更改这个主键。）

### SQL（Structured Query Language）
**SQL 是结构化查询语言（Structured Query Language）的缩写，是一种专门用来与数据库通信的语言。**

SQL 的优点：
* SQL 不是某个特定数据库供应商专有的语言。即 SQL 不是一种专利语言，而且存在一个标准委员会。几乎所有重要的 DBMS 都支持 SQL。
* SQL 简单易学。它的语句全都是由描述性很强的英语单词组成，而且这些单词的书目不多。
* SQL 尽管看上去很简单，但它实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作。

## MySQL 安装

推荐几个 MySQL 安装和连接的经验文章

* [在Mac下安装MySQL](http://www.scienjus.com/install-mysql-on-mac/)

## MySQL 应用

### mysql 命令行
* 命令输入在 mysql> 之后；
* 命令用 ; 或 \g 结束，换句话说，仅按 Enter 不执行命令；
* 输入 help 或 \h 获得帮助，也可以输入更多的文本获得特定命令的帮助（如，输入 help select 获得试用 SELECT 语句的帮助）；
* 输入 quit 或 exit 退出命令行。

### 连接数据库
连接数据库需要以下信息：
* 主机名（计算机名）——如果连接到本地 MySQL 服务器，为 localhost ;
* 端口（如果使用默认端口 3306 之外的端口）；
* 一个合法的用户名；
* 用户口令（如果需要）

例如下面的指令：
```
mysql -u root -h localhost -P 3306 -p
```

### 数据库的登录和成员管理

#### 访问控制
MySQL 服务器的安全基础是：用户应该对他们需要的数据具有适当的访问权，既不能多也不能少。

需要给用户提供他们所需的访问权，且仅提供他们所需的访问权。这就是所谓的**访问控制**。访问控制的目的不仅仅是防止用户的恶意企图，访问控制也有助于避免很常见的无意识错误的结果，如错打 MySQL 语句，在不合适的数据库中操作或其他一些用户错误。

#### 管理用户

##### 查询已有用户
MySQL 用户账号和信息存储在名为 mysql 的 MySQL数据库中。一般只有在需要获得所有用户账号列表时才会直接访问。
```
# 输入
USE mysql;
SELECT user FROM user;

# 输出
+------------------+
| user             |
+------------------+
| test             |
| root             |
+------------------+
2 rows in set (0.01 sec)
```

##### 创建用户账号

> 1、使用 CREATE USER 语句（推荐）
```
# 输入
CREATE USER chenfangxu IDENTIFIED BY '123456';
# 输出
Query OK, 0 rows affected (0.19 sec)

# 输入
SELECT user FROM user;
#输出
+------------------+
| user             |
+------------------+
| chenfangxu       |
| test             |
| root             |
+------------------+
3 rows in set (0.00 sec)
```

> 2、GRANT 语句也可以创建用户账号。（MySQL 8.0以上的新版本已经将创建账户和赋予权限分开了，所以不能再用这种方法创建用户了）
```
# mysql8.0以下
GRANT SELECT ON *.* TO chenfangxu@'%' IDENTIFIED BY '123456';
```

> 3、使用 INSERT 直接插入行到 user 表来增加用户（不建议）

##### 设置访问权限

在创建用户账号后，必须接着分配访问权限。新创建的用户账号没有访问权限。他们能登录 MySQL ，但不能看到数据，不能执行任何数据库操作。

> **查看赋予用户账号的权限** `SHOW GRANTS FOR`

```
# 输入
SHOW GRANTS FOR chenfangxu;

# 输出
+----------------------------------------+
| Grants for chenfangxu@%                |
+----------------------------------------+
| GRANT USAGE ON *.* TO `chenfangxu`@`%` |
+----------------------------------------+
1 row in set (0.00 sec)
```

权限 `USAGE ON *.*` ,USAGE表示根本没有权限，这句话就是说在任意数据库和任意表上对任何东西没有权限。

`chenfangxu@%` 因为用户定义为 `user@host`, MySQL的权限用用户名和主机名结合定义，如果不指定主机名，则使用默认的主机名`%`（即授予用户访问权限而不管主机名）。

> **添加（更新）用户权限** `GRANT privileges ON databasename.tablename TO 'username'@'host';`
```
# 输入
GRANT SELECT ON performance_schema.* TO chenfangxu@'%';
SHOW GRANTS FOR chenfangxu;

# 输出
+------------------------------------------------------------+
| Grants for chenfangxu@%                                    |
+------------------------------------------------------------+
| GRANT USAGE ON *.* TO `chenfangxu`@`%`                     |
| GRANT SELECT ON `performance_schema`.* TO `chenfangxu`@`%` |
+------------------------------------------------------------+
```

> **撤销用户的权限** `REVOKE privileges ON databasename.tablename FROM 'username'@'host';`
```
# 输入
REVOKE SELECT ON performance_schema.* FROM chenfangxu@'%';
SHOW GRANTS FOR chenfangxu;

#输出
+----------------------------------------+
| Grants for chenfangxu@%                |
+----------------------------------------+
| GRANT USAGE ON *.* TO `chenfangxu`@`%` |
+----------------------------------------+
```

#### 重命名
`RENAME USER 'username' TO 'newusername';`
```
# 输入
RENAME USER test TO test1;
SELECT user FROM user;

# 输出
+------------------+
| user             |
+------------------+
| test1            |
| root             |
+------------------+
2 rows in set (0.00 sec)
```

##### 更改用户密码(mysql 8.0.11后)
`SET PASSWORD FOR 'username'@'host' = 'newpassword';`
```
SET PASSWORD FOR chenfangxu@'%' = '654321';

# 更改root密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'yourpasswd';
```

#### 删除用户
`DROP USER 'username'@'host';`

```
# 输入
DROP USER chenfangxu@'%';
SELECT user FROM user;

#输出
+------------------+
| user             |
+------------------+
| test             |
| root             |
+------------------+
2 rows in set (0.00 sec)
```

MySQL 5 以前， DROP USER 只能用来删除用户账号，不能删除相关的权限。因此，如果使用旧版的 MySQL 需要先用 REVOKE 删除与账号相关的权限，然后再用 DROP USER 删除账号。


### 操作数据库

#### 显示数据库列表 `SHOW DATABASES;`
```
# 输入
SHOW DATABASES;

# 输出
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.01 sec)
```