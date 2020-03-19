## 检索数据
### 创建数据
在进行数据操作前，先创建一些测试数据，方便后面测试用例的理解；  
#### 1、创建数据库：
```
create database sql_study;
```
创建样例表：这里使用source引入外部sql文件；
```
source D:\BaiduNetdiskDownload\SQL\mysql_scripts\create.sql
```
注意这个不是SQL语句，后面不能加“;”，其中有以下5张表：
- 供应商表：Vendors 表存储销售产品的供应商；
- 产品表：Products表包含产品目录，每行一个产品。
- 顾客表：Customers表存储所有顾客信息；
- 订单表：Orders表存储顾客订单（不是订单细节）。
- 订单产品表：OrderItems 表存储每个订单中的实际物品，每个订单的每个物品一行。对于Orders 表的每一行，在OrderItems表中有一行或多行；
#### 2、填充数据：
```
source D:\BaiduNetdiskDownload\SQL\mysql_scripts\populate.sql
```
其中create.sql文件内容如下：
```
########################################
# MySQL Crash Course
# http://www.forta.com/books/0672327120/
# Example table creation scripts
########################################


########################
# Create customers table
########################
CREATE TABLE customers
(
  cust_id      int       NOT NULL AUTO_INCREMENT,
  cust_name    char(50)  NOT NULL ,
  cust_address char(50)  NULL ,
  cust_city    char(50)  NULL ,
  cust_state   char(5)   NULL ,
  cust_zip     char(10)  NULL ,
  cust_country char(50)  NULL ,
  cust_contact char(50)  NULL ,
  cust_email   char(255) NULL ,
  PRIMARY KEY (cust_id)
) ENGINE=InnoDB;

#########################
# Create orderitems table
#########################
CREATE TABLE orderitems
(
  order_num  int          NOT NULL ,
  order_item int          NOT NULL ,
  prod_id    char(10)     NOT NULL ,
  quantity   int          NOT NULL ,
  item_price decimal(8,2) NOT NULL ,
  PRIMARY KEY (order_num, order_item)
) ENGINE=InnoDB;


#####################
# Create orders table
#####################
CREATE TABLE orders
(
  order_num  int      NOT NULL AUTO_INCREMENT,
  order_date datetime NOT NULL ,
  cust_id    int      NOT NULL ,
  PRIMARY KEY (order_num)
) ENGINE=InnoDB;

#######################
# Create products table
#######################
CREATE TABLE products
(
  prod_id    char(10)      NOT NULL,
  vend_id    int           NOT NULL ,
  prod_name  char(255)     NOT NULL ,
  prod_price decimal(8,2)  NOT NULL ,
  prod_desc  text          NULL ,
  PRIMARY KEY(prod_id)
) ENGINE=InnoDB;

######################
# Create vendors table
######################
CREATE TABLE vendors
(
  vend_id      int      NOT NULL AUTO_INCREMENT,
  vend_name    char(50) NOT NULL ,
  vend_address char(50) NULL ,
  vend_city    char(50) NULL ,
  vend_state   char(5)  NULL ,
  vend_zip     char(10) NULL ,
  vend_country char(50) NULL ,
  PRIMARY KEY (vend_id)
) ENGINE=InnoDB;

###########################
# Create productnotes table
###########################
CREATE TABLE productnotes
(
  note_id    int           NOT NULL AUTO_INCREMENT,
  prod_id    char(10)      NOT NULL,
  note_date datetime       NOT NULL,
  note_text  text          NULL ,
  PRIMARY KEY(note_id),
  FULLTEXT(note_text)
) ENGINE=MyISAM;


#####################
# Define foreign keys
#####################
ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num) REFERENCES orders (order_num);
ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_products FOREIGN KEY (prod_id) REFERENCES products (prod_id);
ALTER TABLE orders ADD CONSTRAINT fk_orders_customers FOREIGN KEY (cust_id) REFERENCES customers (cust_id);
ALTER TABLE products ADD CONSTRAINT fk_products_vendors FOREIGN KEY (vend_id) REFERENCES vendors (vend_id);

```
populate.sql文件内容如下：
```
########################################
# MySQL Crash Course
# http://www.forta.com/books/0672327120/
# Example table population scripts
########################################


##########################
# Populate customers table
##########################
INSERT INTO customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES(10001, 'Coyote Inc.', '200 Maple Lane', 'Detroit', 'MI', '44444', 'USA', 'Y Lee', 'ylee@coyote.com');
INSERT INTO customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact)
VALUES(10002, 'Mouse House', '333 Fromage Lane', 'Columbus', 'OH', '43333', 'USA', 'Jerry Mouse');
INSERT INTO customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES(10003, 'Wascals', '1 Sunny Place', 'Muncie', 'IN', '42222', 'USA', 'Jim Jones', 'rabbit@wascally.com');
INSERT INTO customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES(10004, 'Yosemite Place', '829 Riverside Drive', 'Phoenix', 'AZ', '88888', 'USA', 'Y Sam', 'sam@yosemite.com');
INSERT INTO customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact)
VALUES(10005, 'E Fudd', '4545 53rd Street', 'Chicago', 'IL', '54545', 'USA', 'E Fudd');


########################
# Populate vendors table
########################
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1001,'Anvils R Us','123 Main Street','Southfield','MI','48075', 'USA');
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1002,'LT Supplies','500 Park Street','Anytown','OH','44333', 'USA');
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1003,'ACME','555 High Street','Los Angeles','CA','90046', 'USA');
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1004,'Furball Inc.','1000 5th Avenue','New York','NY','11111', 'USA');
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1005,'Jet Set','42 Galaxy Road','London', NULL,'N16 6PS', 'England');
INSERT INTO vendors(vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country)
VALUES(1006,'Jouets Et Ours','1 Rue Amusement','Paris', NULL,'45678', 'France');


#########################
# Populate products table
#########################
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('ANV01', 1001, '.5 ton anvil', 5.99, '.5 ton anvil, black, complete with handy hook');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('ANV02', 1001, '1 ton anvil', 9.99, '1 ton anvil, black, complete with handy hook and carrying case');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('ANV03', 1001, '2 ton anvil', 14.99, '2 ton anvil, black, complete with handy hook and carrying case');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('OL1', 1002, 'Oil can', 8.99, 'Oil can, red');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('FU1', 1002, 'Fuses', 3.42, '1 dozen, extra long');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('SLING', 1003, 'Sling', 4.49, 'Sling, one size fits all');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('TNT1', 1003, 'TNT (1 stick)', 2.50, 'TNT, red, single stick');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('TNT2', 1003, 'TNT (5 sticks)', 10, 'TNT, red, pack of 10 sticks');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('FB', 1003, 'Bird seed', 10, 'Large bag (suitable for road runners)');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('FC', 1003, 'Carrots', 2.50, 'Carrots (rabbit hunting season only)');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('SAFE', 1003, 'Safe', 50, 'Safe with combination lock');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('DTNTR', 1003, 'Detonator', 13, 'Detonator (plunger powered), fuses not included');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('JP1000', 1005, 'JetPack 1000', 35, 'JetPack 1000, intended for single use');
INSERT INTO products(prod_id, vend_id, prod_name, prod_price, prod_desc)
VALUES('JP2000', 1005, 'JetPack 2000', 55, 'JetPack 2000, multi-use');



#######################
# Populate orders table
#######################
INSERT INTO orders(order_num, order_date, cust_id)
VALUES(20005, '2005-09-01', 10001);
INSERT INTO orders(order_num, order_date, cust_id)
VALUES(20006, '2005-09-12', 10003);
INSERT INTO orders(order_num, order_date, cust_id)
VALUES(20007, '2005-09-30', 10004);
INSERT INTO orders(order_num, order_date, cust_id)
VALUES(20008, '2005-10-03', 10005);
INSERT INTO orders(order_num, order_date, cust_id)
VALUES(20009, '2005-10-08', 10001);


###########################
# Populate orderitems table
###########################
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 1, 'ANV01', 10, 5.99);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 2, 'ANV02', 3, 9.99);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 3, 'TNT2', 5, 10);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20005, 4, 'FB', 1, 10);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20006, 1, 'JP2000', 1, 55);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20007, 1, 'TNT2', 100, 10);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20008, 1, 'FC', 50, 2.50);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 1, 'FB', 1, 10);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 2, 'OL1', 1, 8.99);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 3, 'SLING', 1, 4.49);
INSERT INTO orderitems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20009, 4, 'ANV03', 1, 14.99);

#############################
# Populate productnotes table
#############################
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(101, 'TNT2', '2005-08-17',
'Customer complaint:
Sticks not individually wrapped, too easy to mistakenly detonate all at once.
Recommend individual wrapping.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(102, 'OL1', '2005-08-18',
'Can shipped full, refills not available.
Need to order new can if refill needed.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(103, 'SAFE', '2005-08-18',
'Safe is combination locked, combination not provided with safe.
This is rarely a problem as safes are typically blown up or dropped by customers.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(104, 'FC', '2005-08-19',
'Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(105, 'TNT2', '2005-08-20',
'Included fuses are short and have been known to detonate too quickly for some customers.
Longer fuses are available (item FU1) and should be recommended.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(106, 'TNT2', '2005-08-22',
'Matches not included, recommend purchase of matches or detonator (item DTNTR).'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(107, 'SAFE', '2005-08-23',
'Please note that no returns will be accepted if safe opened using explosives.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(108, 'ANV01', '2005-08-25',
'Multiple customer returns, anvils failing to drop fast enough or falling backwards on purchaser. Recommend that customer considers using heavier anvils.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(109, 'ANV03', '2005-09-01',
'Item is extremely heavy. Designed for dropping, not recommended for use with slings, ropes, pulleys, or tightropes.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(110, 'FC', '2005-09-01',
'Customer complaint: rabbit has been able to detect trap, food apparently less effective now.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(111, 'SLING', '2005-09-02',
'Shipped unassembled, requires common tools (including oversized hammer).'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(112, 'SAFE', '2005-09-02',
'Customer complaint:
Circular hole in safe floor can apparently be easily cut with handsaw.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(113, 'ANV01', '2005-09-05',
'Customer complaint:
Not heavy enough to generate flying stars around head of victim. If being purchased for dropping, recommend ANV02 or ANV03 instead.'
);
INSERT INTO productnotes(note_id, prod_id, note_date, note_text)
VALUES(114, 'SAFE', '2005-09-07',
'Call from individual trapped in safe plummeting to the ground, suggests an escape hatch be added.
Comment forwarded to vendor.'
);

```
### 基本检索
#### 1、检索单个列
每个SQL 语句都是由一个或多个关键字构成的。最经常使用的SQL语句大概就是SELECT 语句了。它的用途是从一个或多个表中检索信息。例如：
```
select prod_name from Products;
```
- 多条SQL 语句必须以分号（；）分隔。多数DBMS不需要在单条SQL语句后加分号，但也有DBMS可能必须在单条SQL语句后加上分号，所以建议总是加上。
- SQL语句不区分大小写，因此SELECT与select是相同的。同样，写成Select也没有关系。
- 在处理SQL语句时，其中所有换行都被忽略。SQL语句可以写成长长的一行，也可以分写在多行。

