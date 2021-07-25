### 整合 JDBC

配置数据库

```yaml
spring:
  datasource:
    username: admin
    password: 8098
    # 如果时区报错了，加一个时区的配置：serverTimezone=UTC
    url: jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=false
    driver-class-name: com.mysql.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
```

测试类，传统方法与数据库建立连接

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class Spring04DataApplicationTest {

    //自动按照yaml导入数据库资源
    @Autowired
    DataSource dataSource;

    @Test
    public void contextLoads() throws Exception {
        //查看一下默认的数据源
        System.out.println(dataSource.getClass());

        //获得数据库连接
        Connection connection = dataSource.getConnection();

        //xxxxTemplateC Springboot已经配置好模板bean，拿来即用CRUD

        //可以进行各种操作了
        Statement statement = connection.createStatement();
        ResultSet resultSet = statement.executeQuery("SELECT * FROM mybatis.student");
        while (resultSet.next()){ //是个链表
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("tid="+resultSet.getObject("tid"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("=============================");
        }

        //关闭
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

JDBCController

```java
package com.peng.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;
import java.util.Map;

@RestController
public class JDBCController {

    @Autowired
    JdbcTemplate jdbcTemplate; //JdbcTemplate类里，啥都有！

    @GetMapping("/userlist")
    //查询数据库的所有信息
    public List<Map<String,Object>> userList(){
        String sql = "select * from user";
        List<Map<String, Object>> list_maps = jdbcTemplate.queryForList(sql);
        return list_maps;
    }

    //增
    @GetMapping("adduser")
    public String addUser(){
        String sql = "insert into mybatis.user(id,name,pwd) values(4,'peng',8098)";
        jdbcTemplate.update(sql);
        return "update-ok";
    }

    //改
    @GetMapping("/updateuser/{id}")
    public String updateUser(@PathVariable("id") int id){
        String sql = "UPDATE mybatis.user set name=?,pwd=? WHERE id="+id;

        //封装
        Object[] objects = new Object[2];
        objects[0] = "peng2";
        objects[1] = "123456";
        jdbcTemplate.update(sql,objects);
        return "update-ok";
    }

    //删
    @GetMapping("/deleteuser/{id}")
    public String deleteUser(@PathVariable("id") int id){
        String sql = "DELETE FROM mybatis.user WHERE id=?";
        jdbcTemplate.update(sql,id);
        return "delete-ok";
    }
}
```

### 整合DRUID数据源

导入依赖

```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.21</version>
</dependency>
```

配置

```yaml
spring:
  datasource:
    username: admin
    password: 8098
    # 如果时区报错了，加一个时区的配置：serverTimezone=UTC
    url: jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=false
    driver-class-name: com.mysql.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    # druid专有配置
    initial-size: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦载的filters，stat: 监控统计、Log4j: 日志记录、wall:防御sql注入
    #如果允许时报错java.lang.ClassNotFoundException: org.apache.Log4j.Priority
    #则导入Log4j依赖即可，Maven 地址: https://mvnrepository. com/artifact/Log4j/Log4j
    filters: stat,wall,1og4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

导入一个功能，以 log4j 为例

```xml
<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
</dependency>
```

后台监控

```java
package com.peng.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;

@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }

    //后台监控：web.xml，ServletRegis trationBean
    //因为SpringBoot内置了servlet容器，所以没有web.xmL
    @Bean
    public ServletRegistrationBean statViewServlet(){
        //固定写法
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");

        //后台需要有人登录
        HashMap<String, String> initParameters = new HashMap<>();

        //增加配置
        initParameters.put("loginUsername","admin"); //登录的key是固定的
        initParameters.put("loginPassword","123456");

        //允许谁可以访问
        initParameters.put("allow",""); //为空代表谁都能访问

        //禁止谁访问 initParameters.put("peng","192.168.1.120");

        bean.setInitParameters(initParameters);
        return bean;
    }

    //filter 过滤器
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean<Filter> bean = new FilterRegistrationBean<>();

        bean.setFilter(new WebStatFilter());

        //过滤哪些请求
        Map<String,String> initParameters = new HashMap<>();

        //这些东西不进行统计
        initParameters.put("exclustions","*.js,*.css,/druid/**");
        bean.setInitParameters(initParameters);
        return bean;
    }
}
```

![image-20200704142531572](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200704142531572.png)

### 整合MyBatis

1. 导入包
2. 配置文件
3. mybatis配置
4. 编写sql
5. 业务层调用dao层
6. controller调用service层

整合包

mybatis-spring-boot-starter

```xml
<!--mybatis-spring-boot-starter：整合-->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.1.2</version>
</dependency>
```

com.peng.mapper.UserMapper.java 接口

```java
import com.peng.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

import java.util.List;

@Mapper //该注解表示这是一个mybatis的mapper类，也可以在主启动类加上MapperScan扫描mapper文件夹
@Repository
public interface UserMapper {

    //接口里的属性相当于 public static final
    /*public static final*/int qwet1 = 18;

    List<User> queryUserList();

    User queryUserById(int id);

    int addUser(User user);

    int updateUser(User user);

    int deleteUser(int id);
}
```

配置resources.mybatis.mapper.UserMapper.xml

找到mybatis中文文档

![image-20200704152148434](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200704152148434.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.peng.mapper.UserMapper"> <!--要改成自己的-->

    <select id="queryUserList" resultType="User">
        SELECT * FROM USER
    </select>

    <select id="queryUserById" resultType="User">
        SELECT * FROM USER WHERE id = #{id};
    </select>

    <select id="addUser" resultType="User">
        INSERT INTO USER (id,name,pwd) VALUES (#{id},#{name},#{pwd})
    </select>

    <select id="updateUser" resultType="User">
        UPDATE USER SET name=#{name},pwd=#{pwd} WHERE id=#{id}
    </select>

    <select id="deleteUser" resultType="int">
        DELETE FROM USER WHERE id=#{id}
    </select>

</mapper>
```

yaml里整合mybatis，很关键

```yaml
mybatis:
  type-aliases-package: com.peng.pojo
  mapper-locations: classpath:mybatis/mapper/*.xml
```

 com.peng.controller.UserController.java

```java
import com.peng.mapper.UserMapper;
import com.peng.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class UserController {

    @Autowired
    private UserMapper userMapper;

    //查询
    @GetMapping("/queryUserList")
    public List<User> queryUserList(){
        List<User> userList = userMapper.queryUserList();
        for (User user : userList) {
            System.out.println(user);
        }
        return userList;
    }

    //添加一个用户
    @GetMapping("/addUser")
    public String addUser(){
        userMapper.addUser(new User(5,"peng","8098"));
        return "add-ok!";
    }

    //修改一个用户
    @GetMapping("updateUser")
    public String updateUser(){
        userMapper.updateUser(new User(5,"peng2","5829"));
        return "update-ok!";
    }

    //删除一个用户
    @GetMapping("deleteUser")
    public String deleteUser(){
        userMapper.deleteUser(5);
        return "delete-ok!";
    }
}
```

M：数据和业务

C：交接

V：前端

### Spring Security(安全)

不是功能性需求

设计之初考虑

框架：shiro、Spring Security，功能类似

认证，授权

+ 功能权限
+ 访问权限
+ 菜单权限
+ ...拦截器，过滤器：大量源生代码

MVC - SPRING - BOOT - 框架思想

导入thymeleaf依赖

```xml
		<dependency>
			<groupId>org.thymeleaf</groupId>
			<artifactId>thymeleaf-spring5</artifactId>
			<version>3.0.11.RELEASE</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-java8time</artifactId>
			<version>3.0.4.RELEASE</version>
			<scope>compile</scope>
		</dependency>
```

com.peng.controller.RouterController.java 搭建环境

```java
package com.peng.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class RouterController {

    @RequestMapping({"","/","/index"})
    public String index(){
        return "index";
    }

    @RequestMapping("toLogin")
    public String toLogin(){
        return "views/login";
    }

    @RequestMapping("/level1/{id}")
    public String level1(@PathVariable("id") int id){
        return "views/level1/"+id;
    }

    @RequestMapping("/level2/{id}")
    public String level2(@PathVariable("id") int id){
        return "views/level2/"+id;
    }

    @RequestMapping("/level3/{id}")
    public String level3(@PathVariable("id") int id){
        return "views/level3/"+id;
    }
}
```

谁都能看见的界面，明显不合理

![image-20200704165541315](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200704165541315.png)

AOP：切面编程

**整合 Spring Security**

记住几个类:

+ WebSecurityConfigurerAdapter: 自定义Security策略
+ AuthenticationManagerBuilder: 自定义认证策略
+ @EnableWebSecurity: 开启WebSecurity模式 ，@Enablexxxx 开启某个功能

Spring Security的两个主要目标是“认证"和"授权”(访问控制)
“认证”(Authentication)
"授权”(Authorization)
这个概念是通用的，而不是只在Spring Security中存在。

导入依赖

```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
```

![image-20200704170043257](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200704170043257.png)

com.peng.config.SecurityConfig.java 横向添加认证和授权

```java
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

//AOP : 拦截器！
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //链式编程
    //授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //首页所有人可以访问，功能页只对应有权限的人才能访问
        //请求授权的规则
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");

        //没有权限默认跳到登录页面，需要开启登录页面
        // 为什么能进入 /login !
        http.formLogin().loginPage("/toLogin").loginProcessingUrl("/login"); //走定制的登录页

        //防止网站攻击 get post
        http.csrf().disable(); //关闭csrf功能

        //注销，跳回首页
        http.logout().logoutSuccessUrl("/");

        //开启记住我功能 cookie 默认保存两周
        http.rememberMe();
    }

    //认证
    //密码编码：PasswordEncoder
    //在Spring Security 5.0+ 新增了很多的加密方法
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

        //这些数据正常应该从数据库中读取
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("peng").password(new BCryptPasswordEncoder().encode("123")).roles("vip2","vip3")
                .and().withUser("root").password(new BCryptPasswordEncoder().encode("123")).roles("vip1","vip2","vip3")
                .and().withUser("guest").password(new BCryptPasswordEncoder().encode("123")).roles("vip1");

        /*数据库中读取
        auth.jdbcAuthentication()
                .dataSource(datadataSource)
                .withDefaultSchema()
                .withUser(users.username("peng").password("123").roles("vip2"))
                ...........;
                */
    }
}
```

thymeleaf 里控制安全，先导包

```xml
		<!--security-thymeleaf整合包-->
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-springsecurity4</artifactId>
			<version>3.0.4.RELEASE</version>
		</dependency>
```

导入命名空间

```xml
xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4"
```

动态显示 登陆 和 注销

```html
<!--登录注销-->
<div class="right menu">
    <!--如果未登录，显示登录界面-->
    <div sec:authorize="!isAuthenticated()">
        <a class="item" th:href="@{/toLogin}">
            <i class="address card icon"></i> 登录
        </a>
    </div>

    <!--如果登录：用户名，注销-->
    <div sec:authorize="isAuthenticated()">
        <a class="item">
            用户名：<span sec:authentication="name"></span>
        </a>
    </div>
    <div sec:authorize="isAuthenticated()">
        <a class="item" th:href="@{/logout}">
            <i class="sign-out icon"></i> 注销
        </a>
    </div>

</div>
```

动态显示菜单

```html
<div class="column" sec:authorize="hasRole('vip1')">
    <div ............/></div>
<div class="column" sec:authorize="hasRole('vip2')">
	<div ............/></div>
<div class="column" sec:authorize="hasRole('vip3')">
    <div ............/></div>
```

### Shiro

**简介**

● Apache Shiro是一个Java的安全(权限)框架。
● Shiro可以非常容易的开发出足够好的应用，其不仅可以用在JavaSE环境， 也可以用在JavaEE环境。
● Shiro可以完成，认证，授权，加密，会话管理，Web集成，缓存等。
● 下载地址: http://shiro.apache.org/

**功能**

![image-20200705094123626](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200705094123626.png)

**架构**（外部）

![image-20200705094316461](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200705094316461.png)

**架构**（内部）

![image-20200705094402711](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200705094402711.png)

快速开始步骤

1. 导入依赖
2. 配置文件
3. helloworld

resources.log4j.properties

```properties
log4j.rootLogger=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m %n

# General Apache libraries
log4j.logger.org.apache=WARN

# Spring
log4j.logger.org.springframework=WARN

# Default Shiro logging
log4j.logger.org.apache.shiro=INFO

# Disable verbose logging
log4j.logger.org.apache.shiro.util.ThreadContext=WARN
log4j.logger.org.apache.shiro.cache.ehcache.EhCache=WARN
```

resources.shiro.ini

```ini
[users]
# user 'root' with password 'secret' and the 'admin' role
root = secret, admin
# user 'guest' with the password 'guest' and the 'guest' role
guest = guest, guest
# user 'presidentskroob' with password '12345' ("That's the same combination on
# my luggage!!!" ;)), and role 'president'
presidentskroob = 12345, president
# user 'darkhelmet' with password 'ludicrousspeed' and roles 'darklord' and 'schwartz'
darkhelmet = ludicrousspeed, darklord, schwartz
# user 'lonestarr' with password 'vespa' and roles 'goodguy' and 'schwartz'
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# 
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setRoleDefinitions JavaDoc
# -----------------------------------------------------------------------------
[roles]
# 'admin' role has all permissions, indicated by the wildcard '*'
admin = *
# The 'schwartz' role can do anything (*) with any lightsaber:
schwartz = lightsaber:*
# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
# license plate 'eagle5' (instance specific id)
goodguy = winnebago:drive:eagle5
```

官方例子 Quickstart.java

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.ini.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.lang.util.Factory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Quickstart {

    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);

    public static void main(String[] args) {

        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager securityManager = factory.getInstance();
        SecurityUtils.setSecurityManager(securityManager);

        // 获取当前的用户对象
        Subject currentUser = SecurityUtils.getSubject();

        //通过当前用户拿到 Session
        Session session = currentUser.getSession();
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Subject=>session[" + value + "]");
        }

        //判断当前的用户是否被认证
        if (!currentUser.isAuthenticated()) {

            //Token：令牌，没有获取，随机
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true); //设置记住我
            try {
                currentUser.login(token);
            } catch (UnknownAccountException uae) { //用户名错误
                log.info("There is no user with username of " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) { //密码错误
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
            } catch (LockedAccountException lae) { //用户被锁定
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                        "Please contact your administrator to unlock it.");
            }
            // ... catch more exceptions here (maybe custom ones specific to your application?
            catch (AuthenticationException ae) { //认证错误，是以上所以错误的父类
                //unexpected condition?  error?
            }
        }

        //say who they are:
        //print their identifying principal (in this case, a username):
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        //test a role:
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        //粗粒度的
        //test a typed permission (not instance-level)
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        //细粒度
        //a (very powerful) Instance Level permission:
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }

        //注销
        //all done - log out!
        currentUser.logout();

        //结束
        System.exit(0);
    }
}
```

重点的几个函数

![image-20200705134530187](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200705134530187.png)

**Sprintboot中集成Shiro**

### Swagger

### 整合Redis

导入依赖 => 配置连接 => 测试

依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

源码分析：

```java
	@Bean
	//不存在才生效=>我们可以自己定义一个redisTemplate替换这个默认的
	@ConditionalOnMissingBean(name = {"redisTemplate"})
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        //默认的RedisTemplate，没有过多的设置，redis对象都是需要序列化的
        //两个泛型都是Object类型，使用时需要进行强制转换
        RedisTemplate template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean //String是redis最常使用的，所以单独提出来一个bean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
