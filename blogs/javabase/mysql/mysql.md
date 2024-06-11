---
title: mysql(新版)
date: 2028-12-15
sticky: 1
sidebar: 'javabase'
tags:
 - javabase
categories:
 - javabase
---


### 1，数据库基本概念

1. **数据**

   数据（Data）是指对客观事物进行描述并可以鉴别的符号，这些符号是可识别的、抽象的。它不仅指狭义上的数字，而是有多种表现形式：字母、文字、文本、图形、音频、视频等。

2. **数据库**

   数据库是数据管理的有效技术，是由一批数据构成的有序集合，这些数据被存放在结构化的数据表里。数据表之间相互关联，反映客观事物间的本质联系。

3. **数据库管理系统**

   数据库管理系统（Database Management System，DBMS）是用来定义和管理数据的软件。电脑上安装了数据库管理系统后，就可以通过数据库管理系统创建数据库来存储数据，也可以通过该系统对数据库中的数据进行数据的增删改查相关的操作。我们平时说的MySQL数据库其实是MySQL数据库管理系统。

4. **数据库管理员**

   数据库管理员（Database Administrator，DBA）是指对数据库管理系统进行操作的人员，其主要负责数据库的运营和维护。

   

常见的数据库管理系统：

![1718058438327](./assets/1718058438327.png)



**数据库分类：**

- 关系型数据库
- 非关系型数据库

![1718058747335](./assets/1718058747335.png)



**关系型数据库：**关系型数据库最典型的数据结构是表，由二维表及其之间的联系所组成的一个数据组 织。可以采用结构化查询语言（SQL）对数据库进行操作。

- 优点：
  - 易于维护：都是使用表结构，格式一致；
  - 使用方便：SQL语言通用，可用于复杂查询；
  - 复杂操作：支持SQL，可用于一个表以及多个表之间非常复杂的查询。
- 缺点：
  - 读写性能比较差，尤其是海量数据的高效率读写；
  - 固定的表结构，灵活度稍欠；
  - 高并发读写需求，传统关系型数据库来说，硬盘I/O是一个很大的瓶颈。



**非关系型数据库：**非关系型数据库也称之为NoSQL数据库，是一种数据结构化存储方法的集合，可以是文档或者键值对等。

- 优点：
  - 格式灵活：存储数据的格式可以是key,value形式、文档形式、图片形式等等，文档形式、图片形式等等，使用灵活，应用场景广泛，而关系型数据库则只支持基础类型。
  - 速度快：nosql可以使用硬盘或者随机存储器作为载体，而关系型数据库只能使用硬盘；
  - 高扩展性；
  - 成本低：nosql数据库部署简单，基本都是开源软件。

- 缺点：
  - 不提供sql支持，学习和使用成本较高；
  - 无事务处理；
  - 数据结构相对复杂，复杂查询方面稍欠。





### 2，MySQL介绍与安装

MySQL 是一个关系型数据库管理系统， 由瑞典 MySQL AB 公司开发， 目前属于 Oracle 公司。MySQL 是一种关系型数据库管理系统，关系型数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。



**关系型数据库：**

- 关系型数据库是建立在关系模型基础上的数据库，简单说，关系型数据库是由多张能互相连接的 二维表 组成的数据库
- 关系型数据库都可以通过SQL进行操作，所以使用方便。
- 数据存储在磁盘中，安全。



`订单信息表` 和 `客户信息表` 都是有行有列二维表我们将这样的称为关系型数据库。如下：

![1718058918398](./assets/1718058918398.png)



**数据模型：**

![1718059255297](./assets/1718059255297.png)

通过客户端可以通过数据库管理系统创建数据库，在数据库中创建表，在表中添加数据。创建的每一个数据库对应到磁盘上都是一个文件夹。一个数据库下可以创建多张表，我们到MySQL中自带的mysql数据库的文件夹目录下：`db.frm` 是表文件， `db.MYD` 是数据文件，通过这两个文件就可以查询到数据展示成二维表的效果。



**MySQL特点：**

- MySQL 是开源的。
- MySQL 支持大型系统的数据库。可以处理拥有上千万条记录的大型数据库。 MySQL 使用标准的 SQL 数据语言形式。
- MySQL 可以运行于多个系统上，并且支持多种语言。这些编程语言包括 C 、C++、 Python 、Java 、Perl 、PHP 等。
- MySQL 存储数据量较大，32 位系统表文件最大可支持 4GB ，64 位系统支持最大的 表文件为 8TB。
- MySQL 是可以定制的，采用了 GPL 协议，你可以修改源码来开发自己的 MySQL 系 统。



**MySQL分类：**

- MySQL分为社区版，社区版是完全开源免费的，社区版也支持多种数据类型和标准的SQL查询语言，能够对数据进行各种查询、增加、删除、修改等操作，所以一般情况下社区版就可以满足开发需求了。
- 企业版，企业版是收费的。即使在开发中需要用到一些付费的附加功能，价格相对于昂贵的 Oracle、DB2等也是有很大优势的。对数据库可靠性要求比较高的企业可以选择企业版。



**MySQL的安装：**大家直接安装小皮就行，简单粗暴，这个小皮软件中，内置了很多的软件，其中就包含mysql。如果我们直接下载mysql安装的话，过程比较烦索。如下：

![1718059108160](./assets/1718059108160.png)



**Navicat for MySQL安装：**安装了mysql，我后我们可以安装Navicat for MySQL软件，这个软件是用来连接mysql管理系统的。连接上后，可以通过可视化操作mysql。

![1718059392985](./assets/1718059392985.png)





### 3，SQL语言介绍

