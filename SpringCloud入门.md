![image-20200706125944347](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706125944347.png)

创建 父项目 和 3 个模块

![image-20200706130708092](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706130708092.png)

配置

![image-20200706143236114](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143236114.png)

Service层

![image-20200706141151671](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706141151671.png)

![image-20200706143657112](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143657112.png)

![image-20200706141038542](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706141038542.png)

ConsumerController 访问 ProviderDepController 里的内容

![image-20200706140805152](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706140805152.png)

**Euraka** 注册中心

![image-20200706144726110](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706144726110.png)

new 一个 project

![image-20200706141509324](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706141509324.png)

导入依赖

![image-20200706141825455](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706141825455.png)

配置 yaml

![image-20200706142227821](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706142227821.png)

EurakaServer7001.java 启动类

![image-20200706142435880](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706142435880.png)

**将  ProviderDepController8001 注册到 EurakaServer7001**

导入依赖

![image-20200706143130319](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143130319.png)

配置

![image-20200706143306345](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143306345.png)

再启动的时候就把服务自动注册到 Euraka 里了

**此时 ConsumerController 调用 Euraka 就行了**

导入依赖

![image-20200706143604104](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143604104.png)

增加 Euraka 相关配置 yaml

![image-20200706144444847](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706144444847.png)

修改 Config

![image-20200706143748737](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143748737.png)

修改 ConsumerController，服务多的时候地址不好找，但服务名好找

![image-20200706144105837](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706144105837.png)

![image-20200706143949639](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706143949639.png)

修改启动类，添加一个 Euraka 的相关注解

![image-20200706144254315](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200706144254315.png)