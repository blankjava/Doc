## 创建表
```
create table [if not exists] t_name(字段名 字段类型,…,字段名 字段类型)[table options …];
```
表选项则是用来控制表的表现形式的，共有三种，分别为：
- 字符集设定：charset/ character set+具体字符集，用来表示数据存储的编码格式，常用的字符集包括GBK和UTF8等。
- 校对集设定：collate+具体校对集，表示数据比较的规则，其依赖字符集。
- 存储引擎：engine+具体存储引擎，默认为InnoDB，常用的还有MyISAM.

例如：
```
create table if not exists t_student(name varchar(32),birthday date,sex int)charset utf8;
```
上面的方法创建的表，默认就是属于当前数据库，下面显式的指定表所属的数据库：
```
create table if not exists hello.t_student(name varchar(32),birthday date,sex int);
```
在hello数据库中创建t_student表；

### 1、使用null值
使用null值是为了约束列值能否为null。每个表的列要么是NULL列，要么是NOT NULL列：
```
create table if not exists t_student(name varchar(32) not null,birthday date,sex int);
```
在不指定NOT NULL 时，多数DBMS 认为指定的是NULL；不要把NULL 值与空字符串相混淆。NULL 值是没有值，不是空字符串。

### 2、使用默认值
在插入行时如果不给出值，DBMS 将自动采用默认值。默认值在CREATE TABLE 语句的列定义中用关键字DEFAULT 指定。
```
create table if not exists t_student(name varchar(32) not null,birthday date default current_date(),sex int default 0);
```
### 3、创建表同时给表增加注释
```
create table if not exists t_student(name varchar(32) not null,birthday date default current_date(),sex int default 0) comment '学生表';
```
### 4、创建表时同时给字段增加注释
```
create table if not exists t_student(name varchar(32) not null comment '学生姓名',birthday date default current_date() comment '生日',sex int default 0 comment '性别');
```
## 查看表
### 1、查看当前数据库所有表
```
show tables;
```
### 2、模糊查询表
```
show tables like '%s%';
```
### 3、查询创建表的SQL语句
```
show create table 表名;
```
### 4、查看表中的字段信息
```
show columns from 表名;
```
要查看表中字段和注释信息：
```
show full columns from 表名;
```
### 5、查看表的状态
```
show table status;
```

## 修改表
### 1、修改表名，基本语法
```
rename table 旧表名 to 新表名;
```
例如：
```
rename table t_student to t_teacher;
```
### 2、修改表选项，基本语法
```
alter table 表名 表选项[=]值;
```
### 3、新增字段，基本语法
```
alter table 表名 add [column] 字段名 数据类型 [列属性] [位置];
```
其中，位置表示此字段存储的位置，分为first（第一个位置）和after +字段名（指定的字段后，默认为最后一个位置）。例如：
```
alter table t_teacher add work varchar(64) after name;
```
### 4、修改字段类型或位置，基本语法
```
alter table 表名 modify 字段名 数据类型 [列属性] [位置];
```
例如：
```
alter table t_teacher modify name char(10) after work;
```
modify不能修改字段名；
### 5、重命名字段，包括类型的位置，基本语法
```
alter table 表名 change 旧字段名 新字段名 数据类型 [列属性] [位置];
```
例如：
```
alter table t_teacher change work job varchar(32) after name;
```
### 6、删除字段，基本语法
```
alter table 表名 drop 字段名;
```
例如：
```
alter table t_teacher drop job;
```
### 7、修改表的注释
```
alter table student comment '新学生表';
```
### 8、修改字段注释
```
alter table student modify column age int comment '年龄';
```
## 删除表
```
drop table 表1, 表2 ... ;
```
可以一次删除多张表；需要注意：此删除为不可逆操作；

