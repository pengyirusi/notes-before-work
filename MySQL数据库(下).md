### 事务

#### 什么是事务

+ A给B转账，B收到A的钱

  要么都成功，要么都失败

  将一组SQL放到一个批次中去执行

+ ACID原则

  原子性Atomicity，要么都完成，要么都不完成

  一致性Consistency，针对一个事务操作前与操作后状态一直

  持久性Durability，表示事务结束后的数据不随着外界原因导致数据丢失（事务一旦提交不可逆）

  隔离性Isolation，针对多个用户同时操作，主要是排除其他事务对本次事务的影响

+ 隔离导致的问题

  脏读：指一个事务读取了另外- -个事务未是交的数据。
  不可重复读：在一个事务内读取表中的某一行数据， 多次读取结果不同。(这个不一定是错误， 只是某些场合不对)
  虚读(幻读)：是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。

#### MySQL中的事务

+ 用法

  ```mysql
  -- MySQL 是默认开启事务自动提交的
  SET autocommit = 0 -- 关闭
  SET autocommit = 1 -- 开启
  
  -- 手动处理事务
  SET autocommit = 0 -- 关闭自动提交
  -- 事务开启
  START TRANSACTION -- 标记一个事务的开始，从这个之后的sql都在同一个事务内
  INSERT XXX
  INSERT XXX
  -- 提交：持久化（成功）
  COMMIT
  -- 回滚：回到原来的样子（失败）
  ROLLBACK
  -- 事务结束
  SET autocommit = 1 -- 开启自动提交
  
  SAVEPOINT 保存点名 -- 设置一个事务的保存点
  ROLLBACK TO SAVEPOINT 保存点名 -- 回滚到保存点
  RELEASE SAVEPOINT 保存点名 -- 删除保存点
  ```

+ 模拟场景 转账

  ```mysql
  -- 转账
  CREATE DATABASE shop CHARACTER SET utf8 COLLATE utf8_general_ci
  USE shop
  
  CREATE TABLE `account`(
  	`id` INT(3) NOT NULL AUTO_INCREMENT,
  	`name` VARCHAR(30) NOT NULL,
  	`money` DECIMAL(9,2) NOT NULL,
  	PRIMARY KEY(`id`)
  )ENGINE=INNODB DEFAULT CHARSET=utf8
  
  INSERT INTO account(`name`,`money`)
  VALUES ('A',2000.00),('B',10000.00)
  
  -- 模拟转账：事务
  SET autocommit = 0; -- 关闭自动提交
  START TRANSACTION; -- 开启一个事务
  
  UPDATE account SET money=money-500 WHERE `name` = 'A'; -- A减500
  UPDATE account SET money=money+500 WHERE `name` = 'B'; -- B加500
  
  COMMIT; -- 提交事务
  ROLLBACK; -- 回滚
  
  SET autocommit = 1; -- 恢复默认值
  ```

+ Java中操作事务

  ```java
  方法(){
      try(){
          //正常的业务代码
          commit();
      }catch(){
          rollback();
      }
  }
  ```

### 索引

MySQL官方对索引的定义为：**索引(Index) 是帮助MySQL高效获取数据的数据结构**。提取句子主干，就可以得到索引的本质：索引是数据结构。

+ 索引的分类

  主键索引 PRIMARY KEY：唯一的标识，不可重复，只有一个列作为主键

  唯一索引 UNIQUE KEY：避免重复的列（字段）出现，可以重复，多个列都可以标识为唯一索引

  常规索引 KEY / INDEX：默认的

  全文索引 FULLTEXT INDEX：在特定的数据库引擎下才有，快速定位数据

+ 索引的使用

  ```mysql
  -- 显示所有的索引信息
  SHOW INDEX FROM student
  
  -- 添加一个全文索引 索引名（列名）
  ALTER TABLE school.student ADD FULLTEXT INDEX `name`(`name`);
  
  -- EXPLAIN 分析sql执行的状况
  EXPLAIN SELECT * FROM student; -- 非全文索引
  SELECT * FROM student WHERE MATCH(`name`) AGAINST('刘')
  ```

