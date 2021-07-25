### Maven

+ 作用
  1. 在Javaweb开发中，需要使用大量的jar包，我们手动去导入；
  2. 如何能够让一个东西自动帮我导入和配置这个jar包。

#### Maven项目架构管理工具

我们目前用来就是方便导入jar包的

+ 核心思想：**约定大于配置**，有约束，不要去违反

  Maven会规定好你如何去编写java代码，必须要按照这个规范来

#### 下载安装Maven

+ 下载地址

  http://maven.apache.org/download.cgi

+ 安装配置

  1. 下载 binary.zip 的 maven 并解压

  2. 配置环境变量

     + M2_HOME：maven下的bin目录
     + MAVEN_HOME：maven目录
     + 在系统的path中配置 %MAVEN_HOME%\bin

     ![image-20200513122735690](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200513122735690.png)

#### 阿里云镜像

+ 镜像：mirrors

  加速我们的下载，国内建议使用阿里云的镜像

  conf - - setting.xml 文件里找到 mirrors

  ```XML
  <mirror>
  	<id>nexus-aliyun</id>
  	<mirrorOf>*,!jeecg,!jeecg-snapshots</mirrorOf>
  	<name>Nexus aliyun</name>
  	<url>http://maven.aliyun.com/nexus/content/groups/public</url>
  </mirror>
  ```

#### 本地仓库

+ 建立一个本地仓库：LocalRepository

  ```xml
  <localRepository>D:\environment\apache-maven-3.6.3\maven-repo</localRepository>
  ```

  仓库里管理的是jar包

#### IDEA中使用Maven

1. 启动IDEA，创建一个Maven项目

   ![image-20200514124125066](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514124125066.png)

   ![image-20200514124629817](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514124629817.png)

2. 等待项目初始化完毕

   ![image-20200514125025300](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514125025300.png)

3. 观察maven仓库中多了什么东西

   ![image-20200514125301520](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514125301520.png)

4. IDEA中的maven设置

   IDEA项目创建成功后，看一眼Maven的配置，看local文件是不是原来配置的地方

5. 到这里，Maven在IDEA中的配置和使用就OK了

#### 创建一个普通的Maven项目

1. 什么都不勾，直接next

   ![image-20200514130215598](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514130215598.png)

   ![image-20200514130517360](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514130517360.png)

#### 标记文件夹功能

+ mark directory as

  sources root 源码目录

  test sources root 测试源码目录

  resources root 资源目录

  test resources root 测试资源目录

+ 另一种方式

  ![image-20200514131333728](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514131333728.png)

  ![image-20200514131519355](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514131519355.png)

#### 在IDEA中配置tomcat

![image-20200514131832823](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514131832823.png)

![image-20200514132012806](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514132012806.png)

![image-20200514132911149](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514132911149.png)

为什么会有这个问题：我们访问一个网站，需要指定一个文件夹名字

![image-20200514133045110](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514133045110.png)

![image-20200514133536638](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514133536638.png)

启动tomcat

![image-20200514133640058](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514133640058.png)

启动成功

![image-20200514133742024](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514133742024.png)

这里访问到的helloworld，就是我们默认的index.jsp的内容

![image-20200514133955424](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200514133955424.png)

#### pom文件

pom.xml 是maven的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--maven的版本和头文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!--创建项目时配置的GAV-->
  <groupId>com.peng</groupId>
  <artifactId>javaweb-01-maven</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!--package：项目的打包方式
  jar：java应用
  war：javaweb应用
  -->
  <packaging>war</packaging>

  <!--name没啥用，可以删掉-->
  <name>javaweb-01-maven Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <!--配置-->
  <properties>
    <!--项目的默认构建编码-->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编译版本-->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <!--项目依赖-->
  <dependencies>
    <!--具体依赖的jar包配置文件-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
    </dependency>
  </dependencies>

  <!--项目构建用的东西-->
  <build>
    <finalName>javaweb-01-maven</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

+ maven 的高级之处在于，他会帮你导入这个JAR包所低赖的其它jar包