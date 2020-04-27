## 新增数据
### 1、插入完整行
给全表字段插入数据，不需要指定字段列表，但要求数据的值出现的顺序必须与表中的字段出现的顺序一致。
```
insert into 表名 values(值列表),…,(值列表);
```
例如：
```
insert into t_teacher values('zhangsan','1990-12-01',0);
```
虽然这种语法很简单，但并不安全，应该尽量避免使用，因为这样的插入语句高度依赖于表中列的定义次序；一般推荐使用带列名的插入方式：
```
insert into t_teacher(name,birthday,sex) values('zhangsan','1990-12-01',0);
```
因为提供了列名，VALUES 必须以其指定的次序匹配指定的列名，不一定按各列出现在表中的实际次序。其优点是，即使表的结构改变，这条INSERT 语句仍然能正确工作。

### 2、插入部分行
给部分字段插入数据，需要选定字段列表，字段列表中字段出现的顺序与表中字段的顺序无关，但值列表中字段值的顺序必须与字段列表中的顺序保持一致。
```
insert into 表名(字段列表) values(值列表),…,(值列表);
```
例如：
```
insert into t_teacher(name,sex) values('lisi',1),('wangwu',0);
```

### 3、插入检索结果
INSERT 还存在另一种形式，可以利用它将SELECT 语句的结果插入表中，这就是所谓的INSERT SELECT。顾名思义，它是由一条INSERT 语句和一条SELECT语句组成的。    
假如想把另一表中的顾客列合并到Customers 表中。不需要每次读取一行再将它用INSERT 插入，可以如下进行：
```
insert into Customers(cust_name,cust_address) select cust_name,cust_address from NewCustomers;
```
为简单起见，这个例子在INSERT 和SELECT 语句中使用了相同的列名。但是，不一定要求列名匹配。事实上，DBMS 一点儿也不关心SELECT返回的列名。它使用的是列的位置，因此SELECT 中的第一列（不管其列名）将用来填充表列中指定的第一列，第二列将用来填充表列中指定的第二列，如此等等。
用此方法，可以用来备份一张表的数据；

## 修改数据
1、基本修改操作
```
update 表名 set 字段 = 值,…字段 = 值[where 条件];
```
在这里，建议尽量加上where条件，否则的话，操作的就是全表数据。  
例如：
```
update t_teacher set sex=1 where name = 'wangwu';
```
判断更新操作是否成功，并不是看 SQL 语句是否执行成功，而是看是否有记录受到影响，即affected的数量大于1时，才是真正的更新成功。

2、将同表中一个字段赋值给另一个字段
```
UPDATE t_user SET signed_time = create_time;
```
这个操作需要两个字段的类型能兼容，而且一般都会加where子句，否则就是修改整个表的数据；


3、将同表中两个字段互换
```
UPDATE t_user u1, t_user u2
SET u1.signed_time = u2.create_time,
u2.create_time = u1.signed_time;
```

4、不同表的一列复制到另一列
```
update table1,table2 set table1.field1=table2.field2 where table1.id=table2.id;
```


## 删除数据
```
delete from 表名 [where 条件];
```
我们也可以用drop来实现删除操作，不过与delete相比，drop的威力更强，其在执行删除操作的时候，不仅会删除数据，还会删除定义并释放存储空间；而delete在执行删除操作的时候，仅会删除数据，并不会删除定义和释放存储空间。
```
delete from t_teacher where name = 'wangwu';
```