```

配置：

```yaml
# SpringBoot 所有的配置类，都有一个自动配置类:RedisAutoConfiguration
# 自动配置类都会绑定一个 properties:RedisProperties

spring:
  redis:
    host: 127.0.0.1
    port: 6379
# lettuce.xxx SpringBoot2.x使用这个才能生效
# jedis.xxx
```

测试

```java
@SpringBootTest
class RedisSpringbootApplicationTests {

   @Autowired
   private RedisTemplate redisTemplate;

   @Test
   void contextLoads() {
      /*
      //redisTemplate 操作不同的数据类型，api和我们的指令是一样的
      //opsForValue().xxx 操作字符串,类似String
      //opsForList().xxx 操作List,类似List
      //opsForSet()
      //opsForHash()
      //等等各种数据结构
      String s = redisTemplate.opsForValue().toString();

      //获取redis的链接对象
      //除了基本的操作,我们常用的方法都可以直接通过redisTemplate操作,比如事务,和基本的CRUD
      RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
      connection.flushDb();
      connection.flushAll();
      */

      redisTemplate.opsForValue().set("mykey","你好,Redis!");
      System.out.println(redisTemplate.opsForValue().get("mykey"));
   }
}
```

![image-20200706105032685](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706105032685.png)

![image-20200706105117017](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706105117017.png)

```java
	@Test
	public void test() throws JsonProcessingException {
		//开发中一般都使用json来传递对象
		User user = new User("peng", 25);
		//所有的对象都需要序列化
		String jsonUser = new ObjectMapper().writeValueAsString(user);
		redisTemplate.opsForValue().set("user",jsonUser);
		System.out.println(redisTemplate.opsForValue().get("user"));
	}
