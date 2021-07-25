# Install

https://zhuanlan.zhihu.com/p/143156163

```bash
# 报错
jia@jia-virtual-machine:~$ docker container run hello-world
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.

# 解决
# https://blog.csdn.net/liangllhahaha/article/details/92077065
```

# Hello World

```bash
peng@peng-virtual-machine:~$ docker run ubuntu:18.04  /bin/echo "hello world"
# 参数解析
# docker: Docker 的二进制执行文件
# run: 与前面的 docker 组合来运行一个容器
# ubuntu:18.04 指定要运行的镜像，Docker 首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像
# /bin/echo "Hello world": 在启动的容器里执行的命令
Unable to find image 'ubuntu:18.04' locally # 没找到要运行的镜像
18.04: Pulling from library/ubuntu # 从lib里寻找镜像
25fa05cd42bd: Pull complete # 下载==>下载完了
Digest: sha256:139b3846cee2e63de9ced83cee7023a2d95763ee2573e5b0ab6dea9dfbd4db8f
Status: Downloaded newer image for ubuntu:18.04 # 镜像配置好咯
hello world # 输出
peng@peng-virtual-machine:~$ docker run ubuntu:18.04  /bin/echo "hello world2"
hello world2 # 镜像环境已经配置好了，所以直接输出了
peng@peng-virtual-machine:~$ docker run ubuntu:18.04  /bin/echo "hello world3"
hello world3 # 镜像环境已经配置好了，所以直接输出了
```

```bash
# 进入镜像
peng@peng-virtual-machine:~/桌面$ docker run -i -t ubuntu:18.04 /bin/bash
root@2436deacf733:/# 
```

```bash
# 后台模式
peng@peng-virtual-machine:~$ docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
62f57d405f486985a7745f6a230ff61507db24390f35792fcb9c7a57c0ada889
peng@peng-virtual-machine:~$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
62f57d405f48   ubuntu:18.04   "/bin/sh -c 'while t…"   3 seconds ago   Up 3 seconds             flamboyant_margulis

peng@peng-virtual-machine:~$ docker logs 62f57d405f48 # 打印日志
hello world
hello world
hello world
hello world
hello world
hello world
peng@peng-virtual-machine:~$ docker logs flamboyant_margulis # 打印日志
hello world
hello world
hello world
hello world
hello world
hello world

# 停止运行的两种方式
peng@peng-virtual-machine:~$ docker stop 62f57d405f48
62f57d405f48
peng@peng-virtual-machine:~$ docker stop flamboyant_margulis
flamboyant_margulis

# 重新运行暂停的容器
peng@peng-virtual-machine:~$ docker start 62f57d405f48
62f57d405f48

# 删除容器
peng@peng-virtual-machine:~$ docker rm -f 62f57d405f48
62f57d405f48
```

# Docker 容器使用

```bash
# 创建使用和退出
peng@peng-virtual-machine:~$ docker pull ubuntu # 创建
Using default tag: latest
latest: Pulling from library/ubuntu
c549ccf8d472: Pull complete 
Digest: sha256:aba80b77e27148d99c034a987e7da3a287ed455390352663418c0f2ed40417fe
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
peng@peng-virtual-machine:~$ docker run -it ubuntu /bin/bash # 使用
root@9c39fc139c14:/# exit # 退出
exit

# 查看所有的容器命令
peng@peng-virtual-machine:~$ docker ps -a
```

### Docker 运行 web 应用

