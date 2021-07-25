# NoSQL概述

## Why

### 单机时代

1. 数据量太大，一个机器放不下
2. 数据的索引太大，一个服务器内存放不下
3. 访问量太大，一个服务器承受不了

### 缓存 + MySQL + 读写分离

网站 80% 的情况都是在读，每次都查数据库十分麻烦

Memcached 出现了！

### 分库分表 + 水平拆分 + MySQL集群

数据库的本质：读 + 写

早年 MyISAM：表锁，十分影响效率，高并发拉胯

Innodb：行锁

分库分表来解决写的压力

MySQL集群出现了！

### 现在

MySQL 等关系型数据库不够用了

## What

NoSQL = not only sql，泛指非关系型数据库

关系型：行和列

非关系型：`Map<String,Object>`

特点：

1. 方便扩展，数据之间没有关系

2. 大数据量高性能，Redis 一秒写 8w，读取 11w，NoSQL 的缓存是一种细粒度的缓存

3. 数据类型是多样的，不需要事先设计数据库，随取随用

4. 传统 RDBMS 和 NoSQL 区别

   ```bash
   RDBMS
   - 结构化组织
   - SQL
   - 数据和关系都在单独的表中 row column
   - 严格的一致性
   - 基础的事务
   - ...
   
   NoSQL
   - 不仅仅是数据
   - 没有固定的查询语言
   - 键值对存储，列存储，文档存储，图形数据库...
   - 最终一致性
   - CAP定理 BASE理论
   - 高性能，高可用，高可扩展
   - ...
   ```

  不同的数据类型放到不同的数据库（以淘宝为例）

```bash
商品基本信息：关系型数据库 Mysql，Oracle...
文字描述：文档型数据库 MongoDB
图片视频：分布式文件系统 FastDFS，TFS，GFS，HDFS...
商品的关键字：搜索引擎 solr，elasticsearch，ISearch
热门信息秒杀：内存数据库 Redis，Memcache
支付交易接口：三方应用
```

## NoSQL 四大分类

KV 键值对：Redis，Tair，memcache，内容缓存，日志

文档型数据库（bson）：MongoDB（基于分布式文件存储的数据库，主要用于处理大量的文档，介于 sql 和 nosql 中间的产品），ConchDB，查询性能不高

列存储数据库：HBase，分布式文件系统，查询速度快

图形关系数据库：不是存图形，放的是关系，Neo4j，InfoGrid

# Redis入门

## 概述

### What

ReDiS = Remote Dictionary Server

免费开源的 c语言写的 基于内存的 可持久化 日志型的 kv数据库

### How

Redis 能干嘛：

1. 内存存储、持久化，内存中是断电即失，持久化很关键
2. 效率高，可用于高速存缓
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（浏览量）

特性：

1. 多样的数据类型
2. 持久化
3. 集群
4. 事务

官网：[英文](redis.io)，[中文](redis.cn)

