# 基本概念

+ 数据库——保存有组织的数据的容器

+ 表——某种特定类型数据的结构化清单

+ 模式（schema）——关于数据库和表的布局及特性的信息

+ 列——表中的一个字段，所有表都是有一或多列组成的

+ 数据类型

+ 行——表中的一条记录。

+ 主键（primary key）——一列或**一组列**，能够唯一的区分表中的每个行
  表中的列只要满足以下条件即可作为主键：

  - 任意两行都不具有相同的主键值
  - 每个行都必须有一个主键值，主键列不允许NULL值

  ***Best practice***:

  * 不更新主键列的值
  * 不重用主键列的值
  * 不在主键列中使用可能会改变的值，例如人名

# MYSQL

DBMS——数据库管理系统可以分为共享文件型和客户-服务器型，前者用于桌面用途，而MySQL属于后者。

## MYSQL常用命令

MySQL命令结束需要；或\g才会执行，仅回车是不会执行命令的。

+ ```USE XXX;```  XXX为数据库名，代表使用XXX数据库
+ ```SHOW DATABASES; ``` 返回可用的数据库列表，列表内会包含MySQL内部使用的数据库
+ ```SHOW TABLES; ``` 返回一个数据库内所有可用的表
+ ```SHOW COLUMNS FROM XXX;``` XXX为表名，将返回XXX表内所有的列，它对每个字段返回一行，行中包含字段名、数据 类型、是否允许NULL、键信息、默认值以及其他信息 也可以使用**DESCRIBE XXX**来代替SHOW查看列信息
+ ```SHOW STATUS；```用于显示广泛的服务器状态信息
+ ```SHOW CREATE DATABASE XXX```; 和```SHOW CREATE TABLE XXX; ``` 分别用来显示创建特定数据库或表的MySQL语句
+ ```SHOW GRANTS;```  用来显示授予用户(所有用户或特定用户)的安全权限
+ ```SHOW ERRORS```和```SHOW WARNINGS```，用来显示服务器错误或警告消息。

# 检索数据

## SELECT语句

### 检索单个列

```SELECT  column FROM table;```

**关于SQL语句大小写**

SQL语句不区分大小写，但是表名，列名和值可能会区分，具体依赖于具体的DBMS及其如何配置

### 检索多个列

```SELECT c1, c2, c3 FROM table;```

***选择多个列时，列名之间要加逗号，最后一个列不需要加，如果加了会出错***

### 检索所有的列

使用 * 通配符表示所有的列：

```SELECT * FROM table;```

### 去重

DISTINCT, 放在列前面

```SELECT DISTINCT column FROM table;```

### 限制结果

返回一定数量的行，各种数据库对控制结果行数的实现并不相同

MYSQL使用LIMIT来限制结果行数

+ LIMIT加一个数字
  ```SELECT c FROM table LIMIT 5;``` LIMIT 5指示返回不多于5行
+ LIMIT加两个数字
  ```SELECT c FROM table LIMIT 5, 2;``` 第一个数字5代表开始位置，第二个表示要检索的行数，所以该句代表从行5（第6行）开始返回2行
  ***行从0开始记数，LIMIT1, 1检索出的是第二行而不是第一行***
+ OFFSET
  MYSQL 5支持使用OFFSET来代替LIMIT加两数：
  ```SELECT c FROM table LIMIT 4 OFFSET 3;``` 意思是从行3开始取4行，等同于LIMIT 3, 4









