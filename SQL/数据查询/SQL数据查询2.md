## 分组数据
分组数据要用到GROUP BY 子句和HAVING 子句。

### 创建分组
在使用GROUP BY 子句前，需要知道一些重要的规定。  
- GROUP BY 子句可以包含任意数目的列，因而可以对分组进行嵌套，更细致地进行数据分组。
- 如果在GROUP BY 子句中嵌套了分组，数据将在最后指定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- GROUP BY 子句中列出的每一列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在SELECT 中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
- 大多数SQL 实现不允许GROUP BY 列带有长度可变的数据类型（如文本或备注型字段）。
- 除聚集计算语句外，SELECT 语句中的每一列都必须在GROUP BY 子句中给出，否则只会显示分组中的第一条记录中的数值。
- 如果分组列中包含具有NULL 值的行，则NULL 将作为一个分组返回。如果列中有多行NULL 值，它们将分为一组。
- GROUP BY 子句必须出现在WHERE 子句之后，ORDER BY 子句之前。

将所有产品按供应商分组：
```
select vend_id,count(*),prod_num from Products group by vend_id;
```

### 过滤分组
除了能用GROUP BY 分组数据外，SQL 还允许过滤分组，规定包括哪些分组，排除哪些分组。例如，你可能想要列出至少有两个订单的所有顾客；
```
select cust_id from Orders group by cust_id having count(*) >=2;
```
HAVING 非常类似于WHERE。事实上，目前为止所学过的所有类型的WHERE 子句都可以用HAVING 来替代。唯一的差别是，WHERE过滤行，而HAVING 过滤分组。
这里有另一种理解方法，WHERE 在数据分组前进行过滤，HAVING 在数据分组后进行过滤。这是一个重要的区别，WHERE 排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING 子句中基于这些值过滤掉的分组。  
Where和Having一起使用的场景，例如，获取产品表中价格大于4的产品中，产品数量至少有两个的供应商；
```
select vend_id from Products where prod_price>4 group by vend_id having count(*)>=2;
```

### 分组组外排序
分组后的数据，同样可以使用`ORDER BY`子句进行排序，对于MySQL,排序的是每个分组的第一条记录，对于SQLite，排序的是每个分组最后一条记录，这种方式的排序称为组外排序；


### 分组组内排序
如果要对分组中的数据进行排序，即组内排序，则可以使用子查询的办法实现，例如，获取产品表中所有产品按供应商分组，并且每个分组中按产品价格降序列表：
```

```

## 子查询
子查询是把内层的查询结果供外层使用；

### 条件子查询
我们知道，订单存储在两个表中。每个订单包含订单编号、客户ID、订单日期，在Orders 表中存储为一行。各订单的物品存储在相关的OrderItems 表中。Orders 表不存储顾客信息，只存储顾客ID。顾客的实际信息存储在Customers 表中。  
现在，假如需要列出订购物品ID为FB的所有顾客，应该怎样检索？下面列出具体的步骤。  
1)	检索包含物品FB的所有订单的编号。
2)	检索具有前一步骤列出的订单编号的所有顾客的ID。
3)	检索前一步骤返回的所有顾客ID 的顾客信息。

上述每个步骤都可以单独作为一个查询来执行。也可以把一条SELECT语句返回的结果用于另一条SELECT 语句的WHERE子句，组合成一条语句。
```
select cust_name,cust_address from Customers where cust_id in (select cust_id from Orders where order_num in (select order_num from OrderItems where prod_id = 'FB'));
```
where子查询总是从内向外处理的。作为子查询的SELECT语句只能查询单个列。企图检索多个列将返回错误。

### 计算字段子查询
使用子查询的另外一种方式是计算字段子查询；  
例如，查询Customers 表中每个顾客的订单总数；执行这个操作，可以遵循下面的步骤：  
1)	从Customers 表中检索顾客列表；
2)	对于检索出的每个顾客，统计其在Orders表中的订单数目

我们可以认为顾客的订单数目作为顾客的一个计算字段：
```
select cust_id,cust_name,(select count(*) from Orders where Orders.cust_id = Customers.cust_id) ordres from Customers;
```
这条SELECT 语句对Customers 表中每个顾客返回三列：cust_name、cust_id 和orders。orders 是一个计算字段，它是由圆括号中的子查询建立的。该子查询对检索出的每个顾客执行一次。  
计算字段子查询是由外向内交替查询的；

其实上面的需求可以使用更简单的方法完成，那就是多表查询，后面再讲；

## 联结查询
联结是一种机制，用来在一条SELECT 语句中关联多张表；

