MyBatis

学习环境：

+ JDK 1.8
+ MySQL 5.7
+ maven 3.x
+ IDEA

官方中文文档：[https://mybatis.org/mybatis-3/zh/index.html](https://mybatis.org/mybatis-3/zh/index.html)

## 1. 简介

### 1.1 什么是 MyBatis

+ MyBatis 是一款优秀的 **持久层** 框架
+ 它支持定制化SQL、存储过程以及高级映射。
+ MyBatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。
+ MyBatis可以使用简单的XML或注解来配置和映射原生类型、接口和Java的POJO (Plain Old Java Objects,普通老式Java对象)为数据库中的记录。
+ MyBatis本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation迁移到了google code,并且改名为MyBatis。
+ 2013年11月迁移到Github。

如何获得：

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```

+ maven 仓库：[https://mvnrepository.com/search?q=mybatis](https://mvnrepository.com/search?q=mybatis)
+ github：[https://github.com/mybatis/mybatis-3](https://github.com/mybatis/mybatis-3)

### 1.2 持久化

数据持久化

+ 持久化就是将程序的数据在**持久状态**和**瞬时状态**转化的过程
+ 内存：断电即失
+ 数据库(jdbc)，io文件持久化

为什么要持久化？

+ 有一些对象，用完不能丢掉
+ 内存太贵了

### 1.3 持久层

Dao层，Service层，Controller层...

+ 完成持久化工作的代码块
+ 界限十分明显

### 1.4 为什么需要MyBtis

+ 帮助程序员将数据存入到数据库中
+ 方便，传统的JDBC代码太复杂了，简化，框架
+ sql写在xml里，sql与代码分离

## 2. 第一个MyBatis程序

思路：搭建环境 => 导入依赖 => 编写代码 => 调试

**搭建数据库**

```sql
CREATE DATABASE `mybatis`

USE `mybatis`

CREATE TABLE `user`(
	`id` INT(20) NOT NULL PRIMARY KEY,
	`name` VARCHAR(30) DEFAULT NULL,
	`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user` (`id`,`name`,`pwd`) VALUES
(1,'小明','123456'),
(2,'张三','123457'),
(3,'李四','123458')
```

### 2.1 新建项目

1. 新建一个普通的maven项目

2. 删除src目录，父工程

3. 导入maven依赖

   ```xml
   	<dependencies>
           <!--mysql驱动-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.47</version>
           </dependency>
           <!--mybatis-->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.2</version>
           </dependency>
           <!--junit-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.12</version>
           </dependency>
       </dependencies>
   ```

### 2.2 创建一个模块

子模块继承父工程的依赖

```xml
    <parent>
        <artifactId>Mybatis-study</artifactId>
        <groupId>com.peng</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
```

父工程出现子模块

```xml
	<modules>
        <module>mybatis-01</module>
    </modules>
```

+ 编写mybaitis核心配置文件 `src/main/resources/mybatis-config.xml`

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!--核心配置文件-->
  <configuration>
      <!--定义多套环境-->
      <environments default="development">
          <environment id="development">
              <!--事务管理-->
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                  <property name="username" value="admin"/>
                  <property name="password" value="8098"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <mapper resource="org/mybatis/example/BlogMapper.xml"/>
      </mappers>
  </configuration>
  ```

+ 编写mybatis工具类，固定写法

  ```java
  public class MybatisUtils {
  
      private static SqlSessionFactory sqlSessionFactory;
  
      static {
          try {
              //第一步：获取sqlSessionFactory对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  
          }catch (IOException e){
              e.printStackTrace();
          }
      }
  
      //既然有了sq1SessionFactory，顾名思义，我们就可以从中获得sqlSession 的实例了。
      // sqlSession 完全包含了面向数据库执行SQL命令所需的所有方法。
      public static SqlSession getSqlSession(){
          return sqlSessionFactory.openSession();
      }
  }
  ```

### 2.3 编写代码

+ 实体类

  ```java
  public class User {
      private int id;
      private String name;
      private String pwd;
  
      public User() {
      }
  
      public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", pwd='" + pwd + '\'' +
                  '}';
      }
  }
  ```

+ Dao接口

  ```java
  public interface UserDao {
      List<User> getUserList();
  }
  ```

+ 接口实现类：由原来的 UserDaoImpl转换为一个Mapper.xml配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--命名空间-->
  <mapper namespace="com.peng.dao.UserDao">
      <!--查询语句：id是方法名-->
      <select id="getUserList" resultType="com.peng.pojo.User">
          SELECT * FROM mybatis."user"
      </select>
  </mapper>
  ```

### 2.4 测试

注意点：`org.apache.ibatis.binding.BindingException: Type interface com.peng.dao.UserDao is not known to the MapperRegistry.`

MapperRegistry 注册

```xml
	<!--每一个Mapper.xml都需要(Mybatis核心配置文件中注册!-->
    <mappers>
        <mapper resource="com/peng/dao/UserMapper.xml"/>
    </mappers>
```

配置之后还是出错：`java.io.IOException: Could not find resource com/peng/dao/UserMapper.xml`

maven由于他的约定大于配置，我们之后可以能遇到我们写的配置文件，无法被导出或者生效的问题，解决方案：

```xml
	<build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

又出现编码bug：`com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 3 字节的 UTF-8 序列的字节 3 无效`，在总的 pom 文件配置 utf-8

```xml
	<properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
```

+ Junit 测试代码

  ```java
  public class UserDaoTest {
      
      @Test
      public void test(){
          //1.获得sqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  
          //执行sql
          //方式一：getMapper
          UserDao mapper = sqlSession.getMapper(UserDao.class);
          List<User> userList = mapper.getUserList();
          for (User user : userList) {
              System.out.println(user);
          }
          sqlSession.close(); //关闭资源
      }
  }
  ```

可能遇到的问题：

1. 配置文件没有注册
2. 绑定接口错误
3. 方法名不对
4. 返回类型不对
5. Maven导出资源问题

### 2.5 重要的几个类

#### SqlSessionFactoryBuilder

这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。 你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它，以保证所有的 XML 解析资源可以被释放给更重要的事情。

#### SqlSessionFactory

SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。 使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建 SqlSessionFactory 被视为一种代码“坏习惯”。因此 SqlSessionFactory 的最佳作用域是应用作用域。 有很多方法可以做到，最简单的就是使用单例模式或者静态单例模式。

#### SqlSession

每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。 如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 换句话说，**每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它**。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。 下面的示例就是一个确保 SqlSession 关闭的标准模式：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
    //你的应用逻辑代码
}catch(){
    //捕获异常
}finally{
    //关闭资源
}
```

在所有代码中都遵循这种使用模式，可以保证所有数据库资源都能被正确地关闭。

## 3. CRUD

### 3.1 namespace

namespace中的包名要和Dao/Mapper接口的包名一致

### 3.2 添加CRUD功能

+ id：对应的namespace中的方法名
+ resultType：sql语句执行的返回值
+ parameterType：输入参数类型，int可以省略

**添加应用的过程**

只需改以下几部分，别的地方不用动

1. 接口添加功能 UserMapper.java

```java
    //根据id查询用户
    User getUserById(int i);
	
	//插入用户
    int addUser(User user);

	//修改用户
    int updateUser(User user);

	//删除用户
    int deleteUser(int i);
```

2. xml 对应实现，接收数据用 `#{ }`

```xml
	<select id="getUserById" parameterType="int" resultType="com.peng.pojo.User">
        SELECT * FROM mybatis.user WHERE id = #{id}
    </select>
	
	<!--对象中的属性可以直接取出来-->
    <insert id="addUser" parameterType="com.peng.pojo.User">
        INSERT INTO mybatis.user (id, name, pwd) VALUES (#{id},#{name},#{pwd})
    </insert>

    <update id="updateUser" parameterType="com.peng.pojo.User">
        UPDATE mybatis.user SET name=#{name},pwd=#{pwd} WHERE id=#{id};
    </update>

    <delete id="deleteUser" parameterType="int">
        DELETE FROM mybatis.user WHERE id=#{id}
    </delete>
```

3. 测试类

```java
	@Test
    public void getUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession(); //固定

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User userById = mapper.getUserById(1);
        System.out.println(userById);

        sqlSession.close(); //固定
    }
	
	//增删改需要提交事务 ※※※ 否则不生效
    @Test
    public void addUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int tom = mapper.addUser(new User(4, "Tom", "123459"));
        if (tom > 0) System.out.println("插入成功！");

       
        sqlSession.commit(); //提交事务
        sqlSession.close();
    }

	@Test
    public void updateUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int jerry = mapper.updateUser(new User(4, "Jerry", "123450"));
        if (jerry > 0) System.out.println("修改成功！");

        sqlSession.commit(); //提交事务
        sqlSession.close();
    }

	@Test
    public void deleteUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        int jerry = mapper.deleteUser(4);
        if (jerry > 0) System.out.println("修改成功！");

        sqlSession.commit(); //提交事务
        sqlSession.close();
    }
```

### 3.3 常见错误

+ 标签不要匹配错
+ resource绑定mapper，需要使用路径
+ 程序配置文件必须符合规范
+ NullPointerException，没有注册到资源
+ 输出的xml文件中存在中文乱码问题
+ maven资源没有导出问题

### 3.4 万能Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map！

接口

```java
	//万能的map
    int addUser2(Map<String,Object> map);
```

实现

```xml
    <insert id="addUser2" parameterType="map">
        <!--可以自由组合想赋值的属性-->
        INSERT INTO mybatis.user (id, name, pwd) VALUES (#{userid},#{userName},#{password})
    </insert>
```

测试

```java
@Test
public void addUser2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Object> map = new HashMap<String, Object>();
    map.put("userid",5);
    map.put("userName","hello");
    map.put("password","2333");
    mapper.addUser2(map);

    sqlSession.commit();//提交事务
    sqlSession.close();
}
```

Map传递参数，直接在sql中取出key即可！【parameterType="map"】

对象传递参数，直接在sql中取对象的属性即可！【parameterType="Object"】

只有一个基本类型参数的情况下，可以直接在sql中取到！

多个参数用 Map，或者**注解**！

### 3.5 模糊查询

```java
List<User> getUserLike(String value);
```

```xml
<select id="getUserLike" resultType="com.peng.pojo.User">
    SELECT * FROM mybatis.user WHERE name LIKE #{value}
</select>
```

```java
	@Test
    public void getUserLike(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userLike = mapper.getUserLike("李");
        for (User user : userLike) {
            System.out.println(user);
        }

        sqlSession.close();
    }
```

或者改成

```sql
SELECT * FROM mybatis.user WHERE name LIKE #{value}"%"
```

```java
List<User> userLike = mapper.getUserLike("李");
```

在 sql 拼接中使用通配符，可以防止 sql 注入

## 4. 配置解析

### 4.1 核心配置文件

+ mybatis-config.xml

+ MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息

  ```xml
  configuration（配置）
  properties（属性）
  settings（设置）
  typeAliases（类型别名）
  typeHandlers（类型处理器）
  objectFactory（对象工厂）
  plugins（插件）
  environments（环境配置）
  environment（环境变量）
  transactionManager（事务管理器）
  dataSource（数据源）
  databaseIdProvider（数据库厂商标识）
  mappers（映射器）
  ```

### 4.2 environments 环境配置

MyBatis 可以配置成适应多种环境，**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

学会使用配置多套运行环境

Mybatis默认的事务管理器就是 JDBC
连接池：POOLED

### 4.3 properties 属性

我们可以通过 properties 属性来实现引用配置文件

编写一个数据库配置文件 db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8
username=admin
password=8098
```

在核心配置文件中引入

![image-20200713145554869](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200713145554869.png)

```xml
<configuration>
    <!--引入外部配置文件，优先使用外部的配置-->
    <properties resource="db.properties">
        <property name="username" value="wrongname"/>
        <property name="password" value="wrongpwd"/>
    </properties>

    <!--可以定义多套环境-->
    <environments default="test">
        <environment id="test">
            <!--事务管理-->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

+ 可以直接引入外部文件
+ 可以在其中增加一些属性配置
+ 如果两个文件有同一个字段，优先使用外部配置文件的

### 4.4 typeAliases 类型别名

+ 类型别名是为 Java 类型设置一个短的名字
+ 存在的意义仅在于用来减少类完全限定名的冗余

```xml
	<!--给实体类取别名-->
    <typeAliases>
        <typeAlias type="com.peng.pojo.User" alias="User"/>
    </typeAliases>
```

也可以指定一个包名，MyBatis 会在包名下面搜索需要的Java Bean，比如：

扫描实体类的包，它的默认别名就为这个类的类名，**首字母建议使用小写**

```xml
    <!--扫描实体类的包，-->
	<typeAliases>
        <package name="com.peng.pojo"/>
    </typeAliases>
```

在实体类比较少的时候使用第一种，在实体类比较多的时候使用第二种，同时也可以使用注解起别名

```java
@Alias("user")
public class User {...}
```

### 4.5 settings 设置

特别多，完整的：[https://mybatis.org/mybatis-3/zh/configuration.html#settings](https://mybatis.org/mybatis-3/zh/configuration.html#settings)

必须记住的：

| 设置名             | 描述                                                         | 有效值                                                       | 默认值 |
| :----------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----- |
| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| logImpl            | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

### 4.6 mappers 映射器

方式一：【推荐使用】

```xml
<mappers>
	<mapper resource="com/peng/dao/UserMapper.xml"/>
</mappers>
```

方式二：使用 class 文件

```xml
	<mapper class="com.peng.dao.UserMapper"/>
```

方式三：使用扫描包

```xml
	<package name="com.peng.dao"/>
```

方式二和方式三：接口和它的Mapper配置文件必须 **同名** 且必须 **在同一个包下**

### 4.7 其他配置

不是很重要，用的话查文档：[https://mybatis.org/mybatis-3/zh/configuration.html#](https://mybatis.org/mybatis-3/zh/configuration.html#)

### 4.8 生命周期和作用域

![image-20200714104405179](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200714104405179.png)

生命周期，和作用域，是至关重要的，因为错误的使用会导致非常严重的并发问题。

SqlSessionFactoryBuilder

+ 一旦创建了SqlSessionFactory，就不再需要它了
+ 局部变量

SqlSessionFactory

+ 说白了就是可以想象为：数据库连接池
+ SqISessionFactory一旦被创建 就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**。
+ 因此 SqlSessionFactory 的最佳作用域是应用作用域。
+ 最简单的就是使用**单例模式**或者静态单例模式。

SqlSession

+ 连接到连接池的一一个请求！
+ SqlSession的实例不是线程安全的，因此是不能被共享的,所以它的最佳的作域是请求或方法作用域。
+ 用完之后需要赶紧关闭，否则资源被占用！

![image-20200714105001137](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200714105001137.png)

这里面的每一个Mapper, 就代表一个具体的业务！

## 5.ResultMap

解决属性名和字段名不一致的问题

数据库中的字段

![image-20200714105349794](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200714105349794.png)

新建一个项目，拷贝之前的，情况测试实体类字段不一致的情况

```java
public class User {
    private int id;
    private String name;
    private String password; //不一致
}
```

结果出现问题

```java
User{id=1, name='李明', password='null'}
```

原因

```sql
SELECT * FROM mybatis.user WHERE id = #{id}
-- 相当于
SELECT id,name,pwd FROM mybatis.user WHERE id = #{id}
```

解决方法：

+ 起别名

  ```sql
  SELECT id,name,pwd as password FROM mybatis.user WHERE id = #{id}
  ```

+ 结果集映射

  ```xml
  <resultMap id="UserMap123" type="User">
      <!--column数据库中的字段，property实体类的属性-->
      <result column="id" property="id"/>
      <result column="name" property="name"/>
      <result column="pwd" property="password"/>
  </resultMap>
  
  <select id="getUserById" resultMap="UserMap123">
      SELECT * FROM mybatis.user WHERE id = #{id}
  </select>
  ```

+ resultMap 元素是 MyBatis 中最重要最强大的元素

+ ResultMap 的设计思想是，对于简单的语句根本不需要配置显式的结果映射，而对于复杂-点的语句只需要描述它们的关系就行了

+ Resultmap最优秀的地方在于，虽然你已经对它相当了解了，但是根本就不需要显式地用到他们