这里简单介绍一下子句（clause）的概念：  
SQL语句由子句构成，有些子句是必需的，有些则是可选的。一个子句通常由一个关键字加上所提供的数据组成。例如上面的的SELECT 语句的FROM子句。
#### 2、检索多个列
检索多个列，仍然使用相同的SELECT语句。唯一的不同是必须在SELECT关键字后给出多个列名，列名之间必须以逗号分隔。
```
select prod_id,prod_name,prod_price from Products;
```
#### 3、检索所有列
使用星号（*）通配符；
```
select * from Products;
```
一般而言，除非你确实需要表中的每一列，否则最好别使用*通配符。虽然使用通配符不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。  
使用通配符有一个大优点。由于不明确指定列名（因为星号检索每一列），所以能检索出名字未知的列。

### 去除重复
```
select distinct vend_id from Products;
```
distinct vend_id告诉DBMS只返回不同（具有唯一性）的vend_id行；  
注意，DISTINCT关键字作用于所有的列，不仅仅是跟在其后的那一列。例如，你指定SELECT DISTINCT vend_id, prod_price，除非指定的两列完全相同，否则所有的行都会被检索出来。

### 限制结果
```
select prod_name from Products limit 5;
```
LIMIT 5指示MySQL等DBMS返回不超过5行的数据。如果要得到后面的5行数据，需要指定从哪行开始以及检索的行数：
```
select prod_name from Products limit 5 offset 4;
```
LIMIT 5 OFFSET 5指示MySQL等DBMS返回从第5行起的5行数据。  
注意，第一个被检索的行是第0行，而不是第1行。因此，LIMIT 5 OFFSET 1会检索第2 行，而不是第1行。  
MySQL和MariaDB支持简化版的LIMIT 4 OFFSET 3语句，即LIMIT 3,4。使用这个语法，逗号之前的值对应OFFSET，逗号之后的值对应LIMIT。
```
select prod_name from Products limit 5,4;
```

