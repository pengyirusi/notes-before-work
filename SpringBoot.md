### 微服务阶段

javase：OOP

mysql：持久化

html+css+js+jquery+框架：视图

javaweb：独立开发MVC三层架构的网站，原始

ssm框架：简化了我们的开发流程，配置开始复杂

war：tomcat运行

spring再简化：SpringBoot - jar 内嵌tomcat，微服务架构

服务越来越多：SpringCloud

![image-20200516130808239](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516130808239.png)

约定大于配置：maven，spring，springmvc，springboot，docker，...

高内聚，低耦合！

![image-20200516134759173](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516134759173.png)

![image-20200516135441248](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516135441248.png)

### 第一个SpringBoot程序

+ jdk 1.8
+ maven 3.6.1
+ springboot 最新版
+ IDEA

官方：提供了一个快速生成的网站 https://start.spring.io/ ，IDEA继承了这个网站！

+ 可以在官网直接下载后，导入idea开发（官网在哪）

  ![image-20200516141454867](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516141454867.png)

整体结构

![image-20200516160025455](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160025455.png)

HelloworldApplication.java

```java
package com.peng.helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

//程序的主入口
@SpringBootApplication
public class HelloworldApplication {

	public static void main(String[] args) {
		//SpringApplication
		SpringApplication.run(HelloworldApplication.class, args);
	}
}
```

application.properties 空的配置文件

```xml
# springboot 核心配置文件
```

HelloworldApplicationTests.java 测试类

```java
package com.peng.helloworld;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

//单元测试
@SpringBootTest
class HelloworldApplicationTests {
	@Test
	void contextLoads() {
	}
}
```

HelloController.java 

```java
package com.peng.helloworld.controler;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    //接口：http://localhost:8080/hello
    @RequestMapping("/hello")
    public String hello(){
        //调用业务，接受前端的参数
        return "hello,world";
    }
}
```

直接执行

![image-20200516160346827](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160346827.png)

发现自带一个error接口

![image-20200516160431841](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160431841.png)

双击package打包服务

![image-20200516160531491](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160531491.png)

生成可执行的jar文件

![image-20200516160611520](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160611520.png)

在cmd中运行

![image-20200516160704731](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160704731.png)

在浏览器中还能运行，微服务思想，前后端分离

![image-20200516160808016](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516160808016.png)

+ 直接使用idea创建一个springboot项目（一般开发直接在IDEA中创建）

### 原理初探

**pom.xml**

+ spring-boot-dependencies：核心依赖在父工程中
+ 我们在写或者引入一些Springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

**启动器**

+ 通式

  ```xml
  <dependency>
  	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-xxx</artifactId>
  </dependency>
  ```

+ 启动器：说白了就是springboot的启动场景

+ 比如，spring-boot-starter-web，他就会帮我们自动导入web环境所有的依赖

+ springboot会将所有的功能场景，都变成一个个的启动器

+ 我们要使用什么功能，就只需要找到对应的启动器就可以了`starter`

**主程序**

```java
@SpringBootApplication //标注这个类是一个springboot的应用
public class HelloworldApplication {

	public static void main(String[] args) {
		//将springboot应用启动
		SpringApplication.run(HelloworldApplication.class, args);
	}
}
```

+ 注解

```java
@SpringBootConfiguration：springboot的配置
	@Configuration：spring配置类
	@Component：说明这也是一个spring的组件
@EnableAutoConfiguration：自动配置
	@AutoConfigurationPackage：自动配置包
		@Import({Registrar.class})：导入选择器，包注册
	@Import({AutoConfigurationImportSelector.class})：自动配置导入选择
List configurations = this.getCandidateConfigurations(annotationMetadata, attributes); //获取所有的配置
```

