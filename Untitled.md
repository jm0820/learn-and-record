### 一、什么是MYSQL

MYSQL 属于数据库的一种,mysql 目前属于Oracle甲骨文公司,mysql 称之为关系型数据库、同样为关系型数据库的还有微软的SQL Server。

MYSQL 数据库是一种 C/S 模型（客户端/服务端模型）,
客户端通过账户，密码连接服务器，连接成功后才可以进行数据库的操作（CRUD）
mysql服务端采用的IO复用+可伸缩的线程池，实现了网络高并发的经典模型

### 二、MYSQL 语言类别划分

 - DDL(Data Definition Language):数据库定义语言
定义了不同的数据库,数据库表,列,索引等数据库对象的定义
常用的SQL:create(创建),drop(删除),alter(更新)

- DML(data Manipulation Language)：数据库操控语言
用于添加、删除、变更和查询数据库记录
常用SQL:insert(添加),delete(删除),update(更新),select(查找)
- DCL(data Control Language):数据控制语言
控制不同数据库段直接访问和访问级别的语句
常用SQL：grant、remove

### 三、常用SQL语句
1.连接前服务端必须要启动
mysql -u xxx -p xxx
 - mysql：代表客户端命令
 - u 后面跟用户名
 - p 密码

2.退出用户端登录： exit;
3.库操作SQL格式：
- 创建数据库
create database database_name;
- 查看数据库
show databases;
- 使用数据库
use database_name;
- 查看数据库下存在的表
show tables;
- 删除数据库
drop database database_name;

4.表操作SQL格式
- 创建表
   create table table_name(
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;属性名1 数据类型 [完整性约束]，
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;属性名2 数据类型 [完整性约束]，
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;属性名3 数据类型 [完整性约束]
   );
   属性名例如 id,name,sex；
   数据类型例如 int,long,char,vaarchar
   完整性约束例如 主键，外键，非空等
   完整性约束如下：

| 完整性约束                                                   | 说明                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| primary                                                      | 表示当前属性为该表的关键，可以区分不同的行记录 |
| foreign key                                                  | 修饰当前属性为该表的外键，表之间关联使用外键   |
| not null                                                     | 表示属性不能为空                               |
| unique                                                       | 表示属性的值是唯一的                           |
| auto_increatment                                             | MySQL特色，表明属性是自增的，自增类型为整型    |
| default                                                      | 给属性设定默认值                               |
| 用户表：                                                     |                                                |
| 用户ID、用户名、年龄                                         |                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;create table user(             |                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  id int primary key unique, |                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    name varchar(10) , |                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    age int default 18 |                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;);                             |                                                |

- 查找表
desc table_name;
show create table table_name;
- 修改表
在使用过程中不满足要求可能要修改表，用的SQL关键字是alter

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.修改表名
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table old_name_name rename [to] new_table_name;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.修改表的属性数据类型
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name modify 属性名 属性类型

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.修改表的字段（属性）
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name change 旧的属性名 新属性名 新属性类型；

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.修改表的字段
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name add 属性名 属性类型 [完整性约束]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;新增一个address(插在第一个)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name add 属性名 属性类型[完整性约束] first

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.增加一个字段在某个属性后
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name add 属性名 属性类型[完整性约束] after 列名

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.删除字段
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name drop 属性名

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.修改字段的排列顺序
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name modify 属性1 属性类型 first|after 属性2

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.修改存储引擎
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter table table_name engine = innoDB|MYSIAM