### 1. Spring

#### 1.1 简介

+ Spring

  春天

+ 历史

  2002，首次推出了Spring框架的雏形

  2004.3.24，首次推出了Spring框架的雏形：interface21框架

  Spring框架即以interface21框架为基础经过重新设计，并不断丰富其内涵，于2004年月24日发布了1.0正式版。

  Rod Johnsoh，Spring Framework创始人。

+ Spring理念：使现有的技术更加容易使用，本身是- -个大杂烩，整合了现有的技术框架!

+ SSH：Struct2 + Spring + Hibernate
+ SSM：SpringMVC + Spring + Mybatis

#### 1.2 优点

+ Spring是一个开源的免费的框架(容器) 
+ Spring是一个轻量级的、非入侵式的框架
+ **控制反转（IOC），面向切面（AOP）**
+ 支持事务的处理，对框架整合的支持
+ 总结：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架

#### 1.3 组成

+ Spring 7大模块

  ![image-20200513104500364](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200513104500364.png)

#### 1.4 Spring 官网

+ 现代化的java开发，基于Spring的开发

  ![image-20200513104634954](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200513104634954.png)

+ SpringBoot

  一个快速开发的脚手架

  基于SpringBoot可以快速开发单个微服务

  约定大于配置

+ SpringCloud

  是基于SpringBoot实现的

+ 现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC！承上启下的作用！

+ Spring弊端

  发展了太久，违背了原来的理念，配置十分繁琐，"配置地狱"

+ Spring官方文档

  https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html

### 2. IOC理论推导

+ Springmvc jar包

  地址：https://mvnrepository.com/artifact/org.springframework/spring-webmvc

  ```xml
      <dependencies>
              <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-webmvc</artifactId>
              <version>5.2.0.RELEASE</version>
          </dependency>
      </dependencies>
  ```

  导这个包的好处：前置的包都自动导入了

1. UserDao 接口

   ```java
   package com.peng.dao;
   
   public interface UserDao {
       void getUser();
   }
   ```

2. UserDaoImpl 实现类

   ```java
   package com.peng.dao;
   
   public class UserDaoImpl implements UserDao{
       public void getUser() {
           System.out.println("默认获取用户的数据");
       }
   }
   ```

3. UserService 业务接口

   ```java
   package com.peng.service;
   
   public interface UserService {
       void getUser();
   }
   ```

4. UserServiceImpl 业务实现类

   ```java
   package com.peng.service;
   
   import com.peng.dao.UserDao;
   import com.peng.dao.UserDaoImpl;
   import com.peng.dao.UserDaoMysqlImpl;
   import com.peng.dao.UserDaoOracleImpl;
   
   public class UserServiceImpl implements UserService{
   
       //private UserDao userDao = new UserDaoImpl();
       //private UserDao userDao = new UserDaoMysqlImpl();
       private UserDao userDao = new UserDaoOracleImpl();
       //用户每次提出新需求都要改代码
   
       //利用set进行动态实现值的注入
       public void setUserDao(UserDao userDao){
           this.userDao = userDao;
       }
   
       public void getUser() {
           userDao.getUser();
       }
   }
   ```

5. MyTest 测试类

   ```java
   import com.peng.dao.UserDaoMysqlImpl;
   import com.peng.service.UserServiceImpl;
   
   public class MyTest {
       public static void main(String[] args) {
   
           //用户实际调用的是业务层，dao层不需要接触
           UserServiceImpl userService = new UserServiceImpl();
   
           userService.getUser();
   
           //用户自己控制，无需改源码
           ((UserServiceImpl) userService).setUserDao(new UserDaoMysqlImpl());
           userService.getUser();
       }
   }
   ```

6. 添加其他的业务 UserDaoMysqlImpl 和 UserDaoOracleImpl

   ```java
   package com.peng.dao;
   
   public class UserDaoMysqlImpl implements UserDao{
       public void getUser(){
           System.out.println("MySQL获取用户的数据");
       }
   }
   ```

   ```java
   package com.peng.dao;
   
   public class UserDaoOracleImpl implements UserDao{
       public void getUser(){
           System.out.println("Oracle获取用户的数据");
       }
   }
   ```

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改原代码！如果程序代码量十分大，修改一次的成本代价十分昂贵!

我们使用一个Set接口实现，已经发生了革命性的变化

