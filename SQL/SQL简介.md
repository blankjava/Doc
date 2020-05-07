SQL的全称为Structured Query Language，结构化查询语言（数据以查询为主，99% 都是在进行查询操作）。
标准SQL 由ANSI 标准委员会管理，从而称为ANSI SQL。所有主要的DBMS，即使有自己的扩展，也都支持ANSI SQL。

SQL 主要分为三种：
- DDL：Data Definition Language，数据定义语言，用来维护存储数据的结构（数据库、表），代表指令为create、drop和alter等。
- DML：Data Manipulation Language，数据操作语言，用来对数据进行操作（表中的内容）代表指令为insert、delete和update等，不过在 DML 内部又单独进行了一个分类，即 DQL（Data Query Language），数据查询语言，代表指令为select.
- DCL：Data Control Language，数据控制语言，主要是负责（用户）权限管理，代表指令为grant和revoke等。

注：由于各厂家数据库在SQL的实现上略有差异，所以下面的的SQL测试语句均以MySQL数据库为环境；