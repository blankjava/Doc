# 简介
库操作是数据库定义语言（DDL）；

<https://github.com/guobinhit/mysql-tutorial/blob/master/articles/sql-operation.md>

# 创建数据库
```
create database [if not exists] db_name [create_specification …];
```
- db_name表示数据库名称，数据库的名字不能用关键字或者保留字，如果非要使用数据库的关键字或者保留字作为数据库名称，那么必须用反引号“`”将其括起来；
- create_specification表示创建条件：字符集和校对规则，其中字符集的名称中不能出现-字符，比如utf-8要写成utf8，校对规则是指字符的排序规则，可以在MySQL文档查看当前字符集对应有哪些校对规则；

例如：创建字符集为UTF-8且校对规则为utf8_general_ci的数据库hello
```
create database hello character set utf8 collate utf8_general_ci;
或者
create database hello charset utf8 collate utf8_general_ci;
```

# 查询数据库
1. 查看所有的数据库：
```
show databases;
```
2. 模糊查询数据库：
```
show databases like '%m%';
```
其中，匹配模式有两种，分别为：
- %：表示匹配多个字符；
- _：表示匹配单个字符。
此外，在匹配含有下划线_的数据库名称的时候，需要在下划线前面加上反斜线\_进行转义操作。

3. 查看指定数据库的创建SQL语句：
```
show create database db_name;
```
在这里，查看的结果有可能与咱们书写的 SQL 语句不同，这是因为数据库在执行 SQL 语句之前会优化 SQL，系统保存的是优化后的结果。

4. 如果现在已经进入了数据库，要查看当前使用的数据库，可以使用：
```
select database();
```

# 修改数据库
```
alter database [if not exists] db_name [alter_specification …];
```
例如，将数据库hello的字符集修改为gbk；
```
alter database hello character set gbk;
```
需要注意：数据库的名字不可以修改。数据库的修改仅限库选项，即字符集和校对集（校对集依赖字符集）。

# 删除数据库
```
drop database [if exists] db_name;
```
需要注意：在删除数据库之前，应该先进行备份操作，因为删除为不可逆操作，所以不要随意删除数据库。

# 使用数据库
```
use db_name;
```
切换数据库也可以使用use；