```java
private UserDao userDao;

//利用set进行动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

之前，程序是主动创建对象！控制权在程序猿手上！
使用了set注入后，程序不再具有主动性，而是变成了被动的接受对象！

这种思想，从本质上解决了问题，我们程序猿不用再去管理对象的创建了。系统的耦合性大大降低，可以更加专注到业务的实现上。这是**IOC**的原型！

![image-20200514161239533](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514161239533.png)

控制反转**loC(Inversion of Control)**，是一种设计思想，**DI(依赖注入)**是实现IoC的一种方法，也有人认为DI只是IoC的另一-种说法。没有IoC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是:获得依赖对象的方式反转了。

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

**控制反转是一种通过描述(XML或注解)并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是loC容器，其实现方法是依赖注入(Dependency Injection，DI)。**

### 3. HelloSpring

#### 3.1 Hello

+ 防止每次 reimport 都回到1.5，在pom.xml中加上

  ```xml
  <build>
      <finalName>income</finalName>
      <plugins>
          <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.1</version>
              <configuration>
                  <source>1.8</source>
                  <target>1.8</target>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```

+ 官网beans格式

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <!--bean可配置多个-->
      <bean id="..." class="...">  
          <!-- collaborators and configuration for this bean go here -->
      </bean>
  
      <!-- more bean definitions go here -->
  </beans>
  ```

+ Hello程序

  Hello类

  ```java
  package com.peng.pojo;
  
  public class Hello {
  
      private String str;
  
      public String getStr() {
          return str;
      }
  
      public void setStr(String str) {
          this.str = str;
      }
  
      @Override
      public String toString() {
          return "Hello{" + "str='" + str + "\'" + "}";
      }
  }
  ```

  beans.xml 容器，用户在此修改配置，无需程序员修改源码

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <!--使用Spring来创建对象，在Spring这些都被称为bean
      类型 变量名 = new 类名();
      Hello hello = new Hello();
      id = 变量名
      class = new的对象
      property 给对象中的属性设置一个值
      -->
      <bean id="hello" class="com.peng.pojo.Hello">
          <property name="str" value="Spring"></property>
      </bean>
  </beans>
  ```

  MyTest.java 测试类

  ```java
  import com.peng.pojo.Hello;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class MyTest {
      public static void main(String[] args) {
          //获取Spring的上下文对象
          ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
          //我们的对象现在都在Spring中管理，我们要使用，直接去里面取出来就可以！
          Hello hello = (Hello) context.getBean("hello");
          System.out.println(hello.toString());
      }
  }
  ```

#### 3.2 上一节代码修改

+ beans.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="mysqlImpl" class="com.peng.dao.UserDaoMysqlImpl"></bean>
      <bean id="oracleImpl" class="com.peng.dao.UserDaoOracleImpl"></bean>
  
      <bean id="UserServiceImpl" class="com.peng.service.UserServiceImpl">
          <!--
          ref：引用Spring容器中创建好的对象
          value：具体的值，基本数据类型
          -->
          <property name="userDao" ref="mysqlImpl"/>
      </bean>
  </beans>
  ```

+ 测试类

  用容器操作，从此不用再new！

  ```java
  import com.peng.service.UserServiceImpl;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class MyTest {
      public static void main(String[] args) {
          //获取ApplicationContext，拿到Spring的容器，官网固定用法
          ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
  
          //需要什么get什么
          UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");
  
          userServiceImpl.getUser();
      }
  }
  ```

#### 3.3 总结

IOC的理解：原来的对象都是由程序员new出来的，现在我们提供了spring容器，它可以创建出一个对象，并配置这个对象的属性。spring容器的配置文件xml是方便修改的，原来的程序只需被动接受spring提供的对象。一句话说：对象由Spring来创建，管理，装配！

### 4. IOC创建对象的方式