#### 测试索引

+ 插入100万数据

  ```mysql
  DELIMITER $$ -- 写函数之前必须要写的标志
  CREATE FUNCTION mock_data()
  RETURNS INT
  BEGIN
  	DECLARE num INT DEFAULT 1000000;
  	DECLARE i INT DEFAULT 0;
  	WHILE i<num DO
  		INSERT INTO app_user(`name`,`phone`,`password`,`age`) 
  		VALUES(CONCAT('用户',i),CONCAT('18',FLOOR(1000000000-RAND()*999999999)),UUID(),FLOOR(RAND()*100)); -- 插入语句
  		SET i = i+1;
  	END WHILE;
  	RETURN i;
  END;
  
  CREATE TABLE app_user(
  		`id` INT(3) NOT NULL AUTO_INCREMENT,
  		`name` VARCHAR(20) NOT NULL,
  		`phone` VARCHAR(11) NOT NULL,
  		`password` VARCHAR(50) NOT NULL,
  		`age` INT(3) NOT NULL,
  		PRIMARY KEY(`id`)
  )ENGINE=INNODB DEFAULT CHARSET=utf8;
  
  SELECT mock_data();
  ```

+ 测试

  ```mysql
  SELECT * FROM app_user WHERE `name`='用户9999'; -- 将近1s才找到
  CREATE INDEX id_app_user_name ON app_user(`name`); -- 添加索引，用1s多
  SELECT * FROM app_user WHERE `name`='用户9999'; -- 添加索引后查询0.001s
  ```

+ 索引在小数据量的时候用处不大，大数据的时候效果明显变快

#### 索引原则

+ 索引不是越多越好

+ 不要对经常变动数据加索引

+ 小数据量的表不需要加索引

+ 索引一般加在常用来查询的字段上

+ 索引的数据结构

  Hash类型的索引

  Btree：INNODB的默认数据结构

### 权限管理

#### 用户管理

+ Navicat可视化管理

+ mysql.user 用户表

  ![image-20200512122407552](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200512122407552.png)

  对用户表进行增删改查

  ```mysql
  -- 创建用户
  CREATE USER peng IDENTIFIED BY '123456';
  
  -- 修改当前用户密码
  SET PASSWORD = PASSWORD('111111');
  
  -- 修改指定用户密码
  SET PASSWORD FOR peng = PASSWORD('111111');
  
  -- 重命名
  RENAME USER peng TO pengpeng;
  
  -- 授予权限  库.表  *.*表示全部库的全部表
  GRANT ALL PRIVILEGES ON *.* TO pengpeng; -- 除了GRANT别人，其他事都能干
  
  -- 查询权限
  SHOW GRANT FOR pengpeng;
  SHOW GRANT FOR root@localhost;
  
  -- ROOT用户的权限
  GRANT ALL PRIVILEGES ON *.* TO root@localhost WITH GRANT OPTION;
  
  -- 撤销权限
  REVOKE ALL PRIVILEGES ON *.* FROM pengpeng;
  
  -- 删除用户
  DROP USER pengpeng;
  ```

#### MySQL备份

+ why

  1. 保证重要的数据不丢失

  2. 数据转换
  3. 存起来比较小

+ 备份方式

  1. 直接拷贝物理文件

  2. 可视化工具：导出的是SQL语句，拉过来执行就行了

  3. 命令行 mysqlump 导出

     ```CMD
     mysqlump -h127.0.0.1 -uroot -p123456 school [student] >D:/a.sql #导出
     source D:/a.sql #导入(需要先登录)
     ```

### 规范数据库设计

#### 为什么需要设计

数据库比较复杂的时候，我们就需要设计

+ 设计
  1. 分析需求：分析业务和需要处理的数据库的需求
  2. 概要设计：设计关系图E-R图

![image-20200512130612358](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200512130612358.png)

#### 三大范式

+ 问题

  信息会重复

  更新会导致异常

  插入异常：无法正常显示信息

  删除异常：丢失有效信息