## 排序数据
这一节主要讲解如何使用SELECT语句的ORDER BY子句，根据需要排序检索出的数据。  
其实，默认检索出的数据并不是随机显示的。如果不排序，数据一般将以它在底层表中出现的顺序显示，这有可能是数据最初添加到表中的顺序。但是，如果数据随后进行过更新或删除，那么这个顺序将会受到DBMS 重用回收存储空间的方式的影响。因此，如果不明确控制的话，则最终的结果不能（也不应该）依赖该排序顺序。关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有任何意义。

### 单列排序
```
select prod_name from Products order by prod_name;
```
为了排序select检索出来的数据，可使用ORDER BY子句。在指定一条ORDER BY 子句时，应该保证它是SELECT 语句中最后一条子句。

### 多列排序
有的时候需要按多个列进行排序。例如，首先按产品价格排序，如果价格相同，再按产品名称排序；
```
select prod_id,prod_price,prod_name from Products order by prod_price,prod_name;
```
多个列排序，列名之间用逗号分开即可；

### 列相对位置排序
除了能用列名进行排序外，ORDER BY还支持按列相对位置进行排序；
```
select prod_id,prod_price,prod_name from Products order by 2,3;
```
这里的2,3指的是列相对位置，即检索结果中列的位置，而不是表中列的位置；