1. 使用无参构造创建，默认

   User.java 类

   ```java
   package com.peng.pojo;
   
   public class User {
       private String name;
   
       public User(){
           System.out.println("User无参构造");
       }
   
       public User(String name) {
           this.name = name;
           System.out.println("User有参构造");
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public void show(){
           System.out.println("name="+name);
       }
   }
   ```

   beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="user" class="com.peng.pojo.User">
           <property name="name" value="peng"/>
       </bean>
   </beans>
   ```

   MyTest.java 测试类

   ```java
   import com.peng.pojo.User;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class MyTest {
       public static void main(String[] args) {
           ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   
           User user = (User)context.getBean("user");
           user.show();
       }
   }
   ```

   输出

   ```
   User无参构造
   name=peng
   ```

2. 有参构造创建对象（三种方式）

   ```xml
   <beans>    
   	<!--有参构造的三种方法-->
   
       <!--1.下标赋值-->
       <bean id="user" class="com.peng.pojo.User">
           <constructor-arg index="0" value="peng"/>
       </bean>
   
       <!--2.通过类型创建不建议使用-->
       <bean id="user" class="com.peng.pojo.User">
           <constructor-arg type="java.lang.String" value="peng"/>
       </bean>
   
       <!--3.直接通过参数名来设置-->
       <bean id="user" class="com.peng.pojo.User">
           <constructor-arg name="name" value="peng"/>
       </bean>
   </beans>
   ```

3. Spring 容器执行的时候，不管测试类中是否 get，所有的类都已经被实例化了，什么时候用什么时候 get。

   ```java
   User user = (User)context.getBean("user");
   User user2 = (User)context.getBean("user");
   System.out.println(user == user2); //输出为true
   ```

   Spring 中只有一个实例！

### 5. Spring配置

#### 5.1 别名

```xml
<alias name="user" alias="userPeng"/>
```

添加别名后，获取对象既可使用原名，也可以使用别名

```java
User user = (User)context.getBean("user");
//User user = (User)context.getBean("userPeng");
```

#### 5.2 Bean的配置

id：bean 的唯一标识符，也就是相当于我们的对象名

class：bean 对象所对应的全限定名：包名+类名

name：也是别名，而且name可以同时取多个（`name="user2,u2;u3"`），alias 只能一一对应

#### 5.3 import

一般用于团队开发使用，它可以将多个配置文件，导入合并为一个。

applicationContext.xml

```xml
<import resource="beans1.xml"/>
<import resource="beans1.xml"/>
<import resource="beans1.xml"/>
```

### 6. 依赖注入

#### 6.1 构造器注入

前面已经说过

#### 6.2 set方式注入※

+ 依赖注入：本质是set注入！

+ 依赖：bean对象的创建依赖于容器

+ 注入：bean对象中的所有属性，由容器来注入

+ 环境搭建

  1. 复杂类型

     ```java
     package com.peng.pojo;
     
     public class Address {
         private String address;
     
         public String getAddress() {
             return address;
         }
     
         public void setAddress(String address) {
             this.address = address;
         }
         
         @Override
         public String toString() {
             return "Address{" +
                     "address='" + address + '\'' +
                     '}';
         }
     }
     ```

  2. 真实测试对象

     ```java
     package com.peng.pojo;
     
     import java.util.*;
     
     public class Student {
         private String name;
         private Address address;
         private String[] book;
         private List<String> hobbys;
         private Map<String,String> card;
         private Set<String> games;
         private String wife;
         private Properties info;
     
         public String getName() {
             return name;
         }
     
         public void setName(String name) {
             this.name = name;
         }
     
         public Address getAddress() {
             return address;
         }
     
         public void setAddress(Address address) {
             this.address = address;
         }
     
         public String[] getBook() {
             return book;
         }
     
         public void setBook(String[] book) {
             this.book = book;
         }
     
         public List<String> getHobbys() {
             return hobbys;
         }
     
         public void setHobbys(List<String> hobbys) {
             this.hobbys = hobbys;
         }
     
         public Map<String, String> getCard() {
             return card;
         }
     
         public void setCard(Map<String, String> card) {
             this.card = card;
         }
     
         public Set<String> getGames() {
             return games;
         }
     
         public void setGames(Set<String> games) {
             this.games = games;
         }
     
         public String getWife() {
             return wife;
         }
     
         public void setWife(String wife) {
             this.wife = wife;
         }
     
         public Properties getInfo() {
             return info;
         }
     
         public void setInfo(Properties info) {
             this.info = info;
         }
     
         @Override
         public String toString() {
             return "Student{" +
                     "name='" + name + '\'' +
                     ", address=" + address.toString() +
                     ", book=" + Arrays.toString(book) +
                     ", hobbys=" + hobbys +
                     ", card=" + card +
                     ", games=" + games +
                     ", wife='" + wife + '\'' +
                     ", info=" + info +
                     '}';
         }
     }
     ```

  3. beans.xml

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
             https://www.springframework.org/schema/beans/spring-beans.xsd">
     
         <bean id="address" class="com.peng.pojo.Address">
             <property name="address" value="葫芦岛"/>
         </bean>
         <bean id="student" class="com.peng.pojo.Student">
             <!--1.普通值注入，value-->
             <property name="name" value="peng"/>
     
             <!--2.其他高级类型注入，bean注入，ref-->
             <property name="address" ref="address"/>
     
             <!--3.数组注入，ref-->
             <property name="book">
                 <array>
                     <value>红楼梦</value>
                     <value>西游记</value>
                     <value>水浒传</value>
                     <value>三国演义</value>
                 </array>
             </property>
     
             <!--4.List-->
             <property name="hobbys">
                 <list>
                     <value>吃饭</value>
                     <value>睡觉</value>
                 </list>
             </property>
     
             <!--5.Map-->
             <property name="card">
                 <map>
                     <entry key="身份证" value="132151313213513553"/>
                     <entry key="银行卡" value="165562612020201234"/>
                 </map>
             </property>
     
             <!--6.Set-->
             <property name="games">
                 <set>
                     <value>LOL</value>
                     <value>CF</value>
                 </set>
             </property>
     
             <!--7.null，空值注入-->
             <property name="wife">
                 <null/>
             </property>
             
             <!--8.Properties-->
             <property name="info">
                 <props>
                     <prop key="学号">20200525</prop>
                     <prop key="性别">男</prop>
                     <prop key="生日">20010604</prop>
                 </props>
             </property>
         </bean>
     </beans>
     ```

  4. 测试类

     ```java
     import com.peng.pojo.Student;
     import org.springframework.context.ApplicationContext;
     import org.springframework.context.support.ClassPathXmlApplicationContext;
     
     public class MyTest {
         public static void main(String[] args) {
             ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
             Student student = (Student) context.getBean("student");
             System.out.println(student.toString());
         }
     }
     ```

  5. 输出

     ```
     Student{name='peng', address=Address{address='葫芦岛'}, book=[红楼梦, 西游记, 水浒传, 三国演义], hobbys=[吃饭, 睡觉], card={身份证=132151313213513553, 银行卡=165562612020201234}, games=[LOL, CF], wife='null', info={学号=20200525, 生日=20010604, 性别=男}}
     ```