获取候选的配置

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
	List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader());
	Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
	return configurations;
}
```

META-INF/spring.factories：自动配置的核心文件

![image-20200516181039013](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200516181039013.png)

```java
Properties properties = PropertiesLoaderUtils.loadProperties(resource);
//所有资源加载到配置类中!
```

结论：springboot所有自动配置都是在启动的时候扫描并加载：`spring.factories`所有的自动配置类都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的start，就有对应的启动器了，有了启动器，我们自动装配就会生效，然后就配置成功！

1. springboot在启动的时候，从类路径下 /MEAT-INF/`spring.factories`获取指定的值
2. 将这些自动配置的类导入容器，自动配置就会生效，帮我进行自动配置
3. 以前我们需要自动配置的东西，现在 springboot 帮我们做了
4. 整个javaEE，解决方案和自动配置的东西都在 spring-boot-autoconfigure-2.2.0.RELEASE.jar 这个包下
5. 它会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加到容器
6. 容器中也会存在非常多的 xxxAutoConfiguration 的文件(@Bean),就是这些类给容器中导入了这个场景需要的所有组件；并自动配置，@Configuration，JavaConfig！
7. 有了自动配置类，免去了我们手动编写配置注入功能组件等的工作

SpringApplication这个类(加了@SpringApplication注解的主类)主要做了以下四件事情：

1. 推断应用的类型是普通的项目还是Web项目
2. 查找并加载所有可用初始化器， 设置到 initializers 属性中
3. 找出所有的应用程序监听器，设置到 listeners 属性中
4. 推断并设置main方法的定义类，找到运行的主类

### springboot配置

#### 配置文件

SpringBoot使用一个全局的配置文件，配置文件名称是固定的

+ application.properties
  语法结构：`key=value`
+ application.yaml
  。语法结构：`key: 空格value`

配置文件的作用：修改SpringBoot自动配置的默认值，因为SpringBoot在底层都给我们自动配置好了

```yaml
#springboot配置文件都能配置什么东西呢？

#官方文档：https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/htmlsingle/#boot-features-external-config
#Part X. Appendices Appendix A. Common application properties

#官方的配置太多了，了解原理

##基本语法，对空格缩进要求很高

#普通的key-value
name: peng

#存一个对象
student:
  name: peng
  age: 18

#行内写法
student: {name: peng,age: 18}

#数组
pets:
  - cat
  - dog

pet: [cat,dog,pig]
```

yaml 可以直接给实体类赋值

#### 解决测试单元不能运行

+ 问题：测试单元的 @Test 前面没有运行图标

+ 解决

  1. IDEA内：File - Setting - Plugins：搜到JUnitGenerator2.0，安装，重启IDEA

  2. 光标点击到主类上，Alt+Enter，选择 Create Test

     ![image-20200517125846866](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517125846866.png)

  3. 在类上写两个注解：

     ```java
     @RunWith(SpringJUnit4ClassRunner.class)
     @SpringBootTest
     ```

  4. 在类中写的方法上加上 @Test 注解，运行标志出现，可以运行

     ```java
     @RunWith(SpringJUnit4ClassRunner.class)
     @SpringBootTest
     public class Spring02ConfigApplicationTest {
     
         @Autowired //将之前的value自动注入
         private Dog dog;
     
         @Test
         public void a(){
             System.out.println(dog);
         }
     }
     ```

     ![image-20200517130329798](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517130329798.png)

  5. 输出

     ![image-20200517130444489](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517130444489.png)

  

#### yaml配置类的属性

application.yaml 配置

```yaml
person:
  name: peng${random.uuid}
  age: 18
  happy: false
  birth: 2001/06/03
  maps: {k1: v1,k2: v2}
  lists: [eat,sleep,girl]
  dog:
    name: ${person.name:没有主人}的狗 #冒号后面的是默认值
    age: ${random.int(1,5)}
```

Person.java Person类

```java
package com.peng.pojo;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "person") //绑定yanl，让yaml生效
public class Person {
    private String name;
    private Integer age;
    private boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    public Person() {
    }

