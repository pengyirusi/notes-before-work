+ 登录

```shell
C:\Program Files\MySQL\MySQL Server 5.7\bin>mysql -u admin -p
Enter password: ****

Server version: 5.7.29
```

+ distinct去重

  ```sql
  select distinct vend_id from products;
  ```

  ```sql
  select distinct vend_id, prod_price from products;
  ```

  distinct 后面跟两项时，必须都不相同，才算不同

+ limit

  ```sql
  select prod_name from products limit 5;
  ```

  返回行数不超过 5

+ offset

  ```sql
  select prod_name from products limit 5 offset 5;
  -- 简写
  select prod_name from products limit 5 , 5;
  ```

  从第 5 行开始（下标从 0 开始算），往后检索 5 行

+ order by

  后跟多个参数时，以前面的为主

  ```sql
  mysql> select prod_id, prod_name, prod_price
      -> from products
      -> order by prod_price, prod_name;
  +---------+---------------------+------------+
  | prod_id | prod_name           | prod_price |
  +---------+---------------------+------------+
  | BNBG02  | Bird bean bag toy   |       3.49 |
  | BNBG01  | Fish bean bag toy   |       3.49 |
  | BNBG03  | Rabbit bean bag toy |       3.49 |
  | RGAN01  | Raggedy Ann         |       4.99 |
  | BR01    | 8 inch teddy bear   |       5.99 |
  | BR02    | 12 inch teddy bear  |       8.99 |
  | RYL01   | King doll           |       9.49 |
  | RYL02   | Queen doll          |       9.49 |
  | BR03    | 18 inch teddy bear  |      11.99 |
  +---------+---------------------+------------+
  ```

  可按 select 字段的顺序简写（排序的字段必须在 select 中）

  ```sql
  select prod_id, prod_name, prod_price from products order by 3, 2;
  ```

  order by 必须在 where 之后

+ desc

  ```sql
  select prod_id, prod_name, prod_price
      -> from products
      -> order by 3 desc, 2;
  ```

  desc 应分别放在要排序的列的后边

  默认是 asc 升序

  排序中默认不区分大小写

+ not 写哪都行

  ```sql
  mysql> select prod_name, prod_price from products
      -> where vend_id not in ('DLL01','BRS01')
      -> order by prod_name;
  mysql> select prod_name, prod_price from products
      -> where not vend_id in ('DLL01','BRS01')
      -> order by prod_name;
  +------------+------------+
  | prod_name  | prod_price |
  +------------+------------+
  | King doll  |       9.49 |
  | Queen doll |       9.49 |
  +------------+------------+
  ```

  ```sql
  mysql> select prod_name, prod_price from products
      -> where not prod_price = 9.49;
  ```

+ 通配符

  %，0~多个任意字符

  ```sql
  mysql> select prod_id, prod_name from products
      -> where prod_name like 'fish%';
  mysql> select prod_id, prod_name from products
      -> where prod_name like '%bean bag%';
  mysql> select prod_id, prod_name from products
      -> where prod_name like 'f%y';
  mysql> select prod_id, prod_name from products
      -> where prod_name like '%'; -- 只有null不被匹配
  ```

  _，1个任意字符

  ```sql
  mysql> select prod_id, prod_name from products
      -> where prod_name like '__ inch teddy bear';
  ```

+ 拼接字段

  ```sql
  select concat(vend_name, ' (', vend_country, ')') from vendors;
  ```

  去空格

  ```sql
  select trim(concat(vend_name, ' (', vend_country, ') ')) from vendors;
  ```

  ltrim，去左空格

  rtrim，去右空格

+ 算术运算符

  `+ - * /`

  ```sql
  mysql> select prod_id, quantity, item_price, quantity*item_price as expanded_price
      -> from orderitems
      -> where order_num = 20008;
  +---------+----------+------------+----------------+
  | prod_id | quantity | item_price | expanded_price |
  +---------+----------+------------+----------------+
  | RGAN01  |        5 |       4.99 |          24.95 |
  | BR03    |        5 |      11.99 |          59.95 |
  | BNBG01  |       10 |       3.49 |          34.90 |
  | BNBG02  |       10 |       3.49 |          34.90 |
  | BNBG03  |       10 |       3.49 |          34.90 |
  +---------+----------+------------+----------------+
  ```