#### 6.3 其他方式

+ p标签：`xmlns:p="http://www.springframework.org/schema/p"`
+ c标签：`xmlns:c="http://www.springframework.org/schema/c"`

User.java

```java
package com.peng.pojo;

public class User {
    private String name;
    private int age;

    public User() {
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

userbeans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入，可以直接注入属性的值,property-->
    <bean id="user" class="com.peng.pojo.User" p:name="peng" p:age="18"/>
    <!--c命名空间注入，通过构造器注入，constructor-->
    <bean id="user2" class="com.peng.pojo.User" c:age="18" c:name="peng"/>
</beans>
```

测试类

```java
import com.peng.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        test2();
        test3();
    }

    @Test
    public static void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user",User.class);
        System.out.println(user);
    }

    @Test
    public static void test3(){
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user2",User.class);
        System.out.println(user);
    }
}
```

#### 6.4 bean的作用域

![image-20200515101226291](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515101226291.png)

1. 单例模式 singleton

   ![image-20200515105235228](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515105235228.png)

   ![image-20200515105652658](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515105652658.png)

2. 原型模式 prototype

   每次从容器中get的时候，都会产生一个新对象

   ![image-20200515105716387](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515105716387.png)

3. 其余的 request、session、application，这些只能在web开发中使用到

### 7. Bean的自动装配

+ 自动装配是Spring满足bean依赖一种方式
+ Spring会在上下文中自动寻找，并自动给bean装配属性

在Spring中有三种装配的方式

1. 在xml中显示的配置
2. 在java中显示配置
3. 隐式的自动装配bean ※

#### 7.1 测试

环境搭建：一个人有两个宠物

```java
package com.peng.pojo;

public class Cat {
    public void shout(){
        System.out.println("miao~");
    }
}

public class Dog {
    public void shout(){
        System.out.println("wang~");
    }
}

package com.peng.pojo;

public class People {
    private Cat cat;
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="cat" class="com.peng.pojo.Cat"/>
    <bean id="dog" class="com.peng.pojo.Dog"/>

    <bean id="people" class="com.peng.pojo.People">
        <property name="name" value="peng"/>
        <property name="cat" ref="cat"/>
        <property name="dog" ref="dog"/>
    </bean>
</beans>
```