### 排序方向
数据排序默认的方向是升序，如果要指定降序排列，可使用DESC关键字；
```
select prod_id,prod_price,prod_name from Products order by prod_price DESC;
```
多列排序情况下的方向：
```
select prod_id,prod_price,prod_name from Products order by prod_price DESC,prod_name;
```
DESC关键字只作用于直接相关列上，所以上例中是按prod_price降序排列，相同价格按prod_name升序排列；  
DESC是DESCENDING的缩写，这两个关键字都可以使用。与DESC相对的是ASC（或ASCENDING），在升序排序时可以指定它。

### 动态列排序
通常情况下，排序是以固定的一个或多个字段先后进行排序，如果排序字段不是固定的，而是按某个条件才能确定时，就要使用到动态列排序；  
例如，所有的产品，先由供应商的ID降序排列，当供应商ID = 1003时，以产品价格降序列表，当供应商ID为非1003时，以产品名称降序排列；
```
SELECT
	*
FROM
	products
ORDER BY
	vend_id,
	CASE
	WHEN vend_id = '1003' THEN
		prod_price
	ELSE
		prod_name
	END DESC;
```

  ### 条件排序
在`ORDER BY`子句中，将条件判断后的结果作为新的排序列进行排序；条件排序有两种方式，使用`IF`或`IN`;
```
SELECT
	*
FROM
	products
ORDER BY
	IF (vend_id = '1003', 0, 1);
```
上面的`IF`表达式是指当供应商ID = 1003时，返回0，否则返回1，然后按返回的结果进行升序排列，这样可以实现置顶或置尾效果；