+ 文本处理函数

  upper，转大写，lower，转小写

  ```sql
  mysql> select vend_name, upper(vend_name) as vend_name_upcase
      -> from vendors
      -> order by vend_name;
  ```

  length，字符串长度

  soundex，返回 soundex 值

  ```sql
  mysql> select vend_name, soundex(vend_name) as soundex from vendors;
  +-----------------+---------+
  | vend_name       | soundex |
  +-----------------+---------+
  | Bear Emporium   | B65165  |
  | Bears R Us      | B6262   |
  | Doll House Inc. | D4252   |
  | Fun and Games   | F53252  |
  | Furball Inc.    | F61452  |
  | Jouets et ours  | J32362  |
  +-----------------+---------+
  mysql> select cust_name, cust_contact from customers
      -> where soundex(cust_contact) = soundex('michael green');
  +------------+----------------+
  | cust_name  | cust_contact   |
  +------------+----------------+
  | Kids Place | Michelle Green |
  +------------+----------------+
  ```

  SOUNDEX 是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。

  substring(s, start, n)，start 是起始坐标（从 1 开始算），n 是返回最大字符数

+ 日期时间处理函数

  year，哪一年

  ```sql
  mysql> select order_date from orders where year(order_date) = 2012;
  +---------------------+
  | order_date          |
  +---------------------+
  | 2012-05-01 00:00:00 |
  | 2012-01-12 00:00:00 |
  | 2012-01-30 00:00:00 |
  | 2012-02-03 00:00:00 |
  | 2012-02-08 00:00:00 |
  +---------------------+
  ```

  month，day，hour，minute，second

  ```sql
  select order_date from orders where month(order_date) = 01;
  select order_date from orders where day(order_date) = 01;
  ```

+ 数值处理函数

  | 函数 | 说明                                            |
  | ---- | ----------------------------------------------- |
  | abs  | 返回一个数的绝对值                              |
  | cos  | 返回一个角度的余弦，使用弧度制                  |
  | exp  | 返回一个数的指数值，即自然对数 e 的几次方       |
  | pi   | 返回圆周率                                      |
  | sin  | 返回一个角度的正弦，`select sin(pi()/2);`输出 1 |
  | sqrt | 返回一个数的平方根                              |
  | tan  | 返回一个角度的正切                              |

+ 聚集函数

  | 函数  | 说明                                                         |
  | ----- | ------------------------------------------------------------ |
  | avg   | 返回某列的平均值，忽略列中的 null                            |
  | count | 返回某列的行数，count(*) null 值也算，count(字段) null 值不算 |
  | max   | 返回某列的最大值                                             |
  | min   | 返回某列的最小值                                             |
  | sum   | 返回某列值之和                                               |

  ```sql
  select avg(prod_price) as avg_price from products;
  select max(prod_price) as max_price from products;
  ```
  
  ```sql
  mysql> select count(*) as num_cust from customers;
  +----------+
  | num_cust |
  +----------+
  |        5 |
  +----------+
  mysql> select count(cust_email) as num_cust from customers;
  +----------+
  | num_cust |
  +----------+
  |        3 |
  +----------+
  ```
  
  ```sql
  select sum(quantity * item_price) from orderitems;
  ```
  
  去重放在字段前面
  
  ```sql
  mysql> select sum(prod_price) from products;
  +-----------------+
  | sum(prod_price) |
  +-----------------+
  |           61.41 |
  +-----------------+
  
  mysql> select sum(distinct prod_price) from products;
  +--------------------------+
  | sum(distinct prod_price) |
  +--------------------------+
  |                    44.94 |
  +--------------------------+
  ```
  
  组合聚集函数
  
  ```sql
  mysql> select count(*) as num_items,
      -> min(prod_price) as price_num,
      -> max(prod_price) as price_max,
      -> avg(prod_price) as price_avg
      -> from products;
  +-----------+-----------+-----------+-----------+
  | num_items | price_num | price_max | price_avg |
  +-----------+-----------+-----------+-----------+
  |         9 |      3.49 |     11.99 |  6.823333 |
  +-----------+-----------+-----------+-----------+
  ```
  
+ 数据分组 group by

  ```sql
  mysql> select vend_id, count(*) as num_prods
      -> from products
      -> group by vend_id;
  +---------+-----------+
  | vend_id | num_prods |
  +---------+-----------+
  | BRS01   |         3 |
  | DLL01   |         4 |
  | FNG01   |         2 |
  +---------+-----------+
  ```

