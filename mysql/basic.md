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

# 排序检索数据

关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据顺序有意义

## 单列排序

```SELECT c FROM table ORDER BY c;```

将按照c进行排序

##多列排序

```SELECT c1, c2, c3 FROM table ORDER BY c2, c3;```

将先按照c1排序，在同一c1内在按照c2排序，结果类似:

| c1    | c2   | c3   |
| ----- | ---- | ---- |
| name1 | 1    | 1    |
| name2 | 1    | 2    |
| name3 | 1    | 3    |
| name4 | 2    | 1    |
| name5 | 2    | 4    |
| name6 | 3    | 0    |

如果上表c2列中所有值是唯一的，那么将不会按照c3排序

## 排序方向

升序（ASC）为默认排序，所以排序是不指定也可以，但降序排序需要指定DESC来实现

```SELECT c FROM table ORDER BY c DESC;```

对于多列排序，希望哪一列是降序的那么就在该列后加上DESC，其他不加DESC的仍然按照默认排序

使用ORDER BY和LIMIT组合可以找出一个列的最高或最低值

# 过滤数据

## where

where和order by一起使用时，需要让order by位于where之后，否则会产生错误

### where子句操作符

| 操作符  | 说明                       |
| ------- | -------------------------- |
| =       | 等于                       |
| <>      | 不等于                     |
| !=      | 不等于                     |
| <       | 小于                       |
| <=      | 小于等于                   |
| >       | 大于                       |
| >=      | 大于等于                   |
| BETWEEN | 在指定两值之间(两端都包含) |

MySQL在执行匹配时默认不区分大小写，所以会出现where指定=某值但返回多行：

```
mysql> select * from testwhere where name='test';
+----+------+
| id | name |
+----+------+
|  1 | Test |
|  2 | test |
+----+------+
```



***何时使用引号***

单引号用来限定字符串，数值比较不需要字符串。

### 范围值检查

可以使用between来检查某个范围的值：

```SELECT c1 FROM table WHERE c1 BETWEEN 5 AND 10;```

结果包含开始值和结束值。

### 组合句的计算次序

SQL也会像多数语言一样优先处理AND句，优先级AND>OR

### IN操作符

```SELECT c1 FROM table WHERE c1 IN (1, 2, 3);```

IN能做的可以用OR来代替，但IN有如下优势：

+ IN操作符语法更加清楚直观
+ 使用IN计算次序更加容易管理
+ IN一般比OR更快
+ IN最大的优点是可以包含其他的SELECT语句

# 通配符过滤

## LIKE

**通配符 **用来匹配值的一部分的特殊字符

**搜索模式 **由字面值，通配符或者两者组合构成的搜索条件



### %通配符

表示<font color=pink>任何</font>字符出现<font color=pink>任意</font>次数(0个也可以)

```SELECT c1, c2 FROM table WHERE c1 LIKE 'jet%';```

输出

| c1              | c2   |
| --------------- | ---- |
| jetpack001      | 1    |
| jetpack002      | 2    |
| jetpackwhatever | 3    |

**%不能匹配NULL**

### _通配符

下划线通配符与%类似，但它只匹配一个字符。

## 使用通配符注意事项

+ 通配符搜索会花较长时间，不要过度使用
+ 除非绝对必要，否则不要把通配符搜索置于搜索模式的开始处，否则速度是最慢的

# 正则表达式搜索

REGEXP

```SELECT c1 FROM table WHERE c1 REGEXP '1000'```

结果是c1列中值中包含1000的所有行

***LIKE与REGEXP***

LIKE匹配的是整个列，而REGEXP匹配的是列值，如
table:

| c1           |
| ------------ |
| jetpack 1000 |

`SELECT c1 FROM table WHERE c1 LIKE '1000';`

返回0条结果

`SELECT c1 FROM table WHERE c1 REGEXP '1000';`

返回一条结果

## OR匹配

使用｜

'1000|2000'

## 匹配几个字符之一

一组字符用[]括起来

`SELECT c1 FROM table WHERE c1 REGEXP '[123]Ton';`

[123]表示1或2或3

## 匹配范围

[0123456789]可以写成[0-9]