如果有多个判断条件，可以使用`IN`表达式；
```
SELECT
	*
FROM
	products
ORDER BY
	vend_id IN ('1001','1003') DESC;
```
`IN`表达式中，符合条件的返回1，不符合条件的返回0；


## 过滤数据
这一节将讲解如何使用SELECT 语句的WHERE 子句指定检索条件。
### where字句
```
select prod_name,prod_price from Products where prod_price = 4.49;
```
WHERE 子句在表名（FROM子句）之后给出；
### 列操作符
where子句的列操作符有如下几种：
|操作符 | 说明|
|:- | :-|
|= | 等于|
|<>或!= | 不等于|
|< | 小于|
|<= | 小于等于|
|!< | 不小于/大于等于|
|> | 大于|
|>= | 大于等于|
|!> | 不大于/小于等于|
|between and | 在指定的两个值之间|
|is null | 为NULL值|
|in | 在指定的一组值内|
|not | 与其他列操作符配合使用，取反|
|like | 按指定规则匹配，一般与通配符一起使用|

检索产品价格小于10的产品：
```
select prod_name,prod_price from Products where prod_price < 10;
```
检索产品价格在5和10之间的产品：
```
select prod_name,prod_price from Products where prod_price between 5 and 10;
```
注意between后面要跟两个值，并且用and关键字连接；  
检索不是供应商1001生产的产品：
```
select prod_name,prod_price from Products where vend_id != '1001';
或者
select prod_name,prod_price from Products where not vend_id = '1001';
```

### 条件操作符
|操作符 | 说明|
|:- | :-|
|and | 且|
|or | 或|

条件操作符是用来连接where子句的多个判断条件的，例如：
```
select prod_name,prod_price from Products where vend_id = '1001' and prod_price < 10;
```
值得注意的是，and操作符的优先级比or操作符高，所以在同时有and和or操作符语句中，要使用小括号进行分组，否则，SQL语句会优先执行and操作符；
```
select prod_name,prod_price from Products where (vend_id = '1001' or vend_id = '1002') and prod_price < 10;
```

### 通配符
除了上面针对精确值的检索，我们还可以检索模糊值，例如，所有产品名称以Jet开头的产品：
```
select prod_name,prod_price from Products where prod_name like 'Jet%';
```
其中%是通配符，表示任何字符出现任意次数，包括0次；通配符可在搜索模式中的任意位置使用，并且可以使用多个通配符。  
通配符%看起来像是可以匹配任何东西，但有个例外，这就是NULL。子句WHERE prod_name LIKE '%'不会匹配产品名称为NULL 的行。

除了%通配符，还有一个匹配单个字符的通配符“_”，使用示例如下：
```
select prod_name,prod_price from Products where prod_name like 'Carrot_';
```
通配符“_”必须要匹配一个字符，不能少也不能多，同样也可以出现在搜索模式中的任意位置；

SQL的通配符很有用。但这种功能是有代价的，即通配符搜索一般比前面讨论的其他搜索要耗费更长的处理时间。这里给出一些使用通配符时要记住的技巧：
- 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
- 在确实需要使用通配符时，也尽量不要把它们用在搜索模式的开始处。把通配符置于开始处，搜索起来是最慢的。

## 计算字段
存储在数据库表中的数据一般不是应用程序所需要的格式，下面举几个例子。
- 需要显示公司名，同时还需要显示公司的地址，但这两个信息存储在不同的表列中。
- 城市、州和邮政编码存储在不同的列中（应该这样），但邮件标签打印程序需要把它们作为一个有恰当格式的字段检索出来。
- 列数据是大小写混合的，但报表程序需要把所有数据按大写表示出来。
- 物品订单表存储物品的价格和数量，不存储每个物品的总价格（用价格乘以数量即可）。但为打印发票，需要物品的总价格。  
在上述每个例子中，存储在表中的数据都不是应用程序所需要的。我们需要直接从数据库中检索出转换、计算或格式化过的数据，而不是检索出数据，然后再在客户端应用程序中重新格式化，这就是计算字段；