+ 过滤分组

  having，过滤分组，而 where 过滤行，在分组前使用

  ```sql
  mysql> select vend_id, count(*) as num_prods
      -> from products
      -> group by vend_id
      -> having count(*) > 2;
  +---------+-----------+
  | vend_id | num_prods |
  +---------+-----------+
  | BRS01   |         3 |
  | DLL01   |         4 |
  +---------+-----------+
  
  mysql> select vend_id, count(*) as num_prods
      -> from products
      -> where prod_price >= 4
      -> group by vend_id
      -> having count(*) >= 2;
  +---------+-----------+
  | vend_id | num_prods |
  +---------+-----------+
  | BRS01   |         3 |
  | FNG01   |         2 |
  +---------+-----------+
  ```

  order by 排序放在最最后边

  ```sql
  mysql> select order_num, count(*) as items
      -> from orderitems
      -> group by order_num
      -> having count(*) >= 3
      -> order by items, order_num;
  +-----------+-------+
  | order_num | items |
  +-----------+-------+
  |     20006 |     3 |
  |     20009 |     3 |
  |     20007 |     5 |
  |     20008 |     5 |
  +-----------+-------+
  ```

+ select 字句顺序

  | 子句     | 说明               | 是否必须使用         |
  | -------- | ------------------ | -------------------- |
  | select   | 要返回的列或表达式 | ✔                    |
  | from     | 从中检索数据的表   | 从表中选择数据时使用 |
  | where    | 行级过滤           | ✖                    |
  | group by | 分组说明           | 按组计算聚集时使用   |
  | having   | 组级过滤           | ✖                    |
  | order by | 输出排序顺序       | ✖                    |

+ 子查询

  + 列出订购物品 RGAN01 的所有顾客，分开查询，需要三步

  ```sql
  mysql> select order_num from orderitems where prod_id = 'rgan01';
  +-----------+
  | order_num |
  +-----------+
  |     20007 |
  |     20008 |
  +-----------+
  2 rows in set (0.00 sec)
  
  mysql> select cust_id from orders where order_num in ('20007','20008');
  +------------+
  | cust_id    |
  +------------+
  | 1000000004 |
  | 1000000005 |
  +------------+
  2 rows in set (0.00 sec)
  
  mysql> select cust_id, cust_name from customers where cust_id in ('1000000004','1000000005');
  +------------+---------------+
  | cust_id    | cust_name     |
  +------------+---------------+
  | 1000000004 | Fun4All       |
  | 1000000005 | The Toy Store |
  +------------+---------------+
  ```

  嵌套，合起来写

  ```sql
  select cust_id, cust_name from customers where cust_id in (select cust_id from orders where order_num in (select order_num from orderitems where prod_id = 'rgan01'));
  ```

  作为子查询的 SELECT 语句只能查询单个列，企图检索多个列将返回错误

  + 要显示 Customers 表中每个顾客的订单总数

  ```sql
  mysql> select cust_name, (select count(*) from orders where orders.cust_id = customers.cust_id) as orders from customers order by cust_name;
  +---------------+--------+
  | cust_name     | orders |
  +---------------+--------+
  | Fun4All       |      1 |
  | Fun4All       |      1 |
  | Kids Place    |      0 |
  | The Toy Store |      1 |
  | Village Toys  |      2 |
  +---------------+--------+
  ```

  `表名.列名` 区分混淆的列

+ 联结表

  一个联结的例子

  ```sql
  mysql> select vend_name, prod_name, prod_price from vendors, products
      -> where vendors.vend_id = products.vend_id;
  +-----------------+---------------------+------------+
  | vend_name       | prod_name           | prod_price |
  +-----------------+---------------------+------------+
  | Doll House Inc. | Fish bean bag toy   |       3.49 |
  | Doll House Inc. | Bird bean bag toy   |       3.49 |
  | Doll House Inc. | Rabbit bean bag toy |       3.49 |
  | Bears R Us      | 8 inch teddy bear   |       5.99 |
  | Bears R Us      | 12 inch teddy bear  |       8.99 |
  | Bears R Us      | 18 inch teddy bear  |      11.99 |
  | Doll House Inc. | Raggedy Ann         |       4.99 |
  | Fun and Games   | King doll           |       9.49 |
  | Fun and Games   | Queen doll          |       9.49 |
  +-----------------+---------------------+------------+
  ```

  若没有条件，检索出的行数为第一个表的行数乘以第二个表中的行数（笛卡尔积），要使用 where 筛选出正确的
  
  ```sql
  mysql> select count(*) from vendors;
  +----------+
  | count(*) |
  +----------+
  |        6 |
  +----------+
  
  mysql> select count(*) from products;
  +----------+
  | count(*) |
  +----------+
  |        9 |
  +----------+
  
  mysql> select count(*) from (select vend_name, prod_name, prod_price from vendors, products) as new_table;
  +----------+
  | count(*) |
  +----------+
  |       54 |
  +----------+
  ```
  
  返回笛卡儿积的联结，也称叉联结（cross join）
  