```java
import com.peng.pojo.People;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    @Test
    public void test1(){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        People people = context.getBean("people",People.class);
        System.out.println(people.toString());
        people.getCat().shout();
        people.getDog().shout();
    }
}
```

#### 7.2 自动装配

1. ```xml
    <!--byName：会自动在容器上下文中查线，和白己对象set方法后面的值对应的beanid !-->
   <bean id="people" class="com.peng.pojo.People" autowire="byName">
   	<property name="name" value="peng"/>
   </bean>
   ```

2. ```xml
   <bean class="com.peng.pojo.Cat"/>
   <bean class="com.peng.pojo.Dog"/>
   
   <!--byType：会自动在容器上下文中查找，和自己对象属性类型相同的bean!-->
   <bean id="people" class="com.peng.pojo.People" autowire="byType">
       <property name="name" value="peng"/>
   </bean>
   ```

小结：

byname的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致!
bytype的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致!

#### 7.3 使用注解实现自动装配

jdk1.5支持的注解，Spring2.5就支持注解了!

官网介绍：The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.

要使用注解须知：

1. 导入约束：context约束

2. **配置注解的支持：<context:annotation-config/>**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```


3. @Autowired 在属性上用：使用Autowired我们可以不用编写Set方法了，前提是你这个自动装配的属性在I0C (Spring) 容器中存在，且符合名字，即 byName！

   ```java
   public class People {
   
       @Autowired
       private Cat cat;
       @Autowired
       private Dog dog;
       private String name;
   
       public Cat getCat() { return cat;}
   
       public Dog getDog() {return dog;}
   
       public String getName() {return name;}
   
       public void setName(String name) {this.name = name;}
   
       @Override
       public String toString() {
           return "People{" +
                   "cat=" + cat +
                   ", dog=" + dog +
                   ", name='" + name + '\'' +
                   '}';
       }
   }
   ```

   @Autowired 在set方法上用

   ```java
   import org.springframework.beans.factory.annotation.Autowired;
   
   public class People {
   
       private Cat cat;
       private Dog dog;
       private String name;
   
       public Cat getCat() {return cat;}
   
       @Autowired
       public void setCat(Cat cat) {
           this.cat = cat;
       }
   
       public Dog getDog() {return dog;}
   
       @Autowired
       public void setDog(Dog dog) {
           this.dog = dog;
       }
   
       public String getName() {return name;}
   
       public void setName(String name) {this.name = name;}
   
       @Override
       public String toString() {
           return "People{" +
                   "cat=" + cat +
                   ", dog=" + dog +
                   ", name='" + name + '\'' +
                   '}';
       }
   }
   ```

   构造器中加注解 @Nullable name可以为空，在初始化时不给name赋值也不会报错

   ```java
   public People(@Nullable String name) {
       this.name = name;
   }
   ```

   如果显示定义了Autowired的required属性为false, 说明这个对象可以为mull，否则不允许为空

   ```java
   @Autowired(required = false)
   private Cat cat;
   ```

   如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候，我们可以使用 @Qualifier(value="xxx") 去配合@Autowired的使用， 指定一个唯一的bean对象注入！

   ```java
   public class People {
       @Autowired
       @Qualifier(value="dog222")
       private Dog dog;
   }
   ```

   @Resource注解

   ```java
   public class People {
       @Resource(name="dog222")
       private Dog dog;
       @Resource
       private Cat cat;
   }
   ```

   @Resource和@ Autowired的区别:

   + 都是用来自动装配的，都可以放在属性字段上
   + @Autowired通过byType的方式实现，而且必须要求这个对象存在！【常用]】
   + @Resource默认通过byName的方式实现，如果找不到名字，则通过byType实现!如果两个都找不到的情况下，就报错! 【常用】
   + 执行顺序不同：@Autowired：类型、名字；@Resource：名字、类型

### 8. Spring注解开发

在Spring4之后，要使用注解开发，必须要保证aop的包导入了

![image-20200515121523180](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515121523180.png)

使用注解需要导入context约束，增加注解的支持!

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--指定要扫描的包，这个包下的注解就会生效 ※ -->
    <context:component-scan base-package="com.peng.pojo"/>
    <context:annotation-config/>
</beans>
```