**计算字段可以通过函数，算术和子查询来实现；**  
计算字段是在SELECT 语句内创建的，只能于单条记录中的数据计算，如果涉及多条记录，一般使用聚集函数。

### 拼接字段
例如，我们要组合供应商表中供应商的名称和位置：
```
select concat(vend_name,'(',vend_country,')') from Vendors;
```
对于MySQL数据库，拼接字段用到了concat函数，而Oracle、SQLite等一般使用“||”进行拼接，Access 和SQL Server 使用“+”；  
拼接字段后，默认的字段名会默认使用拼接的字符串，例如上面检索后的字段名为：
```
concat(vend_name,'(',vend_country,')')
```
这样的列名不利于我们记忆和使用，因此需要给这个新字段取一个别名：
```
select concat(vend_name,'(',vend_country,')') as vend_title from Vendors;
```
取别名，一般使用as关键字；不过在很多DBMS 中，AS关键字是可选的，不过最好使用它，这被视为一条最佳实践。

### 算术计算
计算字段的另一常见用途是对检索出的数据进行算术计算。举个例子，OrderItems 表包含每个订单中的各项物品。下面的SQL语句检索订单号20008中的所有物品的单项总价：
```
select prod_id,quantity*item_price expanded_price from OrderItems where order_num = 20008;
```
quantity物品的数量，item_price是物品单价，所以物品的单项总价为quantity*item_price；另外算术计算支持“+”，“-”，“*”，“/”四个操作符；

## 函数
与大多数其他计算机语言一样，SQL 也可以用函数来处理数据。函数一般是在数据上执行的，为数据的转换和处理提供了方便。在上一节，MySQL数据库可以使用concat函数来拼接字段；

与几乎所有DBMS 都等同地支持SQL 语句（如SELECT）不同，每一个DBMS 都有特定的函数。事实上，只有少数几个函数被所有主要的DBMS等同地支持。虽然所有类型的函数一般都可以在每个DBMS 中使用，各个函数的名称和语法可能极其不同。为了说明可能存在的问题，下表列出了3 个常用的函数及其在各个DBMS 中的语法：
|函数 | 语法|
|:- | :- |
|提取字符串的组成部分 | Access 使用MID() ； DB2 、Oracle 、PostgreSQL 和SQLite 使用SUBSTR()；MySQL和SQL Server使用SUBSTRING()|
|数据类型转换 | Access和Oracle使用多个函数，每种类型的转换有一个函数；DB2和PostgreSQL使用CAST()；MariaDB、MySQL和SQL Server使用CONVERT()|
|取当前日期 | Access使用NOW() ； DB2 和PostgreSQL 使用CURRENT_DATE ；MariaDB和MySQL使用CURDATE()；Oracle使用SYSDATE；SQL Server使用GETDATE()；SQLite使用DATE()|

大多数SQL 实现支持以下类型的函数。  
- 用于处理文本字符串（如删除或填充值，转换值为大写或小写）的文本函数。
- 用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数。
- 用于处理日期和时间值并从这些值中提取特定成分（如返回两个日期之差，检查日期有效性）的日期和时间函数。
- 返回DBMS 正使用的特殊信息（如返回用户登录信息）的系统函数。

我们在上一课看到函数用于SELECT 后面的列名，但函数的作用不仅于此。它还可以作为SELECT 语句的其他成分，如在WHERE 子句中使用，在其他SQL 语句中使用等；

### 文本函数
|函数	| 语法|
|:- | :- |
|LEFT()（或使用子字符串函数）| 返回字符串左边的字符|
|RIGHT()（或使用子字符串函数）| 返回字符串右边的字符|
|LENGTH()（也使用DATALENGTH()或LEN()）| 返回字符串的长度|
|LOWER()（Access使用LCASE()）| 将字符串转换为小写|
|UPPER()（Access使用UCASE()）|	将字符串转换为大写|
|LTRIM() |	去掉字符串左边的空格|
|RTRIM()	| 去掉字符串右边的空格|
|TRIM()	| 去掉字符串左右两边的空格|
|CONCAT() |	拼接字符串|
|SOUNDEX() | 返回字符串的SOUNDEX值|