Linux 版本在官网下载，Windows 在 [Github](https://github.com/elliotxx/my-redis) 上下载，不建议使用

## 安装

### Windows 安装

1. 下载安装包：[https://github.com/elliotxx/my-redis](https://github.com/elliotxx/my-redis)

2. 解压之后

   ```
   redis-server.exe 服务器
   redis-cli.exe 客户端
   redis-check-aof.exe AOF持久化
   redis-benchmark.exe 性能测试
   ```

3. 开启 Redis，双击服务即可

   ```
   [9868] 21 Nov 13:59:50.760 # Warning: no config file specified, using the default config. In order to specify a config file use D:\my-redis-master\redis-server.exe /path/to/redis.conf
                   _._
              _.-``__ ''-._
         _.-``    `.  `_.  ''-._           Redis 3.2.100 (00000000/0) 64 bit
     .-`` .-```.  ```\/    _.,_ ''-._
    (    '      ,       .-`  | `,    )     Running in standalone mode
    |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
    |    `-._   `._    /     _.-'    |     PID: 9868
     `-._    `-._  `-./  _.-'    _.-'
    |`-._`-._    `-.__.-'    _.-'_.-'|
    |    `-._`-._        _.-'_.-'    |           http://redis.io
     `-._    `-._`-.__.-'_.-'    _.-'
    |`-._`-._    `-.__.-'    _.-'_.-'|
    |    `-._`-._        _.-'_.-'    |
     `-._    `-._`-.__.-'_.-'    _.-'
         `-._    `-.__.-'    _.-'
             `-._        _.-'
                 `-.__.-'
   
   [9868] 21 Nov 13:59:50.766 # Server started, Redis version 3.2.100
   [9868] 21 Nov 13:59:50.766 * The server is now ready to accept connections on port 6379
   ```

   默认端口：6379

4. 使用 Redis 客户端来连接服务端

   ```
   127.0.0.1:6379> ping #测试连接
   PONG
   127.0.0.1:6379> set name peng #设置kv
   OK
   127.0.0.1:6379> get name #getKey
   "peng"
   127.0.0.1:6379>
   ```

### Linux 安装

1. 下载安装包 redis-5.0.8.tar.gz

2. 解压 `tar -zxvf redis-5.0.8.tar.gz`

3. 进入解压后的文件，找到配置文件 redis.conf

4. 基本环境安装 

   `yum install gcc-c++ `

   `make`

   `make install` 确认一下

5. redis 默认安装路径

   `cd usr/local/bin`

   里面有 redis-server、redis-cli 等，比 windows 多了一个 redis-sentinel（哨兵）

6. 将 redis 配置文件，复制到我们当前目录下

   `mk pengconfig`

   `cp opt/redis-5.0.8/redis.conf pengconfig `

7. redis 默认不是后台启动的，修改配置文件

   `vi redis.conf`

   ```conf
   daemonize no # 改成yes
   ```

8. 启动 Redis 服务

   `redis-server pengconfig/redis.conf`

   客户端连服务端

   `redis-cli -h localhost -p 6379`

9. 查看 redis 是否开启

   `ps -ef|grep redis`

10. 关闭 redis 服务

    `shutdown `

    `exit`

### benchmark 性能测试

官方自带的压力测试工具

```bash
# 测试 100个并发连接 每个连接100000请求
benchmark -h 127.0.0.1 -p 6379 -c 100 -n 100000
```

## 基础知识

### redis 默认有 16 个数据库

```
databases 16
```

默认使用的是第一个数据库，可以用 select 语句切换数据库

```bash
127.0.0.1:6379> select 3
OK
127.0.0.1:6379[3]> dbsize # 查看大小
(integer) 0
127.0.0.1:6379[3]> set name peng
OK
127.0.0.1:6379[3]> get name
"peng"
127.0.0.1:6379[3]> dbsize
(integer) 1
127.0.0.1:6379[3]> keys * # 查看所有的key
1) "name"
127.0.0.1:6379[3]> flushdb # 清空当前数据库
OK
127.0.0.1:6379[3]> keys *
(empty list or set)
127.0.0.1:6379[3]> flushall # 清空全部数据库
OK
```

### Redis 是单线程的

Redis 是很快的，Redis 的瓶颈是机器的内存和网络带宽

他不在 CPU 里，还单线程，为啥还这么快：

1. Redis 是 C 语言写的，100000+ 的 QPS
2. 误区：多线程一定比单线程效率高（多线程的线程切换浪费时间）
3. Redis 将所有的数据全部放在内存中，没有上下文切换

# 数据类型

Redis 可以用作数据库、缓存和消息中间件

## Redis-Key

```bash
127.0.0.1:6379> set name peng
OK
127.0.0.1:6379> exists name # 是否存在这个key
(integer) 1
127.0.0.1:6379> exists name1
(integer) 0
127.0.0.1:6379> move name 1 # 将key移动到其他数据库
(integer) 1
127.0.0.1:6379> exists name
(integer) 0 # 1代表true，0代表false
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "name"
127.0.0.1:6379> set age 18
OK
127.0.0.1:6379> expire age 10 # 设置key的过期时间，单位是秒
(integer) 1
127.0.0.1:6379> ttl age # 查看当前key的剩余时间
(integer) 7 # 还有7s
127.0.0.1:6379> ttl age
(integer) 3 # 还有3s
127.0.0.1:6379> ttl age # key失效返回-2
(integer) -2
127.0.0.1:6379> get age
(nil)
127.0.0.1:6379> type name # 查看key的类型
string
```

## String (字符串)

```bash
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> append key1 hello # 追加字符串，如果当前key不存在，相当于直接新set一个key-value                           
(integer) 7 # 返回了String的长度
127.0.0.1:6379> get key1
"v1hello"
127.0.0.1:6379> strlen key1
(integer) 7
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views # i++操作
(integer) 1
127.0.0.1:6379> decr views # i--操作
(integer) 0
127.0.0.1:6379> incrby views 10 # i+=10
(integer) 10
127.0.0.1:6379> decrby views 10
(integer) 0
```

## List



## Set



## Hash



## Zset