1. bean

   ```java
   //等价于 <bean id="user" class="com.peng.dao.User"/>
   //@Component组件,放在类上，说明这个类被Spring管理了
   @Component
   public class User {
       public String name = "peng";
   }
   ```

2. 属性如何注入

   ```java
   @Component
   public class User {
       //相当于 <property name="name" value="peng"/>
       @Value("peng")
       public String name;
   }
   ```

   也可以放到set上

   ```java
   @Component
   public class User {
       public String name;
       
       @Value("peng")
       public void setName(String name) {
           this.name = name;
       }
   }
   ```

3. 衍生的注解

   @Component 有几个衍生注解，我们在web开发中，会按照mvc三层架构分层！

   - dao【@Repository】

     ```java
     import org.springframework.stereotype.Repository;
     
     @Repository
     public class UserDao {
     }
     ```

   - service【@Service】

   - controller【@Controller】

   这四个注解功能都是一样的，都是代表将某个类注册到Spring中，装配Bean

4. 自动装配置

   @Autowired、@Nullable、@Resource，前面介绍过

5. 作用域

   @Scope("singleton") / @Scope("prototype")

6. 小结 **xml 与 注解**

   + xml更加万能，适用于任何场合，维护简单方便
   + 注解不是自己类使用不了，维护相对复杂

   xml与注解最佳实践：

   + xml用来管理bean
   + 注解只负责完成属性的注入
   + 我们在使用的过程中，只需要注意一个问题：必须让注解生效，就需要开启注解的支持

### 9. JavaConfig 配置

我们现在要完全不使用Spring的xml配置了，全权交给lava来做

JavaConfig 是 Spring 的一个子项目，在 Spring 4 之后，它成为了一个核心功能！

User.java 实体类

```java
package com.peng.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

//这里这个注解的意思，就是说明这个类被Spring婷停了注册到了齐器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("peng") //属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

MyConfig.java 配置类

```java
package com.peng.config;

import com.peng.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

//这个也会Spring容器托管，注册到容器中。因为他本来就是一个@Component
@Configuration //@Configuration代表这是一个配置类，跟beans.xml一样
@ComponentScan("com.peng")
@Import(MyConfig2.class) //合并其他beans配置
public class MyConfig {

    //注册一个bean，就相当于我们之前写的bean标签
    //方法名：bean标签中的id
    //返回值：bean标签中的class
    @Bean
    public User getUser(){
        return new User(); //就是返回要注入bean的对象!
    }
}
```

MyTest.java 测试类

```java
import com.peng.config.MyConfig;
import com.peng.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        //如果完全使用了配置类方式去做，我们就只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载!
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User user = context.getBean("user",User.class);
        System.out.println(user.getName());
    }
}
```

这种纯Java的配置方式，在SpringBoot中随处可见！

### 10. 代理模式

为什么要学习代理模式？因为这就是SpringAOP的底层！【SpringAOP和SpringMVC】

代理模式的分类：

+ 静态代理
+ 动态代理

![image-20200515135115318](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515135115318.png)

#### 10.1 静态代理

角色分析：

+ 抽象角色：一般会使用接口或者抽象类来解决

  ```java
  package com.peng.demo01;
  
  //租房
  public interface Rent {
      public void rent();
  }
  ```

+ 真实角色：被代理的角色

  ```java
  package com.peng.demo01;
  
  //房东
  public class Host implements Rent{
      @Override
      public void rent() {
          System.out.println("房东要出租房子！");
      }
  }
  ```

+ 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作

  ```java
  package com.peng.demo01;
  
  public class Proxy implements Rent {
      private Host host;
  
      public Proxy() {
      }
  
      public Proxy(Host host) {
          this.host = host;
      }
  
      @Override
      public void rent() {
          seeHouse();
          host.rent();
          sign();
          fare();
      }
  
      //看房
      public void seeHouse(){
          System.out.println("中介带你看房");
      }
  
      //签合同
      public void sign(){
          System.out.println("签租赁合同");
      }
  
      //收中介费
      public void fare(){
          System.out.println("收中介费");
      }
  }
  ```

+ 客户端：访问代理对象的人！

  ```java
  package com.peng.demo01;
  
  public class Client {
      public static void main(String[] args) {
          //房东要租房子
          Host host = new Host();
          //代理，中介帮房东租房子，但是呢?代理一般会有一些附属操作!
          Proxy proxy = new Proxy(host);
          //你不用面对房东，直接找中介租房即可
          proxy.rent();
      }
  }
  ```

代理模式的好处：

+ 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
+ 公共也就就交给代理角色!实现了业务的分工
+ 公共业务发生扩展的时候，方便集中管理

缺点：一个真实角色就会产生一个代理角色，代码量会翻倍，开发效率会变低

#### 10.2 加深理解

![image-20200515142148468](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515142148468.png)

对修改关闭，不修改源码，外边套一层

#### 10.3 动态代理

+ 动态代理和静态代理角色一样
+ 动态代理的代理类是动态生成的，不是我们直接写好的
+ 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  + 基于接口：JDK动态代理【我们在这里使用】
  + 基于类：cglib
  + java字节码实现：javasist

需要了解两个类：Proxy 代理，InvocationHandler 调用处理程序

Rent.java 租房接口

```java
package com.peng.demo03;