```

一般在pojo中序列化

```java
public class User implements Serializable{
    private String name;
    private int age;
}
```

我们编写一个自己的redisTemplate，com.peng.config.RedisConfig.java

```java
@Configuration
public class RedisConfig {

    //编写我们自己的 redisTemplate，一个固定的模板
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory){
        //我们我了自己开发方便，一般直接使用<String,Object>类型
        RedisTemplate<String,Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);

        //配置具体的序列化方式，这里是json
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        //String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);

        //hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);

        //value的序列化方式采用json
        template.setValueSerializer(jackson2JsonRedisSerializer);

        //hash的value的序列化方式采用json
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        
        template.afterPropertiesSet();
        return template;
    }
}
```

自己封装的redis工具

```java
@Component
public class RedisUtil {
    @Autowired
    private RedisTemplate<String,Object> redisTemplate;

    //==============Common===============

    //缓存失效时间
    public boolean expire(String key, long time){
        try {
            if (time > 0){
                redisTemplate.expire(key,time, TimeUnit.SECONDS);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    //根据key获取过期时间
    public long getExpire(String key){
        return redisTemplate.getExpire(key,TimeUnit.SECONDS);
    }

    //判断key是否存在
    public boolean hasKey(String key){
        try {
            return redisTemplate.hasKey(key);
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    //删除存缓
    public void del(String... key){
        if (key != null && key.length > 0){
            if (key.length == 1){
                redisTemplate.delete(key[0]);
            }else{
                redisTemplate.delete(Arrays.asList(key));
            }
        }
    }

    //普通缓存获取
    public Object get(String key){
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    //普通存缓放入
    public boolean set(String key,Object value){
        try {
            redisTemplate.opsForValue().set(key,value);
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    //普通存缓放入并设置时间
    public boolean set(String key,Object value,long time){
        try {
            if (time > 0){
                redisTemplate.opsForValue().set(key,value,time,TimeUnit.SECONDS);
            }else {
                set(key, value);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    //递增
    public long incr(String key,long delta){
        if (delta < 0){
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }

    //递减
    public long decr(String key,long delta){
        if (delta < 0){
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    //==============Map===============

    //==============Set===============

}
```

**redis**

NoSQL，键值对存储

**应用**

UserController.java

```java
@GetMapper("/getUser")
@ResponseBody
public Object getUser(){
    if (redisUtil.hasKey("user")){ //如果redis里有就从redis里返回
        return redisUtil.get("user");
    }
    RedisUtil redisUtil = new RedisUtil();
    userJson = redisUtil.get("user");
    return userJson;
}
```