+ 三大范式（规范数据库）

  1. 第一范式(1NF)：要求数据库表的每一列都是不可分割的原子数据项，原子性
  2. 第二范式(1NF)：前提，满足第一范式；每张表只描述一件事情
  3. 第三范式(1NF)：前提，满足第一、二范式；每一列都跟主键直接相关，不能间接相关

+ **规范性 和 性能 的问题**

  一般地，关联查询的表不能超过三张表

  1. 考虑商业化的需求和目标，(成本, 用户体验! )数据库的性能更加重要
  2. 在规范性能的问题的时候，需要适当的考虑一下 规范性!
  3. 故意给某些表增加一些冗余的字段。(从多表查询中变为单表查询)
  4. 故意增加一-些计算列(从大数据量降低为小数据量的查询: 索引)

### JDBC

#### 数据库驱动

+ 驱动：声卡，显卡，数据库

  程序通过数据库驱动连接数据库

+ JDBC

  为了简化开发人员的（对数据库的）操作，提供的一个（Java操作数据库的）规范

+ Java包

  java.sql

  javax.sql

  导入一个数据库驱动包 mysql-connector-java-5.1.47.jar

#### 第一个JDBC程序

准备工作（创建测试数据库）：

```mysql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
id INT PRIMARY KEY,
NAME VARCHAR(40),
PASSWORD VARCHAR(40),
email VARCHAR(60),
birthday DATE
);

INSERT INTO `users`(id,NAME,PASSWORD,email,birthday)
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04')
```

+ 创建一个普通项目

+ 导入数据库驱动

  1. 右键项目，新建一个 lib 目录
  2. 把 jar 包拷贝进来
  3. 右键 lib，选择Add as Library...

+ 编写测试代码

  ```java
  import java.sql.*;
  
  //我的第一个JDBC程序
  public class JdbcFirstDemo {
      public static void main(String[] args) throws Exception {
          //1.加载驱动
          Class.forName("com.mysql.jdbc.Driver"); //固定写法
  
          //2.连接信息，用户信息 和 url
          //SSL安全
          String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
          String username = "root";
          String password = "3306";
  
          //3.连接成功，数据库对象  Connection代表数据库
          Connection connection = null;
          connection = DriverManager.getConnection(url,username,password);
  
          //4.执行SQL的对象
          Statement statement = connection.createStatement();
  
          //5.执行SQL的对象 去执行SQL
          String sql = "SELECT * FROM users";
  
          ResultSet resultSet = statement.executeQuery(sql);
          //返回的结果集，结果集中封装了我们全部的查询出来的结果
  
          while (resultSet.next()){ //是个链表
              System.out.println("id="+resultSet.getObject("id"));
              System.out.println("name="+resultSet.getObject("NAME"));
              System.out.println("pwd="+resultSet.getObject("PASSWORD"));
              System.out.println("email="+resultSet.getObject("email"));
              System.out.println("birth="+resultSet.getObject("birthday"));
              System.out.println("=============================");
          }
  
          //6.释放连接
          resultSet.close();
          statement.close();
          connection.close();
      }
  }
  ```

  + JDBC 对象

    DriverManager：`Class.forName("com.mysql.jdbc.Driver"); //固定写法 加载驱动`

    Connection：代表数据库，数据库能做的他都能做

    ```java
    connection.rollback();
    connection.commit();
    connection.setAutoCommit();
    ...
    ```

    URL：`String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";`

    Statement：执行sql的对象

    ```java
    statement.executeQuery(); //查
    statement.execute(); //任何sql语句
    statement.executeUpdate(); //增删改
    ```

    ResultSet：查询结果集

    ```java
    resultSet.getObject(); //不知道什么类型
    resultSet.getString();
    resultSet.getFloat();
    ...
    resultSet.beforeFirst(); //移动到最前面
    resultSet.afterLast(); //移动到最后面
    resultSet.next(); //移动到下一个数据
    resultSet.previous(); //移动到前一行
    resultSet.absolute(row); //移动到指定行
    ```

    释放资源

    ```java
    resultSet.close();
    statement.close();
    connection.close(); //耗资源，用完关闭
    ```

#### statement对象

+ Jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查,只需要通过这个对象向数据库发送增删改查语句即可。

+ Statement对象的executeUpdate方法，用于向数据库发送增、删、改的sq|语句，executeUpdate执行完后， 将会返回一个整数(即增删改语句导致了数据库几行数据发生了变化)。

+ Statement.executeQuery方法用于向数据库发送查询语句，executeQuery方法返回代表查询结果的ResultSet对象。

+ CRUD操作-create

  使用executeUpdate(String sq|)方法完成数据添加操作，示例操作：

  ```java
  Statement st = conn.createStatement();
  Stirng sql = "insert into user(...) values(...)";
  int num = st.executeUpdate(sql);
  if(num>0){
      System.out.println("插入成功！！");
  }
  ```

+ CRUD操作-delete

  使用executeUpdate(String sq|)方法完成数据删除操作，示例操作：

  ```java
  Statement st = conn.createStatement();
  Stirng sql = "delete from user where id=1";
  int num = st.executeUpdate(sql);
  if(num>0){
      System.out.println("删除成功！！");
  }
  ```

+ CRUD操作-update

  使用executeUpdate(String sq|)方法完成数据修改操作，示例操作：

  ```java
  Statement st = conn.createStatement();
  Stirng sql = "update user set name='pengpeng' where name='peng'";
  int num = st.executeUpdate(sql);
  if(num>0){
      System.out.println("修改成功！！");
  }
  ```

+ CRUD操作-read

  使用executeQuery(String sq|)方法完成数据查询操作，示例操作：

  ```java
  Statement st = conn.createStatement();
  Stirng sql = "select * from user where id=1";
  ResultSet rs = st.executeQuery(sql);
  while(rs.next){
      //根据获取列的数据类型，分别调用rs的相应方法映射到java对象中
  }
  ```

+ 代码

  1. 配置

     ![image-20200512161105966](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200512161105966.png)

  2. 工具类 JbdcUtils

     ```java
     package lesson02.utils;
     
     import java.io.InputStream;
     import java.sql.*;
     import java.util.Properties;
     
     public class JdbcUtils {
     
         private static String driver = null;
         private static String url = null;
         private static String username = null;
         private static String password = null;
     
         static{
             try{
                 InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
                 Properties properties = new Properties();
                 properties.load(in);
     
                 driver = properties.getProperty("driver");
                 url = properties.getProperty("url");
                 username = properties.getProperty("username");
                 password = properties.getProperty("password");
     
                 //1.驱动只用加载一次
                 Class.forName(driver);
     
             }catch (Exception e){
                 e.printStackTrace();
             }
         }
     
         //获取连接
         public static Connection getConnection() throws SQLException {
             return DriverManager.getConnection(url,username,password);
         }
     
         //释放资源
         public static void release(Connection conn, Statement st, ResultSet rs){
             if (rs!=null){
                 try {
                     rs.close();
                 } catch (SQLException e) {
                     e.printStackTrace();
                 }
             }
             if (st!=null){
                 try {
                     st.close();
                 } catch (SQLException e) {
                     e.printStackTrace();
                 }
             }
             if (conn!=null){
                 try {
                     conn.close();
                 } catch (SQLException e) {
                     e.printStackTrace();
                 }
             }
         }
     }
     ```

  3. 测试类

     ```java
     package lesson02;
     
     import lesson02.utils.JdbcUtils;
     
     import java.sql.Connection;
     import java.sql.ResultSet;
     import java.sql.SQLException;
     import java.sql.Statement;
     
     public class TestDelete {
         public static void main(String[] args) {
     
             Connection conn = null;
             Statement st = null;
             ResultSet rs = null;
     
             try {
                 conn = JdbcUtils.getConnection(); //获取数据库连接
                 st = conn.createStatement(); //获得执行sql的对象
                 String sql = "DELETE * FROM users WHERE id=1";
                 int i=st.executeUpdate(sql);
                 if (i>0){
                     System.out.println("删除成功!!");
                 }
             } catch (SQLException e) {
                 e.printStackTrace();
             }finally {
                 JdbcUtils.release(conn,st,rs);
             }
         }
     }
     ```

     