//租房
public interface Rent {
    public void rent();
}
```

Host.java 房东实体类

```java
package com.peng.demo03;

//房东
public class Host implements Rent {
    @Override
    public void rent() {
        System.out.println("房东要出租房子！");
    }
}
```

ProxyInvocationHandler.java 动态代理类

```java
package com.peng.demo03;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//用这个类自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler{

    //被代理的接口
    private Rent rent;

    public void setRent(Rent rent) {
        this.rent = rent;
    }

    //生成得到代理类
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),rent.getClass().getInterfaces(),this);
    }

    //处理代理实力，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        //动态代理的本质，就是使用反射机制实现
        seeHouse();
        Object result = method.invoke(rent, args);
        fare();
        return result;
    }

    public void seeHouse(){
        System.out.println("中介带看房子");
    }

    public void fare(){
        System.out.println("收中介费");
    }
}
```

Client.java 客户端

```java
package com.peng.demo03;

public class Client {
    public static void main(String[] args) {
        //真实角色
        Host host = new Host();

        //代理角色：现在没有
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        //通过调用程序处理角色来处理我们要调用的按口对象!
        pih.setRent(host);

        Rent proxy = (Rent) pih.getProxy(); //这里的proxy就是动态生成的，我们并没有写
        proxy.rent();
    }
}
```

动态代理的好处：

+ 可以使真实角色的操作更加纯粹!不用去关注一些公共的业务
+ 公共也就就交给代理角色!实现了业务的分工
+ 公共业务发生扩展的时候，方便集中管理
+ 一个动态代理类代理的是一个**接口**， 一般就是对应的**一类**业务
+ 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

通式

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyInvocationHandler implements InvocationHandler{

    //被代理的接口
    private Object target;

    public void setRent(Object target) {
        this.target = target;
    }

    //生成得到代理类
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }

    //处理代理实力，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //动态代理的本质，就是使用反射机制实现
        log(method.getName()); //利用反射写活了
        Object result = method.invoke(target, args);
        return result;
    }

    public void log(String msg){
        System.out.println("执行了"+msg+"方法");
    }
}
```

### 11. AOP

#### 11.1 什么是AOP

AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是0OP的延续，是软件开发中的-个热点，也是Spring框架中的一个重要内容，是函数式编程的一-种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![image-20200515181245858](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515181245858.png)

#### 11.2 AOP在Spring中的作用

提供声明式事务；允许用户自定义切面

+ 橫切关注点:跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的,但是我们需要关注的部分，就是横切关注点。如日志，安全，缓存，事务等等....
+ 切面(ASPECT)：横切关注点被模块化的特殊对象。即，它是一-个类。
+ 通知(Advice)：切面必须要完成的工作。即，它是类中的一一个方法。
+ 目标(Target)：被通知对象。
+ 代理(Proxy)：向目标对象应用通知之后创建的对象。
+ 切入点(PointCut)：切面通知执行的“地点"的定义。
+ 连接点(JointPoint)：与切入点匹配的执行点。

![image-20200515181817911](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515181817911.png)

SpringAOP中，通过Advice定 义横切逻辑，Spring中支持5种类型的Advice：

![image-20200515181923759](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200515181923759.png)

即AOP在不改变原有代码的情况下，去增加新的功能