+ 内联结

  ```sql
  mysql> select vend_name, prod_name, prod_price
      -> from vendors as v inner join products as p
      -> on v.vend_id = p.vend_id; -- where结果一样
  ```

+ 联结多个表

  ```sql
  mysql> select prod_name, vend_name, prod_price, quantity
      -> from orderitems as o, products as p, vendors as v
      -> where p.vend_id = v.vend_id and o.prod_id = p.prod_id and order_num = 20007;
  +---------------------+-----------------+------------+----------+
  | prod_name           | vend_name       | prod_price | quantity |
  +---------------------+-----------------+------------+----------+
  | 18 inch teddy bear  | Bears R Us      |      11.99 |       50 |
  | Fish bean bag toy   | Doll House Inc. |       3.49 |      100 |
  | Bird bean bag toy   | Doll House Inc. |       3.49 |      100 |
  | Rabbit bean bag toy | Doll House Inc. |       3.49 |      100 |
  | Raggedy Ann         | Doll House Inc. |       4.99 |       50 |
  +---------------------+-----------------+------------+----------+
  ```

  ```sql
  mysql> select cust_name, cust_contact
      -> from customers as c, orders as o, orderitems as oi
      -> where c.cust_id = o.cust_id and oi.order_num = o.order_num and prod_id = 'rgan01';
  +---------------+--------------------+
  | cust_name     | cust_contact       |
  +---------------+--------------------+
  | Fun4All       | Denise L. Stephens |
  | The Toy Store | Kim Howard         |
  +---------------+--------------------+
  ```

+ 自联结（重复用自己）

  找到与 Jim Jones 同一公司的所有顾客

  用子查询

  ```sql
  mysql> select cust_id, cust_name, cust_contact from customers
      -> where cust_name = (select cust_name from customers where cust_contact = 'jim jones');
  +------------+-----------+--------------------+
  | cust_id    | cust_name | cust_contact       |
  +------------+-----------+--------------------+
  | 1000000003 | Fun4All   | Jim Jones          |
  | 1000000004 | Fun4All   | Denise L. Stephens |
  +------------+-----------+--------------------+
  ```

  表联结

  ```sql
  mysql> select c1.cust_id, c1.cust_name, c1.cust_contact
      -> from customers as c1, customers as c2
      -> where c1.cust_name = c2.cust_name and c2.cust_contact = 'jim jones';
  ```

+ 自然联结

  ```sql
  mysql> select c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price
      -> from customers as c, orders as o, orderitems as oi
      -> where c.cust_id = o.cust_id and oi.order_num = o.order_num and prod_id = 'rgan01';
  ```

+ 外联结

  必须有 left 或 right

  检索所有顾客及每个顾客所下的订单有哪些

  ```sql
  mysql> select customers.cust_id, orders.order_num
      -> from customers inner join orders
      -> on customers.cust_id = orders.cust_id;
  +------------+-----------+
  | cust_id    | order_num |
  +------------+-----------+
  | 1000000001 |     20005 |
  | 1000000001 |     20009 |
  | 1000000003 |     20006 |
  | 1000000004 |     20007 |
  | 1000000005 |     20008 |
  +------------+-----------+
  
  mysql> select c.cust_id, o.order_num
      -> from customers as c left outer join orders as o
      -> on c.cust_id = o.cust_id;
  +------------+-----------+
  | cust_id    | order_num |
  +------------+-----------+
  | 1000000001 |     20005 |
  | 1000000001 |     20009 |
  | 1000000002 |      NULL |
  | 1000000003 |     20006 |
  | 1000000004 |     20007 |
  | 1000000005 |     20008 |
  +------------+-----------+
  
  mysql> select c.cust_id, o.order_num
      -> from customers as c right outer join orders as o
      -> on c.cust_id = o.cust_id;
  +------------+-----------+
  | cust_id    | order_num |
  +------------+-----------+
  | 1000000001 |     20005 |
  | 1000000001 |     20009 |
  | 1000000003 |     20006 |
  | 1000000004 |     20007 |
  | 1000000005 |     20008 |
  +------------+-----------+
  ```