### 内联结
在联结两个表时，实际要做的是将第一个表中的每一行与第二个表中的每一行配对。WHERE子句作为过滤条件，只包含那些匹配给定条件（这里是联结条件）的行。例如查询所有供应商生产的产品：
```
select vend_name,prod_name from Vendors,Products where Vendors.vend_id = Products.vend_id;
```
如果没有WHERE子句，第一个表中的每一行将与第二个表中的每一行配对，而不管它们逻辑上是否能配在一起，这种配对的结果被称为笛卡儿积。  
笛卡儿积：由没有联结条件的表关系返回的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。  
有时，我们把返回笛卡儿积的联结，称叉联结（cross join）。

其实，内联结还有一种语法，可以明确知道联结的类型：
```
select vend_name,prod_name from Vendors inner join Products on Vendors.vend_id = Products.vend_id;
```
两个表是以INNER JOIN联结的，联结条件用特定的ON子句而不是WHERE子句；

联结多个表，SQL理论上不限制一条SELECT语句中可以联结的表的数目（其实各DBMS是有限制的），创建联结的基本规则也相同。但是，联结的表越多，性能下降越厉害；  
现在回过头来做条件子查询中的例子：
```
select cust_name,cust_address from Customers,Orders,OrderItems where Customers.cust_id = Orders.cust_id and Orders.order_num = OrderItems.order_num and prod_id = 'FB';
```

### 表别名
SQL 除了可以对列名和计算字段使用别名，还允许给表名起别名；例如上面的查询可以写成：
```
select cust_name,cust_address from Customers c,Orders o,OrderItems oi where c.cust_id = o.cust_id and o.order_num = oi.order_num and prod_id = 'FB';
```
这样做有两个好处：  
- 缩短SQL 语句；
- 允许在一条SELECT 语句中多次使用相同的表。

### 自联结
自联结其实就是联结的表是同一张表，只是表别名不一样而已；

### 自然联结

### 外联结
许多联结将一个表中的行与另一个表中的行相关联，只有关联的行会被保留，但有时候需要包含那些没有关联的行。例如：  
- 对每个顾客下的订单进行计数，包括那些至今尚未下订单的顾客；
- 列出所有产品以及订购数量，包括没有人订购的产品；
- 计算平均销售规模，包括那些至今尚未下订单的顾客。
联结包含了那些在相关表中没有关联的行。这种联结称为外联结。  
例如，第一个需求的实现如下：
```
select cust_name,count(order_num) from Customers left outer join Orders on Customers.cust_id = Orders.cust_id group by cust_name;
```
这个语句与计算字段子查询的查询结果是一样的；  
在使用OUTER JOIN 语法时，必须使用RIGHT 或LEFT 关键字指定包括其所有行的表（RIGHT 指出的是OUTER JOIN 右边的表，而LEFT 指出的是OUTER JOIN左边的表）。

## 组合查询
多数SQL 查询只包含从一个或多个表中返回数据的单条SELECT 语句。但是，SQL 也允许执行多个查询（多条SELECT 语句），并将结果作为一个查询结果集返回。这些组合查询通常称为并（union）或复合查询（compound query）。  
主要有两种情况需要使用组合查询：  
- 在一个查询中从不同的表返回结构数据；
- 对一个表执行多个查询，按一个查询返回数据。

### UNION组合规则
- UNION 必须由两条或两条以上的SELECT 语句组成，语句之间用关键字UNION分隔（因此，如果组合四条SELECT 语句，将要使用三个UNION关键字）。
- UNION 中的每个查询必须包含相同的列、表达式或聚集函数（不过，各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS 可以隐含转换的类型（例如，不同的数值类型或不同的日期类型）。

假如需要Illinois（IL）、Indiana（IN）和Michigan（MI）等美国几个州的所有顾客，还想包括不管位于哪个州的所有的E Fudd。
```
select cust_name,cust_address from Customers where cust_state in ('IL','IN','MI') union select cust_name,cust_address from Customers where cust_name = 'E Fudd';
```
这条语句由两条SELECT语句组成，之间用UNION关键字分隔，并把输出组合成一个查询结果集；（同表的多次查询其实可以用where子句来完成）；

原则上，UNION会从查询结果集中自动去除了重复的行，如果想返回所有的匹配行，可使用UNION ALL；

### 查询结果排序
在用UNION 组合查询时，只能使用一条ORDER BY 子句，它必须位于最后一条SELECT 语句之后。对于结果集，不存在用一种方式排序一部分，而又用另一种方式排序另一部分的情况，因此不允许使用多条ORDER BY子句。