#### 11.3 使用Spring实现AOP

【重点】使用AOP织入，需要导入一个依赖包

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver-->
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

**方式一：使用Spring的API接口**【主要SpringAPI接口实现】

UserService.java 接口

```java
package com.peng.service;

public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void select();
}
```

UserServiceImpl.java 实现类

```java
package com.peng.service;

public class UserServiceImpl implements UserService{
    @Override
    public void add() {
        System.out.println("添加了一个用户");
    }

    @Override
    public void delete() {
        System.out.println("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.println("更新了一个用户");
    }

    @Override
    public void select() {
        System.out.println("查询了一个用户");
    }
}
```

Log.java 要添加的日志功能

```java
package com.peng.log;

import org.springframework.aop.MethodBeforeAdvice;
import org.springframework.lang.Nullable;

import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice{

    //method：要执行的目标对象的方法
    //args：参数
    //target：目标对象
    @Override
    public void before(Method method, Object[] args, @Nullable Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
    }
}
```

AfterLog.java 也是要添加的日志功能

```java
package com.peng.log;

import org.springframework.aop.AfterReturningAdvice;
import org.springframework.lang.Nullable;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {

    //returnValue 返回值
    @Override
    public void afterReturning(@Nullable Object returnValue, Method method, Object[] args, @Nullable Object target) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法，返回结果为"+returnValue);
    }
}
```

applicationContext.xml Spring配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd 
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.peng.service.UserServiceImpl"/>
    <bean id="log" class="com.peng.log.Log"/>
    <bean id="afterlog" class="com.peng.log.AfterLog"/>

    <!--方式一：使用原生Spring API接口-->
    <!--配置aop，需要导入aop的约束-->
    <aop:config>
        <!--切入点:expression:表达式，execution(要执行的位置! * * * * *)-->
        <aop:pointcut id="pointcut" expression="execution(* com.peng.service.UserServiceImpl.*(..))"/>
        <!--执行环绕增加-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterlog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

MyTest.java 测试类

```java
import com.peng.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //动态代理，代理的是接口 ※
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
        userService.delete();
        userService.update();
        userService.select();
    }
}
```

输出：

```
com.peng.service.UserServiceImpl的add被执行了
添加了一个用户
执行了add方法，返回结果为null
com.peng.service.UserServiceImpl的delete被执行了
删除了一个用户
执行了delete方法，返回结果为null
com.peng.service.UserServiceImpl的update被执行了
更新了一个用户
执行了update方法，返回结果为null
com.peng.service.UserServiceImpl的select被执行了
查询了一个用户
执行了select方法，返回结果为null
```

**方式二：自定义来实现AOP**【主要是切面定义】

DiyPointCut.java 自定义类

```java
package com.peng.diy;

public class DiyPointCut {
    public void before(){
        System.out.println("=====方法执行前=====");
    }
    public void after(){
        System.out.println("=====方法执行后=====");
    }
}
```

applicationContext.xml Spring配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.peng.service.UserServiceImpl"/>

    <!--方式二：自定义类-->
    <bean id="diy" class="com.peng.diy.DiyPointCut"/>
    <aop:config>
        <!--自定义切面，ref要引用的类-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.peng.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>

</beans>
```

测试类和其他类不变

**方式三：使用注解实现AOP**

AnnoPointCut.java 切面类

```java
package com.peng.diy;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

//方式三：使用注解实现AOP
@Aspect //标注这个类是切面
public class AnnoPointCut {

    @Before("execution(* com.peng.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("=====方法执行前=====");
    }

    @After("execution(* com.peng.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("=====方法执行后=====");
    }

    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.peng.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        Signature signature = jp.getSignature();// 获得签名（类的信息）
        System.out.println(signature);

        //执行方法
        Object proceed = jp.proceed();

        System.out.println("环绕后");
    }
}
```

applicationContext.xml Spring配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.peng.service.UserServiceImpl"/>

    <!--方式三-->
    <bean id="annoPointCut" class="com.peng.diy.AnnoPointCut"/>
    <!--开启注解支持!  JDK(默认)(false)  cglib(true)-->
    <aop:aspectj-autoproxy proxy-target-class="false"/>

</beans>
```

测试类和其他类不变

输出：

```
环绕前
void com.peng.service.UserService.select()
=====方法执行前=====
查询了一个用户
环绕后
=====方法执行后=====
```