结构化查询语言(Structured Query Language)简称 SQL(发音：sequal['si:kwəl])，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统。

![1718059491071](./assets/1718059491071.png)

SQL能做什么：

- SQL 面向数据库执行查询
- SQL 可在数据库中插入新的记录
- SQL 可更新数据库中的数据
- SQL 可从数据库删除记录
- SQL 可创建新数据库
- SQL 可在数据库中创建新表
- SQL 可在数据库中创建存储过程
- SQL 可在数据库中创建视图
- SQL 可以设置表、存储过程和视图的权限





**SQL语言分类：**

1. 数据查询语言（DQL：Data Query Language）其语句，也称为“数据检索语句”，用以从表中获得数据，确定数据怎样在应用程序给出。关键字 SELECT 是 DQL（也是所有 SQL）用得最多的动词。（对数据进行查询操作，从数据库表中查询到我们想要的数据）

   - SELECT
   - FROM
   - WHERE
   - ORDER BY
   - HAVING

2. 数据操作语言（DML：Data Manipulation Language）其语句包括动词 INSERT，UPDATE 和 DELETE。它们分别用于添加，修改和删除表中的行（对表中数据进行增删改的）。

   - INSERT：添加数据
   - UPDATE：更新数据
   - DELETE：删除数据

   ![1718059634139](./assets/1718059634139.png)

3. 数据定义语言（DDL：Data Definition Language）定义数据库对象语言，其语句包括动词 CREATE 和 DROP 等。（就是用来操作数据库，表等）

   - CREATE：创建数据库对象

   - ALTER：修改数据库对象

   - DROP：删除数据库对象

     ![1718059694547](./assets/1718059694547.png)

4. 数据控制语言（DCL：Data Control Language）它的语句通过 GRANT 或 REVOKE 获得许可，确定用户对数据库对象的访问。

   - GRANT：授予用户某种权限
   - REVOKE：回收授予的某种权限

5. 事务控制语言（TCL ：Transaction Control Language）它的语句能确保被 DML 语句影响的表的所有行及时得以更新。

   - COMMIT：提交事务
   - ROLLBACK：回滚事务
   - SAVEPOINT：设置回滚点





**SQL语言的语法：**

- SQL语句不区分大小写，关键字建议大写。
- SQL语句可以单行或多行书写，以分号结尾。
- 单行注释: -- 注释内容 或 #注释内容(MySQL 特有)。使用-- 添加单行注释时，--后面一定要加空格，而#没有要求。
- 多行注释: /* 注释 */



### 4，DDL操作数据库

使用DDL语句创建数据库：

```sql
CREATE DATABASE  数据库名 DEFAULT CHARACTER SET  字符编码;
```



创建一个test 的数据库，并查看该数据库，以及该数据库的编码。创建数据库：

```sql
create database test default character set utf8;
```

![1718071539779](./assets/1718071539779.png)



查看数据库：

```sql
show databases;
```

![1718071603745](./assets/1718071603745.png)



查看数据库编码：

```sql
select schema_name,default_character_set_name from information_schema.schemata 
where schema_name = 'test';
```

![1718072443525](./assets/1718072443525.png)



使用DDL语言删除数据库:

```sql
DROP DATABASE  数据库名称;
```



删除 test 数据库，删除完后需要刷新一下:

```sql
drop database test;
```



在创建表时，需要先选择数据库:

```sql
USE 数据库名;
```



创建一个名称为 malu 的数据库，编码为 utf8。并选择该数据库：

```sql
create database malu default character set utf8;
use malu;
```



已经有了malu数据库，再去创建时，就会报错，如下：

![1718072709733](./assets/1718072709733.png)



为了避免上面的错误，在创建数据库的时候先做判断，如果不存在再创建。

```sql
CREATE DATABASE IF NOT EXISTS malu DEFAULT CHARACTER SET utf8;
```

![1718072758617](./assets/1718072758617.png)



同理，在删除数据库时，也可以做一个判断：

```sql
DROP DATABASE IF EXISTS malu;
```



![1718072849548](./assets/1718072849548.png)



数据库创建好了，要在数据库中创建表，得先明确在哪儿个数据库中操作，此时就需要使用数据库。

```sql
create database malu default character set utf8;
use malu;
```



查看当前使用的数据库：

```sql
SELECT DATABASE();
```

![1718072948173](./assets/1718072948173.png)







### 5，MYSQL中的数据类型



不同的数据，要开辟不同的存储空间，目的就是为了更加合理地使用存储空间。



**整数类型：**

| **MySQL数据类型** | **含义（有符号）**                   |
| ----------------- | ------------------------------------ |
| tinyint(m)        | 1个字节 范围(-128~127)               |
| smallint(m)       | 2个字节 范围(-32768~32767)           |
| mediumint(m)      | 3个字节 范围(-8388608~8388607)       |
| int(m)            | 4个字节 范围(-2147483648~2147483647) |
| bigint(m)         | 8个字节 范围(+-9.22*10的18次方)      |

数值类型中的长度 m 是指显示长度，并不表示存储长度，只有字段指定 zerofill 时有用。例如： int(3) ，如果实际值是 2 ，如果列指定了 zerofill ，查询结果就是 002 ，左边用 0 来 填充



使用最多的：tinyint和int

- tinyint : 小整数型，占一个字节
- int： 大整数类型，占四个字节。eg： age int





**浮点类型：**

| **MySQL数据类型** | **含义**                                      |
| ----------------- | --------------------------------------------- |
| float(m,d)        | 单精度浮点型 8位精度(4字节) m总个数，d小数位  |
| double(m,d)       | 双精度浮点型 16位精度(8字节) m总个数，d小数位 |

double ： 浮点类型，使用格式： 字段名 double(总长度, 小数点后保留的位数)。eg： score double(5, 2)





**字符类型：**

| **MySQL数据类型** | **含义**                        |
| ----------------- | ------------------------------- |
| char(n)           | 固定长度，最多255个字符         |
| tinytext          | 可变长度，最多255个字符         |
| varchar(n)        | 可变长度，最多65535个字符       |
| text              | 可变长度，最多65535个字符       |
| mediumtext        | 可变长度，最多2的24次方-1个字符 |
| longtext          | 可变长度，最多2的32次方-1个字符 |

char和varchar：

1. char长度固定， 即每条数据占用等长字节空间；适合用在身份证号码、手机号码等定长。
2. varchar可变长度，可以设置最大长度；适合用在长度可变的属性。
3. text不设置长度， 当不知道属性的最大长度时，适合用text。
4. 按照查询速度： char最快， varchar次之，text最慢。



总结 char和varchar：

- char： 定长字符串。优点：存储性能高。缺点：浪费空间。eg： name char(10) 如果存储的数据字符个数不足10个，也会占10个的空间
- varchar： 变长字符串。优点：节约空间。缺点：存储性能底。eg： name varchar(10) 如果存储的数据字符个数不足10个，那就数据字符个数是几就占几个的空间



字符串型使用建议：

1. 经常变化的字段用varchar
2. 知道固定长度的用char
3. 尽量用varchar
4. 超过255字符的只能用varchar或者text
5. 能用varchar的地方不用text



**日期类型：**

| **MySQL数据类型** | **含义**                     |
| ----------------- | ---------------------------- |
| date              | 日期 YYYY-MM-DD              |
| time              | 时间 HH:MM:SS                |
| datetime          | 日期时间 YYYY-MM-DD HH:MM:SS |
| timestamp         | 时间戳YYYYMMDD HHMMSS        |

- date：日期值。只包含年月日。eg：birthday date ：
- datetime ： 混合日期和时间值。包含年月日时分秒



**二进制数据(BLOB)：**

1. BLOB和TEXT存储方式不同，TEXT以文本方式存储，英文存储区分大小写，而Blob是以二进制方式存储，不分大小写。
2. BLOB存储的数据只能整体读出。
3. TEXT可以指定字符集，BLOB不用指定字符集。



总结：

![1718060762837](./assets/1718060762837.png)



### 5，DDL操作表



使用DDL语句创建表：

```sql
CREATE TABLE 表名(列名 类型,列名 类型......);
```



创建一个 employees 表包含雇员 ID ，雇员名字，雇员薪水:

```sql
create table employees(employee_id int,employee_name varchar(10),salary float(8,2));
```

![1718074022419](./assets/1718074022419.png)



查看已创建的表:

```sql
show tables;
```

![1718074050707](./assets/1718074050707.png)

使用DDL语句删除表：

```sql
DROP TABLE 表名;
```



删除 employees 表:

```sql
drop table employees;
```



使用DDL语句修改表:

```sql
ALTER TABLE  旧表名 RENAME  新表名;
```



创建一个 employees 表包含雇员 ID ，雇员名字，雇员薪水:

```sql
create table employees(employee_id int,employee_name varchar(10),salary float(8,2));
```



将 employees 表名修改为 emp:

```sql
alter table employees rename emp;
```



使用DDL语句修改列名:

```sql
ALTER TABLE  表名 CHANGE COLUMN  旧列名 新列名 类型;
```



将 emp 表中的 employee_name 修改为 name:

```sql
alter table emp change column employee_name name varchar(20);
```



使用DDL语句修改列类型:

```sql
ALTER TABLE  表名 MODIFY  列名 新类型;
```



将 emp 表中的 name 的长度指定为 40:

```sql
alter table emp modify name varchar(40);
```



使用DDL语句添加新列:

```sql
ALTER TABLE  表名 ADD COLUMN  新列名 类型;
```



在 emp 表中添加佣金列，列名为 commission_pct:

```sql
alter table emp add column commission_pct float(4,2);
```



使用DDL语句删除指定的列:

```sql
ALTER TABLE  表名 DROP COLUMN  列名;
```



删除 emp 表中的 commission_pct:

```sql
alter table emp drop column commission_pct;
```



### 6，MySQL中的约束



看如下表：

![1718061428571](./assets/1718061428571.png)

上面表存在的一些问题：

- id 列一般是用标示数据的唯一性的，而上述表中的id为1的有三条数据，并且 `马花疼` 没有id进行标示

- `柳白` 这条数据的age列的数据是3000，而人也不可能活到3000岁

- `马运` 这条数据的math数学成绩是-5，而数学学得再不好也不可能出现负分

- `柳青` 这条数据的english列（英文成绩）值为null，而成绩即使没考也得是0分

  

针对上面的遇到的问题，从数据库层面在添加数据的时候进行限制，这个就是约束。约束是作用于表中列上的规则，用于限制加入表的数据。例如：我们可以给id列加约束，让其值不能重复，不能为null值。



约束的存在保证了数据库中数据的正确性、有效性和完整性。添加约束可以在添加数据的时候就限制不正确的数据，年龄是3000，数学成绩是-5分这样无效的数据，继而保障数据的完整性。




数据库约束是对表中的数据进行进一步的限制，保证数据的正确性、有效性和完整性。

- 主键约束(Primary Key) PK

  > 主键约束是使用最频繁的约束。在设计数据表时，一般情况下，都会要求表中设置一个主键。 主键是表的一个特殊字段，该字段能唯一标识该表中的每条信息。例如，学生信息表中的学号是唯一的。
  >
  > 主键是一行数据的唯一标识，要求非空且唯一。一般我们都会给没张表添加一个主键列用来唯一标识数据。
  >
  > 例如：上图表中id就可以作为主键，来标识每条数据。那么这样就要求数据中id的值不能重复，不能为null值。

- 外键约束(Foreign Key) FK

  > 外键约束经常和主键约束一起使用，用来确保数据的一致性。
  >
  > 外键用来让两个表的数据之间建立链接，保证数据的一致性和完整性。
  >
  > 外键约束现在可能还不太好理解，后面我们会重点进行讲解。

- 唯一性约束(Unique)

  > 唯一约束与主键约束有一个相似的地方，就是它们都能够确保列的唯一性。与主键约束不同的是，唯一约束在一个表中可以有多个，并且设置唯一约束的列是允许有空值的。
  >
  > 保证列中所有数据各不相同。
  >
  > 例如：id列中三条数据的值都是1，这样的数据在添加时是绝对不允许的。

- 非空约束(Not Null)

  > 非空约束用来约束表中的字段不能为空。
  >
  > 保证列中所有的数据不能有null值。
  >
  > 例如：id列在添加 `马花疼` 这条数据时就不能添加成功。

- 检查约束(Check)

  > 检查约束也叫用户自定义约束，是用来检查数据表中，字段值是否有效的一个手段，但目前 MySQL 数据库不支持检查约束。
  >
  > 保证列中的值满足某一条件。
  >
  > 例如：我们可以给age列添加一个范围，最低年龄可以设置为1，最大年龄就可以设置为300，这样的数据才更合理些。
  >
  > MySQL不支持检查约束。从数据库层面不能保证，以后可以在java代码中进行限制，一样也可以实现要求。

- 默认约束： 关键字是 DEFAULT

  > 保存数据时，未指定值则采用默认值。
  >
  > 例如：我们在给english列添加该约束，指定默认值是0，这样在添加数据时没有指定具体值时就会采用默认给定的0。





### 7，DQL

#### a）select基本查询介绍

![1718061914243](./assets/1718061914243.png)



SELECT 语句从数据库中返回信息。使用一个 SELECT 语句，可以做下面的事：

- **列选择：**能够使用 SELECT 语句的列选择功能选择表中的列，这些列是想

  要用查询返回的。当查询时，能够返回列中的数据。

- **行选择：**能够使用 SELECT 语句的行选择功能选择表中的行，这些行是想

  要用查询返回的。能够使用不同的标准限制看见的行。

- **连接：**能够使用 SELECT 语句的连接功能来集合数据，这些数据被存储在不

  同的表中，在它们之间可以创建连接，查询出我们所关心的数据。



**SELECT基本语法：**

在最简单的形式中，SELECT 语句必须包含下面的内容：

- 一个 SELECT 子句，指定被显示的列
- 一个 FROM 子句，指定表，该表包含 SELECT 子句中的字段列表

| 语句                 | 含义                   |
| -------------------- | ---------------------- |
| SELECT               | 是一个或多个字段的列表 |
| *                    | 选择所有的列           |
| DISTINCT             | 禁止重复               |
| column \| expression | 选择指定的字段或表达式 |
| alias                | 给所选择的列不同的标题 |
| FROM table           | 指定包含列的表         |



#### b）选择所有列和指定列

选择所有列：

![1718062227900](./assets/1718062227900.png)

用跟在 SELECT 关键字后面的星号 (*)，你能够显示表中数据的所有列。

示例：查询 departments 表中的所有数据。

```sql
select * from departments;
```



选择指定列：

![1718062314733](./assets/1718062314733.png)

能够用 SELECT 语句来显示表的指定列，指定列名之间用逗号分隔。

示例：查询 departments 表中所有部门名称。

```sql
select department_name from departments;
```



#### c）查询中的算术表达式

![1718062404398](./assets/1718062404398.png)



需要修改数据显示方式，如执行计算，或者作假定推测，这些都可能用到算术表达式。一个算术表达式可以包含列名、固定的数字值和算术运算符。

![1718062434384](./assets/1718062434384.png)

查询雇员的年薪，并显示他们的雇员ID，名字。

```sql
select employees_id,last_name, 12*salary from employees;
```



运算符的优先级：

![1718062530856](./assets/1718062530856.png)



如果算术表达式包含有一个以上的运算，乘法和除法先计算。如果在一个表达式中的运算符优先级相同，计算从左到右进行。可以用圆括号强制其中的表达式先计算。



计算 employees 表中的员工全年薪水加 100 以后的薪水是多少，并显示他们的员工ID与名字。

```sql
select employees_id,last_name, 12*salary+100 from employees;
```



计算 employees 表中的员工薪水加 100 以后的全年薪水是多少，并显示他们的员工ID与名字。

```sql
select employees_id,last_name, 12*(salary+100) from employees;
```



#### d）mysql中定义空值

![1718062601469](./assets/1718062601469.png)



如果一行中的某个列缺少数据值，该值被置为 *null*， 或者说包含一个空。空是一个难以获得的、未分配的、未知的，或不适用的值。空和 0 或者空格不相同。 0 是一个数字，而空格是一个字符。



**算术表达式中的空值**

![1718062651998](./assets/1718062651998.png)

计算年薪包含佣金：

```sql
select 12*salary*commission_pct from employees;
```





#### e）别名

**使用列别名：**

![1718062728661](./assets/1718062728661.png)

```sql
SELECT  列名 AS  列别名 FROM  表名 WHERE  条件;
```



查询 employees 表将雇员 last_name 列定义别名为 name:

```sql
select last_name as name from employees;
select last_name name from employees;
```





**使用表别名:**

```sql
SELECT  表别名.列名 FROM  表名 as 表别名 WHERE  条件;
```

查询 employees 表为表定义别名为emp，将雇员 last_name 列定义别名为 name：

```sql
select emp.last_name name from employees emp;
```



#### f）mysql去重复

![1718062879993](./assets/1718062879993.png)



去掉相同的行：

![1718062977996](./assets/1718062977996.png)

```sql
SELECT DISTINCT 列名 FROM 表名;
```



查询 employees 表，显示唯一的部门 ID:

```sql
select distinct department_id from employees;
```



#### g）查询中的行选择

![1718063129380](./assets/1718063129380.png)

用 WHERE 子句限制从查询返回的行。一个 WHERE 子句包含一个必须满足的条件，WHERE 子句紧跟着 FROM 子句。如果条件是 true，返回满足条件的行。

在语法中：WHERE 限制查询满足条件的行。*condition* 由列名、表达式、常数和比较操作组成

```sql
SELECT * |  投影列 FROM  表名 WHERE  选择条件;
```



查询 departments 表中部门 ID 为 90 的部门名称与工作地点 ID:

```sql
select department_name,location_id from departments where department_id =4;
```



#### h）mysql中的比较条件

![1718063227314](./assets/1718063227314.png)

符号 != 也能够表示 不等于条件。



查询 employees 表中员工薪水大于等于 3000 的员工的姓名与薪水：

```sql
select last_name,salary from employees where salary >= 3000;
```





查询 employees 表中员工薪水不等于 5000 的员工的姓名与薪水:

```sql
select last_name,salary from employees where salary<>5000;
```





#### i）其它比较条件

![1718063310931](./assets/1718063310931.png)





可以用 BETWEEN 范围条件显示基于一个值范围的行。指定的范围包含一个下限和一个上限。

![1718063351561](./assets/1718063351561.png)

查询 employees 表，薪水在 3000-8000 之间的雇员ID、名字与薪水：

```sql
select employee_id,last_name,salary from employees where salary between 3000 and 8000;
```







使用IN条件:

![1718063416740](./assets/1718063416740.png)



查询 employees 表，找出薪水是 5000,6000,8000 的雇员ID、名字与薪水:

```sql
select employee_id,last_name,salary from employees where salary in(5000,6000,8000);
```



使用LIKE条件:

![1718063469624](./assets/1718063469624.png)



查询 employees 中雇员名字第二个字母是 e 的雇员名字:

```sql
select last_name from employees where last_name like '_e%';
```



使用NULL条件:

![1718063599313](./assets/1718063599313.png)

NULL 条件，包括 IS NULL 条件和 IS NOT NULL 条件。IS NULL 条件用于空值测试。空值的意思是难以获得的、未指定的、未知的或者不适用的。因此，你不能用 = ，因为 null 不能等于或不等于任何值。



找出 emloyees 表中那些没有佣金的雇员雇员ID、名字与佣金:

```sql
select employee_id,last_name,commission_pct from employees where commission_pct is null;
```



找出 employees 表中那些有佣金的雇员ID、名字与佣金:

```sql
select employee_id,last_name,commission_pct from employees where commission_pct is not null;
```



#### j）逻辑条件

![1718063659054](./assets/1718063659054.png)



逻辑条件组合两个比较条件的结果来产生一个基于这些条件的单个的结果，或者逆转一个单个条件的结果。当所有条件的结果为真时，返回行。SQL 的三个逻辑运算符是：

- AND
- OR
- NOT

可以在 WHERE 子句中用 AND 和 OR 运算符使用多个条件。



查询 employees 表中雇员薪水是 8000 的并且名字中含有e 的雇员名字与薪水：

```sql
select last_name,salary from employees where salary = 8000 and last_name like '%e%';
```



查询 employees 表中雇员薪水是 8000 的或者名字中含有e 的雇员名字与薪水:

```sql
select last_name,salary from employees where salary = 8000 or last_name like '%e%';
```



查询 employees 表中雇员名字中不包含 u 的雇员的名字:

```sql
select last_name from employees where last_name not like '%u%';
```



#### k）优先规则

![1718063833797](./assets/1718063833797.png)



![1718063865295](./assets/1718063865295.png)



在图片的例子中，有两个条件：

- 第一个条件是 job_id 是 AD_PRES 并且薪水高于 15,000。
- 第二个条件是 job_id 是 SA_REP。



![1718063901509](./assets/1718063901509.png)

在图片中的例子有两个条件：

- 第一个条件是 job_id 是 AD_PRES 或者 SA_REP 。
- 第二个条件是薪水高于$15,000





#### l）order by排序

![1718064001546](./assets/1718064001546.png)



在一个不明确的查询结果中排序返回的行。ORDER BY 子句用于排序。如果使用了 ORDER BY 子句，它必须位于 SQL 语句的最后。

**SELECT 语句的执行顺序如下：**

- FROM 子句
- WHERE 子句
- SELECT 子句
- ORDER BY 子句



查询 employees 表中的所有雇员，显示他们的ID、名字与薪水，并按薪水升序排序：

```sql
select employee_id,last_name,salary from employees order by salary;
select employee_id,last_name,salary from employees order by salary asc;
```



查询 employees 表中的所有雇员，显示他们的ID与名字，并按雇员名字降序排序:

```sql
select employee_id,last_name from employees order by last_name desc;
```



使用别名排序：

![1718064121482](./assets/1718064121482.png)

显示雇员ID，名字。计算雇员的年薪，年薪列别名为annsal，并对该列进行升序排序:

```sql
select employee_id,last_name ,12*salary annsal from employees order by annsal;
```



多列排序：

![1718064176462](./assets/1718064176462.png)



以升叙排序显示 DEPARTMENT_ID 列，同时以降序排序显示 SALARY 列：

```sql
select department_id,salary from employees order by department_id asc ,salary desc;
```



#### m）实操

创建一个查询，显示收入超过 12,000 的雇员的名字和薪水。

```sql
select 
LAST_NAME,SALARY
from employees
WHERE SALARY > 12000;
```



创建一个查询，显示雇员号为 176 的雇员的名字和部门号。

```sql
SELECT
LAST_NAME,DEPARTMENT_ID
from employees
where EMPLOYEE_ID = 176;
```



显示所有薪水不在 5000 和 12000 之间的雇员的名字和薪水。

```sql
select
LAST_NAME,SALARY
from employees
where salary not BETWEEN 5000 and 12000;
```



显示所有在部门 20 和 50 中的雇员的名字和部门号，并以名字按字母顺序排序。

```sql
LAST_NAME,DEPARTMENT_ID
FROM employees
WHERE DEPARTMENT_ID IN (20,50)
ORDER BY LAST_NAME asc;
```



列出收入在 5,000 和 12,000 之间，并且在部门 20 或50 工作的雇员的名字和薪水。将列标题分别显示为 Employee 和 Monthly Salary

```sql
SELECT
LAST_NAME Employee,SALARY 'Monthly Salary'
FROM employees
WHERE SALARY BETWEEN 5000 and 12000
AND
DEPARTMENT_ID in(20,50);
```



显示所有没有主管经理的雇员的名字和工作岗位。

```sql
SELECT
LAST_NAME,JOB_ID
FROM employees
WHERE MANAGER_ID is null;
```



显示所有有佣金的雇员的名字、薪水和佣金。以薪水和佣金的降序排序数据。

```sql
SELECT
LAST_NAME,SALARY,COMMISSION_PCT
from employees
where COMMISSION_PCT is not NULL
ORDER BY SALARY DESC , COMMISSION_PCT desc;
```



显示所有名字中有一个 a 和一个 e 的雇员的名字。

```sql
SELECT
LAST_NAME
from employees
where LAST_NAME LIKE '%a%'
AND
LAST_NAME LIKE '%e%';
```



显示所有工作岗位是销售代表（SA_REP）或者普通职员(ST_CLERK)，并且薪水不等于 2,500、3,500 或 7,000 的雇员的名字、工作岗位和薪水。

```sql
SELECT
LAST_NAME,JOB_ID,SALARY
from employees
WHERE
JOB_ID in('SA_REP','ST_CLERK')
AND
SALARY not IN(2500,3500,7000)
```



### 8，sql中的函数

![1718064492868](./assets/1718064492868.png)

函数是 SQL 的一个非常强有力的特性，函数能够用于下面的目的：

- 执行数据计算
- 修改单个数据项
- 操纵输出进行行分组
- 格式化显示的日期和数字
- 转换列数据类型



SQL 函数有输入参数，并且总有一个返回值。函数分类：

![1718064568547](./assets/1718064568547.png)



1. 单行函数：单行函数仅对单个行进行运算，并且每行返回一个结果。

   常见的函数类型：

   - 字符
   - 数字
   - 日期
   - 转换

2. 多行函数：多行函数能够操纵成组的行，每个行组给出一个结果，这些函数也被称为组函数。



#### a）单行函数

![1718064677329](./assets/1718064677329.png)



**单行函数分类：**

![1718064728223](./assets/1718064728223.png)



#### b）字符函数

**大小写处理函数：**

| **函数**           | 描述                  | 实例                                                        |
| ------------------ | --------------------- | ----------------------------------------------------------- |
| LOWER(s)\|LCASE(s) | 将字符串 s 转换为小写 | 将字符串 OLDLU转换为小写：`SELECT LOWER("OLDLU"); -- oldlu` |
| UPPER(s)\|UCASE(s) | 将字符串s转换为大写   | 将字符串 oldlu转换为大写：`SELECT UPPER("oldlu"); -- OLDLU` |



显示雇员 Davies 的雇员号、姓名和部门号，将姓名转换为大写。

```sql
select employee_id,UPPER(last_name),department_id from employees where last_name = 'davies';
```



**字符处理函数:**

| 函数                        | 描述                                                      | 实例                                                         |
| --------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| LENGTH(s)                   | 返回字符串 s 的长度                                       | 返回字符串oldlu的字符数`SELECT LENGTH("oldlu"); --5;`        |
| CONCAT(s1,s2...sn)          | 字符串 s1,s2 等多个字符串合并为一个字符串                 | 合并多个字符串`SELECT CONCAT("sxt ", "teacher ", "oldlu"); --sxt teacher oldlu;` |
| LPAD(s1,len,s2)             | 在字符串 s1 的开始处填充字符串 s2，使字符串长度达到 len   | 将字符串 x 填充到 oldlu字符串的开始处：`SELECT LPAD('oldlu',8,'x'); -- xxxoldlu` |
| LTRIM(s)                    | 去掉字符串 s 开始处的空格                                 | 去掉字符串 oldlu开始处的空格：`SELECT LTRIM(" oldlu") ;-- oldlu` |
| REPLACE(s,s1,s2)            | 将字符串 s2 替代字符串 s 中的字符串 s1                    | 将字符串 oldlu 中的字符 o 替换为字符 O：`SELECT REPLACE('oldlu','o','O'); --Oldlu` |
| REVERSE(s)                  | 将字符串s的顺序反过来                                     | 将字符串 abc 的顺序反过来：`SELECT REVERSE('abc'); -- cba`   |
| RPAD(s1,len,s2)             | 在字符串 s1 的结尾处添加字符串 s2，使字符串的长度达到 len | 将字符串 xx填充到 oldlu字符串的结尾处：`SELECT RPAD('oldlu',8,'x'); -- oldluxxx` |
| RTRIM(s)                    | 去掉字符串 s 结尾处的空格                                 | 去掉字符串 oldlu 的末尾空格：`SELECT RTRIM("oldlu "); -- oldlu` |
| SUBSTR(s, start, length)    | 从字符串 s 的 start 位置截取长度为 length 的子字符串      | 从字符串 OLDLU中的第 2 个位置截取 3个 字符：`SELECT SUBSTR("OLDLU", 2, 3); -- LDL` |
| SUBSTRING(s, start, length) | 从字符串 s 的 start 位置截取长度为 length 的子字符串      | 从字符串 OLDLU中的第 2 个位置截取 3个 字符：`SELECT SUBSTRING("OLDLU", 2, 3); --LDL` |
| TRIM(s)                     | 去掉字符串 s 开始和结尾处的空格                           | 去掉字符串 oldlu 的首尾空格：`SELECT TRIM(' oldlu ');--oldlu` |



显示所有工作岗位名称从第 4 个字符位置开始，包含字符串 REP的雇员的ID信息，将雇员的姓和名连接显示在一起，还显示雇员名的的长度，以及名字中字母 a 的位置。

```sql
SELECT employee_id, CONCAT(last_name，first_name) NAME, 
job_id, LENGTH(last_name),INSTR(last_name, 'a') "Contains 'a'?" FROM employees WHERE SUBSTR(job_id, 4) = 'REP';
```



#### c）数学函数

| 函数名                             | 描述                                                         | 实例                                                         |
| ---------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ABS(x)                             | 返回 x 的绝对值                                              | 返回 -1 的绝对值：`SELECT ABS(-1) -- 返回1`                  |
| ACOS(x)                            | 求 x 的反余弦值(参数是弧度)                                  | `SELECT ACOS(0.25);`                                         |
| ASIN(x)                            | 求反正弦值(参数是弧度)                                       | `SELECT ASIN(0.25);`                                         |
| ATAN(x)                            | 求反正切值(参数是弧度)                                       | `SELECT ATAN(2.5);`                                          |
| ATAN2(n, m)                        | 求反正切值(参数是弧度)                                       | `SELECT ATAN2(-0.8, 2);`                                     |
| AVG(expression)                    | 返回一个表达式的平均值，expression 是一个字段                | 返回 Products 表中Price 字段的平均值：`SELECT AVG(Price) AS AveragePrice FROM Products;` |
| CEIL(x)                            | 返回大于或等于 x 的最小整数                                  | `SELECT CEIL(1.5) -- 返回2`                                  |
| CEILING(x)                         | 返回大于或等于 x 的最小整数                                  | `SELECT CEILING(1.5); -- 返回2`                              |
| COS(x)                             | 求余弦值(参数是弧度)                                         | `SELECT COS(2);`                                             |
| COT(x)                             | 求余切值(参数是弧度)                                         | `SELECT COT(6);`                                             |
| COUNT(expression)                  | 返回查询的记录总数，expression 参数是一个字段或者 * 号       | 返回 Products 表中 products 字段总共有多少条记录：`SELECT COUNT(ProductID) AS NumberOfProducts FROM Products;` |
| DEGREES(x)                         | 将弧度转换为角度                                             | `SELECT DEGREES(3.1415926535898) -- 180`                     |
| n DIV m                            | 整除，n 为被除数，m 为除数                                   | 计算 10 除于 5：`SELECT 10 DIV 5; -- 2`                      |
| EXP(x)                             | 返回 e 的 x 次方                                             | 计算 e 的三次方：`SELECT EXP(3) -- 20.085536923188`          |
| FLOOR(x)                           | 返回小于或等于 x 的最大整数                                  | 小于或等于 1.5 的整数：`SELECT FLOOR(1.5) -- 返回1`          |
| GREATEST(expr1, expr2, expr3, ...) | 返回列表中的最大值                                           | 返回以下数字列表中的最大值：`SELECT GREATEST(3, 12, 34, 8, 25); -- 34`返回以下字符串列表中的最大值：`SELECT GREATEST("Google", "Runoob", "Apple"); -- Runoob` |
| LEAST(expr1, expr2, expr3, ...)    | 返回列表中的最小值                                           | 返回以下数字列表中的最小值：`SELECT LEAST(3, 12, 34, 8, 25); -- 3`返回以下字符串列表中的最小值：`SELECT LEAST("Google", "Runoob", "Apple"); -- Apple` |
| LN                                 | 返回数字的自然对数，以 e 为底。                              | 返回 2 的自然对数：`SELECT LN(2); -- 0.6931471805599453`     |
| LOG(x) 或 LOG(base, x)             | 返回自然对数(以 e 为底的对数)，如果带有 base 参数，则 base 为指定带底数。 | `SELECT LOG(20.085536923188) -- 3 SELECT LOG(2, 4); -- 2`    |
| LOG10(x)                           | 返回以 10 为底的对数                                         | `SELECT LOG10(100) -- 2`                                     |
| LOG2(x)                            | 返回以 2 为底的对数                                          | 返回以 2 为底 6 的对数：`SELECT LOG2(6); -- 2.584962500721156` |
| MAX(expression)                    | 返回字段 expression 中的最大值                               | 返回数据表 Products 中字段 Price 的最大值：`SELECT MAX(Price) AS LargestPrice FROM Products;` |
| MIN(expression)                    | 返回字段 expression 中的最小值                               | 返回数据表 Products 中字段 Price 的最小值：`SELECT MIN(Price) AS MinPrice FROM Products;` |
| MOD(x,y)                           | 返回 x 除以 y 以后的余数                                     | 5 除于 2 的余数：`SELECT MOD(5,2) -- 1`                      |
| PI()                               | 返回圆周率(3.141593）                                        | `SELECT PI() --3.141593`                                     |
| POW(x,y)                           | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POW(2,3) -- 8`                          |
| POWER(x,y)                         | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POWER(2,3) -- 8`                        |
| RADIANS(x)                         | 将角度转换为弧度                                             | 180 度转换为弧度：`SELECT RADIANS(180) -- 3.1415926535898`   |
| RAND()                             | 返回 0 到 1 的随机数                                         | `SELECT RAND() --0.93099315644334`                           |
| ROUND(x)                           | 返回离 x 最近的整数                                          | `SELECT ROUND(1.23456) --1`                                  |
| SIGN(x)                            | 返回 x 的符号，x 是负数、0、正数分别返回 -1、0 和 1          | `SELECT SIGN(-10) -- (-1)`                                   |
| SIN(x)                             | 求正弦值(参数是弧度)                                         | `SELECT SIN(RADIANS(30)) -- 0.5`                             |
| SQRT(x)                            | 返回x的平方根                                                | 25 的平方根：`SELECT SQRT(25) -- 5`                          |
| SUM(expression)                    | 返回指定字段的总和                                           | 计算 OrderDetails 表中字段 Quantity 的总和：`SELECT SUM(Quantity) AS TotalItemsOrdered FROM OrderDetails;` |
| TAN(x)                             | 求正切值(参数是弧度)                                         | `SELECT TAN(1.75); -- -5.52037992250933`                     |
| TRUNCATE(x,y)                      | 返回数值 x 保留到小数点后 y 位的值（与 ROUND 最大的区别是不会进行四舍五入） | `SELECT TRUNCATE(1.23456,3) -- 1.234`                        |



ROUND 函数四舍五入列、表达式或者 n 位小数的值。如果第二个参数是 0 或者缺少，值被四舍五入为整数。如果第二个参数是 2值被四舍五入为两位小数。如果第二个参数是–2，值被四舍五入到小数点左边两位。

```sql
SELECT ROUND(45.923,2), ROUND(45.923,0),ROUND(45.923,-1);
```



TRUNCATE函数的作用类似于 ROUND 函数。如果第二个参数是 0 或者缺少，值被截断为整数。如果第二个参数是 2，值被截断为两位小数。如果第二个参数是–2，值被截断到小数点左边两位。与 ROUND 最大的区别是不会进行四舍五入。

```sql
SELECT TRUNCATE(45.923,2);
```



MOD 函数找出m 除以n的余数。所有job_id是SA_REP的雇员的名字，薪水以及薪水被5000除后的余数。

```sql
SELECT last_name, salary, MOD(salary, 5000) FROM employees
WHERE job_id = 'SA_REP';
```





#### d）日期函数

在MySQL中允许直接使用字符串表示日期，但是要求字符串的日期格式必须为：‘YYYY-MM-DD HH:MI:SS’ 或者‘YYYY/MM/DD HH:MI:SS’;

| 函数名                 | 描述                                              | 实例                                                   |
| ---------------------- | ------------------------------------------------- | ------------------------------------------------------ |
| CURDATE()              | 返回当前日期                                      | `SELECT CURDATE(); -> 2018-09-19`                      |
| CURTIME()              | 返回当前时间                                      | `SELECT CURTIME(); -> 19:59:02`                        |
| CURRENT_DATE()         | 返回当前日期                                      | `SELECT CURRENT_DATE(); -> 2018-09-19`                 |
| CURRENT_TIME()         | 返回当前时间                                      | `SELECT CURRENT_TIME(); -> 19:59:02`                   |
| DATE()                 | 从日期或日期时间表达式中提取日期值                | `SELECT DATE("2017-06-15"); -> 2017-06-15`             |
| DATEDIFF(d1,d2)        | 计算日期 d1->d2 之间相隔的天数                    | `SELECT DATEDIFF('2001-01-01','2001-02-02') -> -32`    |
| DAY(d)                 | 返回日期值 d 的日期部分                           | `SELECT DAY("2017-06-15"); -> 15`                      |
| DAYNAME(d)             | 返回日期 d 是星期几，如 Monday,Tuesday            | `SELECT DAYNAME('2011-11-11 11:11:11') ->Friday`       |
| DAYOFMONTH(d)          | 计算日期 d 是本月的第几天                         | `SELECT DAYOFMONTH('2011-11-11 11:11:11') ->11`        |
| DAYOFWEEK(d)           | 日期 d 今天是星期几，1 星期日，2 星期一，以此类推 | `SELECT DAYOFWEEK('2011-11-11 11:11:11') ->6`          |
| DAYOFYEAR(d)           | 计算日期 d 是本年的第几天                         | `SELECT DAYOFYEAR('2011-11-11 11:11:11') ->315`        |
| HOUR(t)                | 返回 t 中的小时值                                 | `SELECT HOUR('1:2:3') -> 1`                            |
| LAST_DAY(d)            | 返回给给定日期的那一月份的最后一天                | `SELECT LAST_DAY("2017-06-20"); -> 2017-06-30`         |
| MONTHNAME(d)           | 返回日期当中的月份名称，如 November               | `SELECT MONTHNAME('2011-11-11 11:11:11') -> November`  |
| MONTH(d)               | 返回日期d中的月份值，1 到 12                      | `SELECT MONTH('2011-11-11 11:11:11') ->11`             |
| NOW()                  | 返回当前日期和时间                                | `SELECT NOW() -> 2018-09-19 20:57:43`                  |
| SECOND(t)              | 返回 t 中的秒钟值                                 | `SELECT SECOND('1:2:3') -> 3`                          |
| SYSDATE()              | 返回当前日期和时间                                | `SELECT SYSDATE() -> 2018-09-19 20:57:43`              |
| TIMEDIFF(time1, time2) | 计算时间差值                                      | `SELECT TIMEDIFF("13:10:11", "13:10:10"); -> 00:00:01` |
| TO_DAYS(d)             | 计算日期 d 距离 0000 年 1 月 1 日的天数           | `SELECT TO_DAYS('0001-01-01 01:01:01') -> 366`         |
| WEEK(d)                | 计算日期 d 是本年的第几个星期，范围是 0 到 53     | `SELECT WEEK('2011-11-11 11:11:11') -> 45`             |
| WEEKDAY(d)             | 日期 d 是星期几，0 表示星期一，1 表示星期二       | `SELECT WEEKDAY("2017-06-15"); -> 3`                   |
| WEEKOFYEAR(d)          | 计算日期 d 是本年的第几个星期，范围是 0 到 53     | `SELECT WEEKOFYEAR('2011-11-11 11:11:11') -> 45`       |
| YEAR(d)                | 返回年份                                          | `SELECT YEAR("2017-06-15"); -> 2017`                   |



向 employees 表中添加一条数据，雇员ID：300，名字：kevin ，email：mailto:kevin@qq.com，入职时间：2049-5-1 8:30:30，工作部门：‘IT_PROG’。

```sql
insert into employees(EMPLOYEE_ID,last_name,email,HIRE_DATE,JOB_ID) values(300,'kevin','kevin@qq.com','2049-5-1 8:30:30','IT_PROG');
```



#### e）转换函数

**隐式数据类型转换**

隐式数据类型转换是指MySQL服务器能够自动地进行类型转换。如：可以将标准格式的字串日期自动转换为日期类型。

MySQL字符串日期格式为：‘YYYY-MM-DD HH:MI:SS’ 或 ‘YYYY/MM/DD HH:MI:SS’;



**显示数据类型转换**

显示数据类型转换是指需要依赖转换函数来完成相关类型的转换。

如：

- DATE_FORMAT(date,format) 将日期转换成字符串;
- STR_TO_DATE(str,format) 将字符串转换成日期;



向 employees 表中添加一条数据，雇员ID：400，名字：oldlu ，email：mailto:oldlu@qq.com ，入职时间：2049 年 5 月 5 日，工作部门：‘IT_PROG’。

```sql
insert into employees(EMPLOYEE_ID,last_name,email,HIRE_DATE,JOB_ID) values(400,'oldlu','oldlu@qq.com ', STR_TO_DATE('2049 年 5 月 5 日','%Y 年%m 月%d 日'),'IT_PROG');
```



查询 employees 表中雇员名字为 King 的雇员的入职日期，要求显示格式为 yyyy 年 MM 月 dd 日。

```sql
select DATE_FORMAT(hire_date,'%Y 年%m 月%d 日') from employees where last_name = 'King';
```



#### f）通用函数

| 函数名                                                       | 描述                                                         | 实例                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| IF(expr,v1,v2)                                               | 如果表达式 expr 成立，返回结果 v1；否则，返回结果 v2。       | `SELECT IF(1 > 0,'正确','错误') ->正确`                      |
| IFNULL(v1,v2)                                                | 如果 v1 的值不为 NULL，则返回 v1，否则返回 v2。              | `SELECT IFNULL(null,'Hello Word') ->Hello Word`              |
| ISNULL(expression)                                           | 判断表达式是否为 NULL                                        | `SELECT ISNULL(NULL); ->1`                                   |
| NULLIF(expr1, expr2)                                         | 比较两个参数是否相同，如果参数 expr1 与 expr2 相等 返回 NULL，否则返回 expr1 | `SELECT NULLIF(25, 25); ->`                                  |
| COALESCE(expr1, expr2, ...., expr_n)                         | 返回参数中的第一个非空表达式（从左向右）                     | `SELECT COALESCE(NULL, NULL, NULL, 'bjsxt.com', NULL, 'google.com'); -> bjsxt.com` |
| `CASE expression WHEN condition1 THEN result1 WHEN condition2 THEN result2 ... WHEN conditionN THEN resultN ELSE result END` | CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了。 | `SELECT CASE 'oldlu' WHEN 'oldlu' THEN 'OLDLU' WHEN 'admin' THEN 'ADMIN' ELSE 'kevin' END;` |



查询部门编号是50或者80的员工信息，包含他们的名字、薪水、佣金。在income列中，如果有佣金则显示‘SAL+COMM’，无佣金则显示'SAL'。

```sql
SELECT last_name, salary, commission_pct,   if(ISNULL(commission_pct),
'SAL','SAL+COMM') income 
FROM employees 
WHERE department_id IN (50, 80);
```



计算雇员的年报酬，你需要用 12 乘以月薪，再加上它的佣金 (等于年薪乘以佣金百分比)。

```sql
SELECT last_name, salary, IFNULL(commission_pct, 0), (salary*12) +(salary*12*IFNULL(commission_pct, 0)) AN_SAL 
FROM employees;
```



查询员工表，显示他们的名字、名字的长度该列名为expr1，姓氏、姓氏的长度该列名为expr2。在result列中，如果名字与姓氏的长度相同则显示空，如果不相同则显示名字长度。

```sql
SELECT first_name, LENGTH(first_name) "expr1",
    last_name, LENGTH(last_name) "expr2",    NULLIF(LENGTH(first_name), LENGTH(last_name)) result
FROM employees;
```



查询员工表，显示他们的名字，如果 COMMISSION_PCT 值是非空，显示它。如果COMMISSION_PCT 值是空，则显示 SALARY 。如果 COMMISSION_PCT 和SALARY 值都是空，那么显示 10。在结果中对佣金列升序排序。

```sql
SELECT last_name,
COALESCE(commission_pct, salary, 10) comm 
FROM employees 
ORDER BY commission_pct;
```



查询员工表，如果 JOB_ID 是 IT_PROG，薪水增加 10%；如果 JOB_ID 是 ST_CLERK，薪水增加 15%；如果 JOB_ID 是 SA_REP，薪水增加 20%。对于所有其他的工作角色，不增加薪水。

```sql
SELECT last_name, job_id, salary, 
    CASE job_id WHEN 'IT_PROG' THEN 1.10*salary 
          WHEN 'ST_CLERK' THEN 1.15*salary 
          WHEN 'SA_REP' THEN 1.20*salary 
    ELSE salary END "REVISED_SALARY"
FROM employees;
```



#### g）实操

显示受雇日期在 1998 年 2 月 20 日 和 2005 年 5 月 1 日 之间的雇员的名字、岗位和受雇日期。按受雇日期顺序排序查询结果。

```sql
SELECT
LAST_NAME,JOB_ID,HIRE_DATE
FROM employees
WHERE HIRE_DATE BETWEEN '1998-2-20' AND '2005-5-1'
order by HIRE_DATE;
```



显示每一个在 2002 年受雇的雇员的名字和受雇日期。

```sql
select 
LAST_NAME,HIRE_DATE
FROM employees
where HIRE_DATE like '2002%'
```

对每一个雇员，显示 employee number、last_name、salary 和 salary 增加 15%，并且表示成整数，列标签显示为 New Salary。

```sql
SELECT
EMPLOYEE_ID,LAST_NAME,SALARY,
ROUND(SALARY *1.15,0)
FROM employees
```

写一个查询，显示名字的长度，对所有名字开始字母是 J、A 或 M 的雇员。用雇员的 lastname排序结果。

```sql
SELECT
LAST_NAME,
LENGTH(LAST_NAME)
FROM employees
WHERE LAST_NAME LIKE 'J%'
OR
LAST_NAME LIKE 'A%'
OR
LAST_NAME LIKE 'M%'
ORDER BY LAST_NAME;
```

创建一个查询显示所有雇员的 last name 和 salary。将薪水格式化为 15 个字符长度，用 $左填充 。

```sql
SELECT
LAST_NAME,LPAD(SALARY,15,'$')
FROM employees;
```



创建一个查询显示雇员的 last names 和 commission (佣金) 比率。如果雇员没有佣金，显示 “No Commission”，列标签 COMM。

```sql
SELECT
LAST_NAME,IFNULL(COMMISSION_PCT,'No Commission') COMM
FROM employees
```

写一个查询，按照下面的数据显示所有雇员的基于 JOB_ID 列值的级别。

| **工作**   | **级别** |
| ---------- | -------- |
| AD_PRES    | A        |
| ST_MAN     | B        |
| IT_PROG    | C        |
| SA_REP     | D        |
| ST_CLERK   | E        |
| 不在上面的 | 0        |



```sql
SELECT JOB_ID, 
  CASE JOB_ID WHEN 'AD_PRES' THEN 'A'
        WHEN 'ST_MAN' THEN 'B'
          WHEN 'IT_PROG' THEN 'C'
          WHEN 'SA_REP' THEN 'D'
        WHEN 'ST_CLERK' THEN 'E'
  ELSE 0 END 
FROM employees;
```





### 9，多表查询

#### a）多表查询介绍

![1718065727561](./assets/1718065727561.png)



**笛卡尔乘积：**

![1718065777530](./assets/1718065777530.png)

笛卡尔乘积 ：

当一个连接条件无效或被遗漏时，其结果是一个笛卡尔乘积 (*Cartesian product*)，其中所有行的组合都被显示。第一个表中的所有行连接到第二个表中的所有行。一个笛卡尔乘积会产生大量的行，其结果没有什么用。你应该在 WHERE 子句中始终包含一个有效的连接条件，除非你有特殊的需求，需要从所有表中组合所有的行。

![1718065866775](./assets/1718065866775.png)





#### b）等值连接

![1718066049985](./assets/1718066049985.png)

**等值连接**

为了确定一个雇员的部门名，需要比较 EMPLOYEES 表中的 DEPARTMENT_ID 列与DEPARTMENTS 表中的 DEPARTMENT_ID 列的值。在 EMPLOYEES 和DEPARTMENTS 表之间的关系是一个相等 (*equijoin*) 关系，即，两 个 表 中DEPARTMENT_ID 列的值必须相等。



**等值连接特点：**等值连接也被称为简单连接 (*simple joins*) 或内连接 (*inner joins*)。

1. 多表等值连接的结果为多表的交集部分；
2. n表连接，至少需要n-1个连接条件；
3. 多表不分主次，没有顺序要求；
4. 一般为表起别名，提高阅读性和性能；
5. 可以搭配排序、分组、筛选….等子句使用；



**等值连接的使用：**

![1718066268831](./assets/1718066268831.png)

SELECT 子句指定要返回的列名：

- employee last name、employee number 和 department number，这些是EMPLOYEES 表中的列
- department number、department name 和 location ID，这些是 DEPARTMENTS 表中的列



FROM 子句指定数据库必须访问的两个表：

- EMPLOYEES 表
- DEPARTMENTS 表



WHERE 子句指定表怎样被连接：

- EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID，因为 DEPARTMENT_ID 列是两个表的同名列，它必须用表名做前缀以避免混淆。



增加搜索条件：

![1718066863027](./assets/1718066863027.png)



**添加查询条件:**

除连接之外，可能还要求用 WHERE 子句在连接中限制一个或多个表中的行。



**限制不能缺的列：**

![1718066908732](./assets/1718066908732.png)

**限制不明确的列名：**

- 需要在 WHERE 子句中用表的名字限制列的名字以避免含糊不清。没有表前缀，DEPARTMENT_ID 列可能来自 DEPARTMENTS 表，也可能来自 EMPLOYEES 表，这种情况下需要添加表前缀来执行查询。
- 如果列名在两个表之间不相同，就不需要限定列。但是，使用表前缀可以改善性能，因为MySQL服务器可以根据表前缀找到对应的列。
- 必须限定不明确的列名也适用于在其它子句中可能引起混淆的那些列，例如 SELECT子句或 ORDER BY 子句。



**使用表别名：**

![1718066993053](./assets/1718066993053.png)

**表别名定义原则：**

- 表别名不易过长，短一些更好。
- 表别名应该是有意义的。
- 表别名只对当前的 SELECT 语句有效。



**多表连接：**

![1718067146446](./assets/1718067146446.png)



查询雇员 King 所在的部门名称。

```sql
select d.department_name from employees e,departments d  where e.dept_id = d.department_id and e.last_name = 'King';
```



显示每个雇员的 last name、departmentname 和 city。

```sql
SELECT e.last_name, d.department_name, l.city 
FROM employees e, departments d, locations l 
WHERE e.department_id = d.department_id 
AND d.location_id = l.location_id;
```





















