+ SQL注入的问题

  sql存在漏洞，会被攻击

  login(username=" ' or ' 1=1 ",password=" ' or ' 1=1 ")

#### PrepareStatement

+ 是Statement的子类，可以防止sql注入，并且效率更高

  ```java
  package lesson03;
  
  import lesson02.utils.JdbcUtils;
  
  import java.sql.Connection;
  import java.sql.PreparedStatement;
  import java.sql.SQLException;
  import java.util.Date;
  
  public class TestInsert {
      public static void main(String[] args) {
          Connection conn = null;
          PreparedStatement st = null;
  
          try {
              conn = JdbcUtils.getConnection();
  
              //区别
              //使用？占位符代替参数
              String sql = "INSERT INTO user(id,`name`,`password`,`birthday`) VALUES(?,?,?,?)";
  
              st = conn.prepareStatement(sql); //预编译sql，先写sql，然后不执行
  
              //手动给参数赋值
              st.setInt(1,2018215555);
              st.setString(2,"peng");
              st.setString(3,"8098");
              //注意点： sql.Date  数据库  java.sql.Date()转换
              //        util.Date  Java   new Date().getTime() 获得时间戳
              st.setDate(4,new java.sql.Date(new Date().getTime()));
  
              //执行
              int i = st.executeUpdate();
              if (i>0){
                  System.out.println("插入成功！！");
              }
          } catch (SQLException e) {
              e.printStackTrace();
          } finally {
              JdbcUtils.release(conn,st,null);
          }
      }
  }
  ```

+ 防注入本质

  传递过来的参数当作字符，有转义字符时直接忽略

#### IDEA连接数据库

![image-20200512165026503](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200512165026503.png)

#### 事务

+ ACID原则

  原子性，一致性，**隔离性**，持久性

+ 隔离性的问题

  脏读：一个事务读取了另一个没有提交的事务
  不可重复读：在同一个事务内，重复读取表中的数据，表数据发生了改变
  虛读(幻读)：在一个事务内，读取到了别人插入的数据，导致前后读出来结果不一致

  ```java
  package lesson04;
  
  import lesson02.utils.JdbcUtils;
  
  import java.sql.Connection;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  
  public class TestTransaction {
      public static void main(String[] args) {
          Connection conn = null;
          PreparedStatement st = null;
          ResultSet rs = null;
  
          try {
              conn = JdbcUtils.getConnection();
              //关闭数据库的自动提交，自动会开启事务
              conn.setAutoCommit(false); //开启事务
  
              String sql1 = "UPDATE account set money=money-100 WHERE name='A'";
              st = conn.prepareStatement(sql1);
              st.executeUpdate();
  
              String sql2 = "UPDATE account set money=money+100 WHERE name='B'";
              conn.prepareStatement(sql2);
              st.executeUpdate();
  
              //业务完毕，提交事务
              conn.commit();
              System.out.println("成功！！");
          } catch (SQLException e) {
              //conn.rollback(); //失败则回滚，可以不写，失败默认回滚
              e.printStackTrace();
          } finally {
              try {
                  conn.setAutoCommit(true);
              } catch (SQLException e) {
                  e.printStackTrace();
              }
              JdbcUtils.release(conn,st,rs);
          }
      }
  }
  ```

#### 数据库连接池

连接 - - 释放，浪费系统资源

+ 池化技术

  准备一些预先的资源，过来就连接预先准备好的

  常用连接数 10个

  最小连接数 10

  最大连接数 15  业务最高承载上限

  等待超时 100ms

+ 编写连接池

  实现一个接口 DataSource

+ 开源数据源实现(拿来即用)

  DBCP，C3P0，Druid

  使用了这些连接池后，我们在项目开发中就不需要编写连接数据库的代码了

+ DBCP

  需要用到的jar包

  commons-dbcp-1.4、commons-pool-1.6

+ C3P0

  需要用到的jar包

  c3p0-0.9.5.5、mchange-commons-java-0.2.19

+ 结论

  无论使用什么数据源，本质还是一样的，DataSource接口不会变, 方法就不会变