    public Person(String name, Integer age, boolean happy, Date birth, Map<String, Object> maps, List<Object> lists, Dog dog) {
        this.name = name;
        this.age = age;
        this.happy = happy;
        this.birth = birth;
        this.maps = maps;
        this.lists = lists;
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public boolean isHappy() {
        return happy;
    }

    public void setHappy(boolean happy) {
        this.happy = happy;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", happy=" + happy +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }
}
```

Spring02ConfigApplicationTest.java 测试类

```java
package com.peng;

import com.peng.pojo.Person;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class Spring02ConfigApplicationTest {

    @Autowired
    private Person person;

    @Test
    public void contextLoads() {
        System.out.println(person);
    }
}
```

输出：

```
Person{name='peng24756efe-3e05-4d61-abe7-f31e59e94f6f', age=18, happy=false, birth=Sun Jun 03 00:00:00 CST 2001, maps={k1=v1, k2=v2}, lists=[eat, sleep, girl], dog=Dog{name='peng92a5f5f2-3149-4336-9c45-76da5e680285的狗', age=3}}
```

![image-20200517134624302](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517134624302.png)

+ 松散绑定

  lastName，在yaml中可以写成last-name

+ JSR303校验：赋值前验证数据类型

  ```java
  @Validated //数据校验
  public class Person {
      @NotNull //传入的值必须不为空才能通过验证
      private String name;
  }
  ```

   **Bean Validation 中内置的 constraint**

  ![img](https://upload-images.jianshu.io/upload_images/3145530-8ae74d19e6c65b4c?imageMogr2/auto-orient/strip|imageView2/2/w/654/format/webp)

  **Hibernate Validator 附加的 constraint**

  ![img](https://upload-images.jianshu.io/upload_images/3145530-10035c6af8e90a7c?imageMogr2/auto-orient/strip|imageView2/2/w/432/format/webp)

#### 多环境配置

![image-20200517145339645](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517145339645.png)

```yaml
#springboot的多环境配置:可以选择激活哪一个配置文件
server:
  port: 8080
spring:
  profiles:
    active: dev

# --- 分文档模块
---
server:
  port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: test
```

#### 浅显的底层原理

```yaml
# 配置文件里到底能写什么 --联系-- spring.factories
# 在我们这配置文件中能配置的东西，都存在一个固有的规律
# xxxAutoConfiguration --连接-- xxxProperties --连接-- yaml
# xxxAutoConfiguration:自动装配的默认值
# xxxProperties有set方法改变默认值，和配置文件yaml绑定，设置新值并使用
server:
  port: 8080
```

ctrl+鼠标左键，点进 port 看源码，果然有 port 属性和 set 方法

```java
private Integer port;

public void setPort(Integer port) {
    this.port = port;
}
```

发现这个方法属于 ServerProperties 类，类上有注解 @ConfigurationProperties，他连接 server 的默认配置文件

```java
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
public class ServerProperties{...}
```

自动默认配置文件中果然有

![image-20200517153640572](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517153640572.png)

![image-20200517153954800](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517153954800.png)

点进去之后，确实是个自动配置类，在注解中，他允许了 ServerProperties 类重新设置 port 等属性，这些设置可通过配置文件 yaml 配置

![image-20200517154114131](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517154114131.png)

而 ServerProperties 类连接 yaml，就是通过之前说过的 @ConfigurationProperties 注解，点进 prefix，是一个配置属性的注解

```java
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
public class ServerProperties {
```

本质：我们原先需要在 bean 中配置的属性（properties）封装成一个类然后通过 yaml 文件进行自动注入，而我们可以在 application.yaml 文件中自定义这些 property 属性

+ 自动装配原理总结

  1. SpringBoot 启动会加载大量的自动配置类

  2. 我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中

  3. 我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

  4. 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可

     xxxAutoConfigurartion：自动配置类；给容器中添加组件

     xxxProperties：封装配置文件中相关属性；

+ 通过 debug=true 来查看，哪些配置生效，哪些没有

  ```yaml
  debug: true
  ```

### SpringBoot Web开发

jar：webapp！

自动装配：创建应用，选择模块

springboot到底帮我们配置了什么？我们能不能进行修改？能修改哪些东西？能不能扩展？

+ xxxAutoConfiguration.. 向容器中自动配置组件
+ xxxProperties：自动配置类，装配配置文件中自定义的一些内容

要解决的问题

+ 导入静态资源 html css
+ 首页
+ jsp，模板引擎 Thymeleaf
+ 装备扩展 SpringMVC
+ 增删改查
+ 拦截器
+ 国际化

#### 静态资源

WebMvcAutoConfiguration.java

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
	if (!this.resourceProperties.isAddMappings()) {
		logger.debug("Default resource handling disabled");
		return;
	}
	Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
	CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
	if (!registry.hasMappingForPattern("/webjars/**")) {
		customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
.addResourceLocations("classpath:/META-INF/resources/webjars/").setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
	}
	String staticPathPattern = this.mvcProperties.getStaticPathPattern();
	if (!registry.hasMappingForPattern(staticPathPattern)) {
		customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern).addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
	}
}
```

+ 第一种方法：添加webjars依赖拿到静态资源

```xml
<dependency>
	<groupId>org.webjars</groupId>
	<artifactId>jquery</artifactId>
	<version>3.4.1</version>
</dependency>
```

![image-20200517192918569](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517192918569.png)

![image-20200517192738483](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517192738483.png)

+ 第二种方法

ResourceProperties.java 类定义了几个资源地址的静态全局变量

![image-20200517193940927](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517193940927.png)

按照源码新建文件夹，在该文件下的所有目录都直接能被取到

![image-20200517194319635](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517194319635.png)

![image-20200517194542894](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517194542894.png)

+ 总结

1. 在springboot，我们可以使用以下方式处理静态资源
   + webjars： `localhost:8080/webjars/`
   + public，static，resource，/**，这些都在 `localhost:8080/` 下
2. 优先级：resource>static(默认的)>public

#### 首页定制

![image-20200517221029586](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517221029586.png)

看源码，意思是在任意资源目录添加 index.html 文件，我这里添加到了 public 文件夹下

![image-20200517222015016](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517222015016.png)

启动后

![image-20200517221554410](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517221554410.png)

经测试，index.html 文件放到三个子目录下都能生成主页，直接放到上级的 resources 里不生效，一般主页都放到 static 文件夹下

#### 模板引擎 Thymeleaf

导入依赖

```xml
<!--thymeleaf 以后都是基于3.x开发-->
<dependency>
	<groupId>org.thymeleaf</groupId>
	<artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
	<groupId>org.thymeleaf.extras</groupId>
	<artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```

源码

```java
public class ThymeleafProperties {
   private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;
   public static final String DEFAULT_PREFIX = "classpath:/templates/";
   public static final String DEFAULT_SUFFIX = ".html";
   ...
}
```

从源码看出，Thymeleaf 能取到 templates 目录下的 .html 文件

![image-20200517225641127](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517225641127.png)

![image-20200517225655051](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517225655051.png)

结论：只要需要使用 thymeleaf，只需要导入对应的依赖就可以了！我们将html放在我们的 templates 目录下即可！

```html
<!DOCTYPE html>
<!--特定的声明-->
<html lang="en" xmlns:th="http://www.thymeleaf.org"/>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>test</h1>

<!--所有的htmL元素都可以被thymeleaf 替换接管 th:元素名-->
<div th:text="${msg}"></div>

</body>
</html>
```

```java
@Controller
public class IndexController {

    @RequestMapping("/test")
    public String test(Model model){
        model.addAttribute("msg","hello,springboot");
        return "test";
    }
}
```

![image-20200517231314573](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200517231314573.png)

+ thymeleaf语法

  ![image-20200518104722147](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518104722147.png)

  测试

  ```xml
  <!DOCTYPE html>
  <!--特定的声明-->
  <html lang="en" xmlns:th="http://www.thymeleaf.org"/>
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
  <h1>test</h1>
  
  <!--所有的htmL元素都可以被thymeleaf 替换接管 th:元素名-->
  <div th:text="${msg}"></div>
  <!--utext表示转义-->
  <div th:utext="${msg}"></div>
  
  <hr>
  
  <!--遍历取值-->
  <h3 th:each="user:${users}" th:text="${user}"></h3>
  <h3 th:each="user:${users}">[[${user}]]</h3><!--不建议使用-->
  
  </body>
  </html>
  ```

  ```java
  @Controller
  public class IndexController {
  
      @RequestMapping("/test")
      public String test(Model model){
          model.addAttribute("msg","<h1>hello,springboot</h1>");
          model.addAttribute("users", Arrays.asList("peng1","peng2"));
          return "test";
      }
  }
  ```

  结果

  ![image-20200518113940332](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113940332.png)

  更多语法

  ![image-20200518113334206](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113334206.png)

  ![image-20200518113422704](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113422704.png)

  ![image-20200518113447344](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113447344.png)

  ![image-20200518113500019](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113500019.png)

  ![image-20200518113518978](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113518978.png)

  ![image-20200518113529384](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113529384.png)

  ![image-20200518113549320](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518113549320.png)

### SpringMVC自动配置

自己扩展的mvc配置类 com.peng.config/WebMvcConfigurer.java

```java
//如果,你想diy一些定制化的功能，只要写这个组件，
//然后将它交给springboot, springboot会帮我们自动装配!
//扩展  springmvc       dispatchservlet
@Configuration
public class MyConfig implements WebMvcConfigurer {

    //ViewResolver实现了视图解析器按口的类,我们就可以把它看做视图解析器
    @Bean
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }

    //自定义了一个自己的视图解析器
    public static class MyViewResolver implements ViewResolver{
        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }
    }
}
```

在 springboot 中， 有非常多的xxxx Configuration帮助我们进行扩展配置，只要看见了这个东西，我们就要注意了！

一个例子：

```java
	//视图跳转
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/peng").setViewName("test");
    }
```

### 员工管理系统

#### 准备工作

将资源放到对应的文件夹

![image-20200518133207708](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518133207708.png)

java 模拟数据库

![image-20200518133312520](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518133312520.png)

Department.java 部门实体类

```java
//部门表
@Data
@NoArgsConstructor //无参构造器
@AllArgsConstructor //有参构造器
public class Department {

    private Integer id;
    private String departmentName;
}
```

Employee.java 员工实体类

```java
//员工表
@Data
@NoArgsConstructor
public class Employee {

    private Integer id;
    private String lastName;
    private String email;
    private Integer gender; //0:女 1:难
    private Department department;
    private Date birth;

    public Employee(Integer id, String lastName, String email, Integer gender, Department department) {
        this.id = id;
        this.lastName = lastName;
        this.email = email;
        this.gender = gender;
        this.department = department;
        //默认的创建日期！
        this.birth = new Date();
    }
}
```

DepartmentDao.java

```java
//部门dao
@Repository
public class DepartmentDao {

    //模拟数据库中的数据
    private static Map<Integer,Department> departments = null;
    static {
        //创建部门表
        departments = new HashMap<Integer,Department>();

        departments.put(101,new Department(101,"教学部"));
        departments.put(102,new Department(102,"市场部"));
        departments.put(103,new Department(103,"教研部"));
        departments.put(104,new Department(104,"运营部"));
        departments.put(105,new Department(105,"后勤部"));
    }

    //获得所有部门信息
    public Collection<Department> getDepartments(){
        return departments.values();
    }

    //通过id得到部门
    public Department getDepartmentById(Integer id){
        return departments.get(id);
    }
}
```

EmployeeDao.java

```java
//员工dao
@Repository
public class EmployeeDao {
    //模拟数据库中的数据
    private static Map<Integer,Employee> employees = null;
    //员工有所属的部门
    @Autowired
    private DepartmentDao departmentDao;
    static {
        //创建部门表
        employees = new HashMap<Integer,Employee>();

        employees.put(101,new Employee(1001,"AA","A6131313@qq.com",1,new Department(101,"教学部")));
        employees.put(102,new Employee(1002,"BB","B6131313@qq.com",0,new Department(102,"市场部")));
        employees.put(103,new Employee(1003,"CC","C6131313@qq.com",1,new Department(103,"教研部")));
        employees.put(104,new Employee(1004,"DD","D4234524@qq.com",0,new Department(104,"运营部")));
        employees.put(105,new Employee(1005,"EE","E6131313@qq.com",1,new Department(105,"后勤部")));
    }

    //===增删改查===

    //主键自增
    private static Integer initId = 1006;
    //增加一个员工
    public void add(Employee employee){
        if (employee.getId()==null){
            employee.setId(initId++);
        }

        employee.setDepartment(departmentDao.getDepartmentById(employee.getDepartment().getId()));

        employees.put(employee.getId(),employee);
    }

    //查询全部员工信息
    public Collection<Employee> getAll(){
        return employees.values();
    }

    //通过id查询员工
    public Employee getEmployeeById(Integer id){
        return employees.get(id);
    }

    //通过id删除员工
    public void delete(Integer id){
        employees.remove(id);
    }
}
```

#### 首页实现

注意点：

1. 所有页面的静态资源都需要使用 `thymeleaf `接管
2. url：`@{}`

com.peng.config/MyMvcConfig.java 配置主页

```java
@Configuration
public class MyConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
    }
}
```

#### 页面国际化

注意点：

1. 我们需要配置 i18n 文件
2. 我们如果需要在项目中进行按钮自动切换，我们需要自定义一个组件 `LocaleResolver`
3. 记得将自己写的组件配置到spring容器 `@Bean`
4. `#{}`

![image-20200518140539795](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518140539795.png)

利用 bundle 可视化可同时配置几个文件，并在 application.yaml 配置

```yaml
#我们的配置文件放在的真实位置
spring:
  messages:
    basename: i18n.login
```

首页按钮链接改成 thymeleaf 的语法

```html
<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
```

MyLocaleResolver.java 自己写的地区解析器

```java
public class MyLocaleResolver implements LocaleResolver {

    //解析请求
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {

        //获取请求中的语言参数链接
        String language = httpServletRequest.getParameter("l");
        Locale locale = Locale.getDefault(); //如果没有就使用默认的

        //如果请求的链接携带了国际化的参数
        if (! StringUtils.isEmpty(language)){
            //zh_CN
            String[] split = language.split("_");
            //国家,地区
            locale = new Locale(split[0], split[1]);
        }
        return locale;
    }

    //这里用不上，但不实现接口中这个类会报错
    @Override
    public void setLocale(HttpServletRequest httpServletRequest, @Nullable HttpServletResponse httpServletResponse, @Nullable Locale locale) { }
}
```

在 com.peng.config/MyConfig.java 配置文件中注册bean

```java
 	//自定义的国际化组件放到bean中生效
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
```

测试结果

![image-20200518144923235](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518144923235.png)

![image-20200518144936827](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518144936827.png)

#### 实现登录功能

LoginController.java 登录控制器

```java
@Controller
public class LoginController {
    @RequestMapping("/user/login")
    public String login(
            @RequestParam("username") String username,
            @RequestParam("password") String password,
            Model model){
        //具体的业务
        if (!StringUtils.isEmpty(username) && "123456".equals(password)){
            return "dashboard";
        }else {
            //告诉用户，登录失败
            model.addAttribute("msg","用户名或密码错误");
            return "index";
        }
    }
}
```

对应的 index.html 主页中插入提醒

```html
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```

#### 登录拦截器

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {

    //返回布尔值，是否放行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //在陆成功之后，应该有用户的sessoin
        Object loginUser = request.getSession().getAttribute("loginUser");

        if (loginUser==null){ //没有登录
            request.setAttribute("msg","没有权限，请先登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else {
            return true;
        }
    }
}
```

#### 展示员工列表

1. 提取公共页面
   1. `th: fragment="si debar"`
   2. `th: replace="~{ commons/ commons : : topbar}"`
   3. 如果要传递参数，可以直接使用 () 传参，接收判断即可
2. 列表循环展示

EmployeeController.java 员工控制器

```java
@Controller
public class EmployeeController {

    @Autowired
    EmployeeDao employeeDao;

    @RequestMapping("emps")
    public String list(Model model){
        Collection<Employee> employees = employeeDao.getAll();
        model.addAttribute("emps",employees);
        return "emp/list";
    }
}
```

![image-20200518195032098](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518195032098.png)

#### 添加员工实现

1. 按钮提交
2. 跳转到添加页面
3. 添加员工成功
4. 返回首页

EmployeeController.java 员工控制器，添加两个方法

```java
    @GetMapping("/emp")
    public String toAddpage(Model model){
        //查出所有部门的信息
        Collection<Department> departments = departmentDao.getDepartments();
        model.addAttribute("departments",departments);
        return "emp/add";
    }

    @PostMapping("/emp")
    public String addEmp(Employee employee){
        System.out.println("save => "+employee);
        //调川底层业务方法保存员工信息
        employeeDao.add(employee);
        return "redirect:emps";
    }
```

![image-20200518203916941](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518203916941.png)

#### 修改员工实现

EmployeeController.java 员工控制器，又添加两个方法

```java
	//去员工的修改页面
    @GetMapping("/emp/{id}")
    public String toUpdateEmp(@PathVariable("id")Integer id,Model model){
        //查出原来的数据
        Employee employee = employeeDao.getEmployeeById(id);
        model.addAttribute("emp",employee);
        //查出所有部门的信息
        Collection<Department> departments = departmentDao.getDepartments();
        model.addAttribute("departments",departments);
        return "emp/update";
    }

    @PostMapping("/updateEmp")
    public String updateEmp(Employee employee){
        employeeDao.add(employee); //修改
        return "redirect:/emps";
    }
```

![image-20200518211414523](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518211414523.png)

#### 删除员工实现

EmployeeController.java 员工控制器，又添了一个方法

```java
	//删除员工
    @GetMapping("/delemp/{id}")
    public String deleteEmp(@PathVariable("id")Integer id){
        employeeDao.delete(id);
        return "redirect:/emps";
    }
```

#### 注销

在 LoginController 里添加该函数

```java
	//注销
    @RequestMapping("/user/logout")
    public String logout(HttpSession session){
        session.invalidate();
        return "redirect:/index.html";
    }
```

#### 404

新建一个error文件夹，放一个 404.html 即可，500 也同理

![image-20200518212140808](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200518212140808.png)

#### 写网站步骤

1. 前端搞定，页面长什么样子，数据
2. 设计数据库（数据库设计 难点！）
3. 前端让他能够自动运行，独立化工程
4. 数据接口如何对接：json，对象 all in one！
5. 前后端联调测试

+ 注意点

  1. 有一套自己熟悉的后台模板：工作必要，推荐x- admin

  2. 前端界面：至少自己能够通过前端框架，组合出来一个网站页面

     index，about，blog，post，user

  3. 让这个网站能够独立运行

### SpringBoot 连接数据库

application.yaml 配置文件

```yaml
spring:
  datasource:
    username: admin
    password: 8098
    url: jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=false
    driver-class-name: com.mysql.jdbc.Driver
```

Spring04DataApplicationTest.java 测试类

```java
package com.peng;

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

        //可以进行各种操作了
        Statement statement = connection.createStatement();
        ResultSet resultSet = statement.executeQuery("SELECT * FROM mybatis.student");
        while (resultSet.next()){ //是个链表
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("tid="+resultSet.getObject("tid"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("=============================");
        }

        //关闭资源
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

JDBCController.java 在控制器中用jdbcTemplate操作

xxx TemplateSpringBoot 已经配置好模板 bean，拿来即用

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
    JdbcTemplate jdbcTemplate;

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
    public String updateUser(@PathVariable int id){
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
    public String deleteUser(@PathVariable int id){
        String sql = "DELETE FROM mybatis.user WHERE id=?";
        jdbcTemplate.update(sql,id);
        return "delete-ok";
    }
}
```

#### Druid数据源

springboot默认数据源：class com.zaxxer.hikari.HikariDataSource

pom.xml 导入依赖

```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.21</version>
</dependency>
<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
</dependency>
```

application.yaml 配置

```yaml
spring:
  datasource:
    username: admin
    password: 8098
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

com.peng.config/DruidConfig.java

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

测试

![image-20200520131934592](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200520131934592.png)

![image-20200520132008996](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200520132008996.png)

![image-20200520135439562](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200520135439562.png)

#### MyBatis

导入依赖

```xml
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.1.2</version>
</dependency>
```

配置环境

```yaml
spring:
  datasource:
    username: admin
    password: 8098
    url: jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    
#整合mybatis
mybatis:
  type-aliases-package: com.peng.pojo
  mapper-locations: classpath:mybatis/mapper/*.xml
```

1. 导入包
2. 配置文件
3. mybatis 配置（xml）
4. 编写 sq|（xml里编写sql范式）
5. service 层调用 dao层
6. controller 调用 service层