```bash
peng@peng-virtual-machine:~$ docker pull training/webapp # 载入镜像
Using default tag: latest
latest: Pulling from training/webapp
Image docker.io/training/webapp:latest uses outdated schema1 manifest format. Please upgrade to a schema2 image for better future compatibility. More information at https://docs.docker.com/registry/spec/deprecated-schema-v1/
e190868d63f8: Pull complete 
909cd34c6fd7: Pull complete 
0b9bfabab7c1: Pull complete 
a3ed95caeb02: Pull complete 
10bbbc0fc0ff: Pull complete 
fca59b508e9f: Pull complete 
e7ae2541b15b: Pull complete 
9dd97ef58ce9: Pull complete 
a4c1b0cb7af7: Pull complete 
Digest: sha256:06e9c1983bd6d5db5fba376ccd63bfa529e8d02f23d5079b8f74a616308fb11d
Status: Downloaded newer image for training/webapp:latest
docker.io/training/webapp:latest
peng@peng-virtual-machine:~$ docker run -d -P training/webapp python app.py # 运行web
1652ce15451c0fb66e41373da9ee8660608c209e12aaf32b5734b0d11d190b7c
peng@peng-virtual-machine:~$ docker ps # 查看web应用容器，发现有端口信息
CONTAINER ID   IMAGE             COMMAND           CREATED          STATUS          PORTS                                         NAMES
1652ce15451c   training/webapp   "python app.py"   16 seconds ago   Up 15 seconds   0.0.0.0:49153->5000/tcp, :::49153->5000/tcp   quirky_carson
```

打开浏览器，运行成功

![image-20210713201747351](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713201747351.png)

```bash
peng@peng-virtual-machine:~$ docker port 1652ce15451c
5000/tcp -> 0.0.0.0:49153
5000/tcp -> :::49153
peng@peng-virtual-machine:~$ docker logs -f 1652ce15451c
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
172.17.0.1 - - [13/Jul/2021 12:16:50] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [13/Jul/2021 12:16:50] "GET /favicon.ico HTTP/1.1" 404 -

peng@peng-virtual-machine:~$ docker top 1652ce15451c
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                5754                5733                0                   20:14               ?                   00:00:00            python app.py
peng@peng-virtual-machine:~$ docker stop 1652ce15451c
1652ce15451c
peng@peng-virtual-machine:~$ docker rm 1652ce15451c
1652ce15451c
```

# Docker 镜像使用

```bash
peng@peng-virtual-machine:~$ docker images # 列出镜像
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
ubuntu            latest    9873176a8ff5   3 weeks ago    72.7MB
ubuntu            18.04     7d0d8fa37224   3 weeks ago    63.1MB
hello-world       latest    d1165f221234   4 months ago   13.3kB
training/webapp   latest    6fae60ef3446   6 years ago    349MB
```

查找镜像

```bash
peng@peng-virtual-machine:~$ docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11120     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4216      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   825                  [OK]
```

删除镜像

```bash
peng@peng-virtual-machine:~$ docker rmi hello-world
```

# Docker 容器连接

### 网络端口映射

```bash
peng@peng-virtual-machine:~$ docker run -d -P training/webapp python app.py
f1cd9d0ff44fd7c89018f158e68e6b29372169cf51987f39be5a486830c3e067
peng@peng-virtual-machine:~$ docker ps
CONTAINER ID   IMAGE             COMMAND           CREATED         STATUS         PORTS                                         NAMES
f1cd9d0ff44f   training/webapp   "python app.py"   7 seconds ago   Up 6 seconds   0.0.0.0:49153->5000/tcp, :::49153->5000/tcp   elegant_bhabha
peng@peng-virtual-machine:~$ docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py # 指定了ip地址
901610f8dd8e073c4cb4e138cbfba5214ffc070867d3bd3a03f0b552c77508d7

# 上面默认都是绑定 tcp 端口，如果要绑定 UDP 端口，可以在端口后面加上 /udp
peng@peng-virtual-machine:~$ docker run -d -p 127.0.0.1:5001:5000/udp training/webapp python app.py
9692652ebac97fb3fde895c2611f5c27d68b05f70ceae27ec2f5056c9b9c7f1c

peng@peng-virtual-machine:~$ docker ps
CONTAINER ID   IMAGE             COMMAND           CREATED          STATUS          PORTS                                         NAMES
9692652ebac9   training/webapp   "python app.py"   45 seconds ago   Up 44 seconds   5000/tcp, 127.0.0.1:5001->5000/udp            gifted_joliot
901610f8dd8e   training/webapp   "python app.py"   3 minutes ago    Up 3 minutes    127.0.0.1:5001->5000/tcp                      priceless_aryabhata
f1cd9d0ff44f   training/webapp   "python app.py"   4 minutes ago    Up 4 minutes    0.0.0.0:49153->5000/tcp, :::49153->5000/tcp   elegant_bhabha
peng@peng-virtual-machine:~$ docker port 901610f8dd8e 
5000/tcp -> 127.0.0.1:5001
```