+ 聚集函数 + 联结

  检索所有顾客及每个顾客所下的订单数

  ```sql
  mysql> select c.cust_id, count(o.order_num) as num_order
      -> from customers as c inner join orders as o
      -> on c.cust_id = o.cust_id
      -> group by c.cust_id;
  +------------+-----------+
  | cust_id    | num_order |
  +------------+-----------+
  | 1000000001 |         2 |
  | 1000000003 |         1 |
  | 1000000004 |         1 |
  | 1000000005 |         1 |
  +------------+-----------+
  
  mysql> select c.cust_id, count(o.order_num) as num_order
      -> from customers as c left outer join orders as o
      -> on c.cust_id = o.cust_id
      -> group by c.cust_id;
  +------------+-----------+
  | cust_id    | num_order |
  +------------+-----------+
  | 1000000001 |         2 |
  | 1000000002 |         0 |
  | 1000000003 |         1 |
  | 1000000004 |         1 |
  | 1000000005 |         1 |
  +------------+-----------+
  ```

+ 什么时候使用联结

  + 注意所使用的联结类型。一般我们使用内联结，但使用外联结也有效
  + 关于确切的联结语法，应该查看具体的文档，看相应的 DBMS 支持何种语法
  + 保证使用正确的联结条件（不管采用哪种语法），否则会返回不正确的数据。
  + 应该总是提供联结条件，否则会得出笛卡儿积
  + 在一个联结中可以包含多个表，甚至可以对每个联结采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一起测试它们前分别测试每个联结。这会使故障排除更为简单

+ 组合查询

  同一个表

  ```sql
  mysql> select cust_name, cust_contact, cust_email
      -> from customers
      -> where cust_state in ('il','in','mi')
      -> union
      -> select cust_name, cust_contact, cust_email
      -> from customers
      -> where cust_name = 'fun4all';
      -> order by cust_name, cust_contact;
  ```

  相当于

  ```sql
  mysql> select cust_name, cust_contact, cust_email from customers
      -> where cust_state in ('il','in','mi') or cust_name = 'fun4all';
  ```

  union 可自动去重，不想去重可用 union all

  union 规则

  + UNION 必须由两条或两条以上的 SELECT 语句组成，语句之间用关键字UNION 分隔（因此，如果组合四条SELECT语句，将要使用三个UNION关键字）
  + UNION 中的每个查询必须包含相同的列、表达式或聚集函数（不过，各个列不需要以相同的次序列出）
  + 列数据类型必须兼容：类型不必完全相同，但必须是 DBMS 可以隐含转换的类型（例如，不同的数值类型或不同的日期类型）

+ 数据插入

  直接按字段顺序，不建议使用

  ```sql
  mysql> insert into customers
      -> values('1000000006','toy land','123 street','Beijing','BJ','102249','China',null,null);
  ```

  把字段写上，前后对应插入

  ```sql
  mysql> insert into customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country)
      -> values('1000000006','toy land','123 street','Beijing','BJ','102249','China');
  ```

  insert 还存在另一种形式，可以利用它将 select 语句的结果插入表中

  如，把另一表中的顾客列合并到 customers 表中

  ```sql
  mysql> insert into customers(cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country)
      -> select cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country
      -> from custnew;
  ```

+ 表复制

  ```sql
  mysql> create table custcopy as
      -> select * from customers;
  ```

+ 更新数据

  ```sql
  mysql> update customers
      -> set cust_email = 'kim@thetoystore.com'
      -> where cust_id = '1000000005';
  ```

  set 中若有多个字段，用逗号隔开

  删除某行的某个值，可以把它 set 成 null，表示空，不用 where 即可删除一列

+ 删除数据

  删除一行

  ```sql
  mysql> delete from customers
      -> where cust_id = '1000000006';
  ```

  若没有 where 将删除所有行

  ```sql
  mysql> delete from custcopy;
  Query OK, 6 rows affected (0.01 sec)
  ```

  或者使用 truncate，速度更快

  ```sql
  mysql> truncate table custcopy;
  ```

  联通表结构一块删除

  ```sql
  mysql> drop table custcopy;
  ```