SOUNDEX 是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX 考虑了类似的发音字符和音节，使得能对字符串进行发音比较而不是字母比较，一般用于发音相似的字段检索；

去除左右空格
```
select trim(prod_name) from Products;
```

### 日期和时间函数
日期和时间采用相应的数据类型存储在表中，每种DBMS 都有自己的特殊形式。日期和时间值以特殊的格式存储，以便能快速和有效地排序或过滤，并且节省物理存储空间。  
下面举MySQL中获取当前日期和时间的函数：
|函数	| 语法|
|:- | :- |
|now() | 返回当前日期+时间|
|curdate() | 返回当前的日期|
|current_date() | 同curdate()|
|current_date |	同curdate()|
|curtime() |	返回当前的时间|
|current_time() |	同curtime()|
|current_time | 同curtime()|
|sysdate() |	sysdate()日期时间函数跟 now() 类似，不同之处在于：now()在执行开始时值就得到了，sysdate()在函数执行时动态得到值|
|current_timestamp() | 获取当前时间戳|
|current_timestamp | 同current_timestamp()|

日期和时间转换函数：
|函数	| 语法|
|:- | :-|
|date_format(str|datetime,format)	| 日期和时间格式化|
|time_format(str|datetime,format) | 时间格式化|
|str_to_date(str, format) | 字符串转换为日期和时间|
|to_days(datetime) | 将日期转换成天数，从公元元年开始计算|
|from_days(days) | 将天数转换成日期，从公元元年开始计算|
|time_to_sec(time) | 将时间转换成秒数|
|sec_to_time(seconds) | 将秒数转换成时间|
|makedate(year,dayofyear) | 拼接年和年中的天|
|maketime(hour,minute,second)	| 拼接时分秒|

日期和时间选取函数：
|函数	| 语法|
|:- | :- |
|date(str\|datetime) | 获取日期|
|time(str\|datetime) | 获取时间|
|year(str\|datetime) | 获取年|
|month(str\|datetime)	| 获取月|
|day(str\|datetime) |	获取日|
|hour(str\|datetime) | 获取时|
|minute(str\|datetime) | 获取分|
|second(str\|datetime) | 获取秒|
|microsecond(str\|datetime)	| 获取毫秒|
|quarter(str\|datetime) | 获取季度|
|week(str\|datetime) | 获取一年内的第几周|
|weekofyear(str\|datetime) | 同上|
|dayofyear(str\|datetime) |	获取一年内的第几天|
|dayofmonth(str\|datetime) | 获取一月内的第几天|
|dayofweek(str\|datetime)	| 获取一周内的第几天|
|dayname(str\|datetime) | 获取英文的星期几|
|monthanme(str\|datetime)	| 获取英文的几月|
|last_day(str\|datetime) | 获取月中的最后一天的日期|

日期和时间计算的函数：
|函数	| 语法|
|:- |:-|
|date_add(str\|datetime,interval expr type)	| 给指定日期加上某一时间间隔|
|date_sub(str\|datetime,interval expr type)	| 给指定日期减去某一时间间隔|
|period_diff(str\|datetime, str\|datetime) | 获取两个日期的间隔月数|
|datediff(str\|datetime, str\|datetime) |	获取两个日期的间隔天数|
|timediff(str\|datetime, str\|datetime) | 获取两个时期的间隔时间|

其中interval expr type的参考写法如下：
```
interval 1 year
interval 1 month
interval 1 day
interval 1 hour
interval 1 minute
interval 1 second
interval 1 microsecond
```
#### 1、日期和时间格式化：
```
select date_format('2008-08-08 22:23:01', '%Y%m%d%H%i%s');
-- 20080808222301
```
其中Y表示年，m表示月，d表示日，H表示时，i表示分，s表示秒；
时间格式化：
```
select time_format('2008-08-08 22:23:01', '%Y%m%d%H%i%s');
-- 00000000222301
```
可以time_format处理不了日期，但date_format可以处理时间；