### 容器互联

```bash
## 容器命名
peng@peng-virtual-machine:~$ docker run -d -P --name jiajia training/webapp python app.py 
8f8d410101dadf80cf2d9c4040bf32269a42aeabb61b5f512ca23af04c26f771

## 新建网络
peng@peng-virtual-machine:~$ docker network create -d bridge test-net 
3d1aaa490c5668b3489e3a068c212bccd285f609ee25a9ac64e6f4a73ee914a9
peng@peng-virtual-machine:~$ docker network ls
NETWORK ID     NAME       DRIVER    SCOPE
9edef8661be9   bridge     bridge    local
cd97074f0986   host       host      local
96d3ade2909a   none       null      local
3d1aaa490c56   test-net   bridge    local

## 连接容器
# 运行一个容器并连接到新建的 test-net 网络
peng@peng-virtual-machine:~$ docker run -itd --name test1 --network test-net ubuntu /bin/bash
ae643470cad5041378819e02dc6091be98b99ba0e533c0ce00850cb41254b144
# 打开新的终端，再运行一个容器并加入到 test-net 网络
peng@peng-virtual-machine:~$ docker run -itd --name test2 --network test-net ubuntu /bin/bash
803de99afdd35842143beee8e089f3e38450f4c6c65832d4ddd5d4da608b20a7
# 互ping
peng@peng-virtual-machine:~$ docker exec -it test1 /bin/bash
root@ae643470cad5:/# ping test2
PING test2 (172.18.0.3) 56(84) bytes of data.
64 bytes from test2.test-net (172.18.0.3): icmp_seq=1 ttl=64 time=0.141 ms
64 bytes from test2.test-net (172.18.0.3): icmp_seq=2 ttl=64 time=0.050 ms
64 bytes from test2.test-net (172.18.0.3): icmp_seq=3 ttl=64 time=0.051 ms
^C
--- test2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2025ms
rtt min/avg/max/mdev = 0.050/0.080/0.141/0.042 ms
root@ae643470cad5:/# exit
exit
peng@peng-virtual-machine:~$ docker exec -it test2 /bin/bash
root@803de99afdd3:/# ping test1
PING test1 (172.18.0.2) 56(84) bytes of data.
64 bytes from test1.test-net (172.18.0.2): icmp_seq=1 ttl=64 time=0.068 ms
64 bytes from test1.test-net (172.18.0.2): icmp_seq=2 ttl=64 time=0.139 ms
64 bytes from test1.test-net (172.18.0.2): icmp_seq=3 ttl=64 time=0.058 ms
^C
--- test1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2037ms
rtt min/avg/max/mdev = 0.058/0.088/0.139/0.036 ms
root@803de99afdd3:/# exit
exit
```

# Docker 仓库管理

[https://hub.docker.com](https://hub.docker.com/) 免费注册

可以在里边 search 和 pull 其它人发布的镜像

```bash
# 登录dockerhub
peng@peng-virtual-machine:~$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: pengyirusi
Password: 
WARNING! Your password will be stored unencrypted in /home/peng/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

# 推送镜像
peng@peng-virtual-machine:~$ docker tag hello-world:latest pengyirusi/hello

# 退出dockerhub
peng@peng-virtual-machine:~$ docker logout
Removing login credentials for https://index.docker.io/v1/
```

# Tomcat + IDEA

https://blog.csdn.net/Singlepledge/article/details/107139265

# Docker + IDEA

https://www.jianshu.com/p/d931e7f47966 入门

https://blog.csdn.net/weixin_44520739/article/details/105209447 更狠！