+ 创建表

  ```sql
  mysql> create table t1(
      -> id	char(10)	not null,
      -> name	char(10),
      -> age	integer		default 18
      -> );
  ```

+ 更新表

  增加一列

  ```sql
  mysql> alter table t1
      -> add tel char(20);
  ```

  删除一列

  ```sql
  mysql> alter table t1
      -> drop column tel;
  ```

  删除表

  ```sql
  mysql> drop table t1;
  ```

  重命名表

  ```sql
  mysql> rename table os to orders;
  ```

+ 创建视图

  create view 接 select

  本质就是存下一个 select 的查询结果，方便以后使用

  ```sql
  mysql> create view productcustomers as
      -> select cust_name, cust_contact, prod_id
      -> from customers as c, orders as o, orderitems as oi
      -> where c.cust_id = o.cust_id and oi.order_num = o.order_num;
  ```

  ```sql
  mysql> select cust_name, cust_contact
      -> from productcustomers
      -> where prod_id = 'rgan01'
      -> order by cust_name; -- 不建议在这排序
  +---------------+--------------------+
  | cust_name     | cust_contact       |
  +---------------+--------------------+
  | Fun4All       | Denise L. Stephens |
  | The Toy Store | Kim Howard         |
  +---------------+--------------------+
  ```

  ```sql
  mysql> create view vendorlocations as
      -> select concat(vend_name, ' (', vend_country, ')') as vend_title
      -> from vendors;
  ```

  ```sql
  mysql> select * from vendorlocations;
  +-------------------------+
  | vend_title              |
  +-------------------------+
  | Bear Emporium (USA)     |
  | Bears R Us (USA)        |
  | Doll House Inc. (USA)   |
  | Fun and Games (England) |
  | Furball Inc. (USA)      |
  | Jouets et ours (France) |
  +-------------------------+
  ```

+ 视图过滤

  只看有 email 的用户

  ```sql
  mysql> create view customeremaillist as
      -> select cust_id, cust_name, cust_email from customers
      -> where cust_email is not null;
  
  mysql> select * from customeremaillist;
  +------------+---------------+-----------------------+
  | cust_id    | cust_name     | cust_email            |
  +------------+---------------+-----------------------+
  | 1000000001 | Village Toys  | sales@villagetoys.com |
  | 1000000003 | Fun4All       | jjones@fun4all.com    |
  | 1000000004 | Fun4All       | dstephens@fun4all.com |
  | 1000000005 | The Toy Store | kim@thetoystore.com   |
  +------------+---------------+-----------------------+
  ```

+ 事务

  开启事务首先要关闭自动提交，`set autocommit = 0;`
  
  ```sql
  mysql> start transaction;
  mysql> update acount
      -> set money = money - 500
      -> where name = 'a';
  mysql> update acount
      -> set money = money + 500
      -> where name = 'b';
  mysql> commit;
  ```
  
+ 回滚

  ```sql
  mysql> delete from acount where name = 'a';
  mysql> select * from acount;
  +------+-------+
  | name | money |
  +------+-------+
  | b    |  1500 |
  +------+-------+
  mysql> rollback;
  mysql> select * from acount;
  +------+-------+
  | name | money |
  +------+-------+
  | a    |   500 |
  | b    |  1500 |
  +------+-------+
  ```

+ 保留点

  savepoint，返回的点

  ```sql
  mysql> savepoint delete1;
  
  mysql> delete from acount where name = 'a';
  mysql> delete from acount where name = 'b';
  
  mysql> select * from acount;
  Empty set (0.00 sec)
  
  mysql> rollback; -- 或者使用 rollback to delete1;
  
  mysql> select * from acount;
  +------+-------+
  | name | money |
  +------+-------+
  | a    |   500 |
  | b    |  1500 |
  +------+-------+
  ```

  在事务中也一样使用

+ 添加主键

  + 创建时添加 primary key

  + 后来添加

  ```sql
  mysql> alter table acount
      -> add primary key(name);
  ```

+ 添加索引

  ```sql
  mysql> create index name_idx
      -> on acount(name);
  ```

+ Bye

  ```sql
  mysql> quit;
  Bye
  ```