#### 2、字符串转换成日期时间：
```
select str_to_date('08/09/2008', '%m/%d/%Y');
-- 2008-08-09
```

### 数值函数
数值处理函数仅处理数值数据。这些函数一般主要用于代数、三角或几何运算；
|函数	| 语法|
|:- |:-|
|ABS() | 返回一个数的绝对值|
|COS() | 返回一个角度的余弦|
|EXP() | 返回一个数的指数值|
|PI()	| 返回圆周率|
|SIN() | 返回一个角度的正弦|
|SQRT() |	返回一个数的平方根|
|TAN() | 返回一个角度的正切|
|MOD() | 返回除操作的余数|
|RAND() | 返回随机数|
|ROUND(x,d) |	四舍五入，当d为0时可以省略，当d为正数时取小数点后d位小数，当d为负数时略去小数，将小数点左边d位均设置为0；|
|TRUNCATE(x,d) | 规则与ROUND(x,d)相同，只是不进行四舍五入，而且d不能省略|
|FORMA(x,d) |	强制保留D位小数，位数不够用0补，整数部分超过三位的时候以逗号分割，并且返回的结果是string类型的|
|CONVERT(value，type)	| 类型转换|

#### 1、取余：
```
select mod(3,2);
-- 1
```
#### 2、四舍五入：
```
select round(123.5),round(123.5,2),round(123.512,2),round(132.5,-2);
-- 124 |         123.50 |           123.51 |             100
```
#### 3、截取数字：
```
select truncate(123.5,2),truncate(123.512,2),truncate(132.5,-2);
-- 123.50 |              123.51 |                100
```
#### 4、类型转换：
```
select convert(100.3465,decimal(10,2)), convert(100,decimal(10,2)),convert(100.4,decimal(10,2));
-- 100.35 |                     100.00 |                       100.40
```
其中数据类型type有以下可选项：  
- 二进制，同带binary前缀的效果 : BINARY 
- 字符型，可带参数 : CHAR() 
- 日期 : DATE 
- 时间: TIME 
- 日期时间型 : DATETIME 
- 浮点数 : DECIMAL 
- 整数 : SIGNED 
- 无符号整数 : UNSIGNED

### 聚集函数
我们经常需要汇总数据而不用把它们实际检索出来，为此SQL 提供了专门的函数。使用这些函数，SQL 查询可用于检索数据，以便分析和报表生成。这种类型的检索例子有：  
- 确定表中行数（或者满足某个条件或包含某个特定值的行数）
- 获得表中某些行的和；
- 找出表列（或所有行或某些特定的行）的最大值、最小值、平均值。

SQL的聚集函数在各种主要SQL 实现中得到了相当一致的支持；
|函数	| 语法|
|:- | :- |
|AVG() | 返回某列的平均值|
|COUNT() | 返回某列的行数|
|MAX() | 返回某列的最大值|
|MIN() | 返回某列的最小值|
|SUM() | 返回某列值之和|

#### 1、获取Products 表中所有产品的平均价格
```
select avg(prod_price) avg_price from Products;
```
AVG()函数忽略列值为NULL 的行。

#### 2、获取产品的总数
```
select count(prod_id) from Products;
```
使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL 值。
```
select count(*) from Products;
```
使用COUNT(*)对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值。

#### 3、获取Products 表中最贵物品
```
select max(prod_price) from Products;
```
MAX()函数忽略列值为NULL 的行。  
虽然MAX()一般用来找出最大的数值或日期值，但许多（并非所有）DBMS 允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。在用于文本数据时，MAX()返回按该列排序后的最后一行。

#### 4、获取订单号为20008的订单总价
```
select sum(quantity*item_price) from OrderItems where order_num = 20008;
```
SUM()函数忽略列值为NULL 的行。








