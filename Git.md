+ 安装

  官网下载exe，一路默认，安装到d盘

  ![image-20200917085227224](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917085227224.png)

+ IDEA配置git

  + 配置路径

    ![image-20200917085838597](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917085838597.png)

  + 登录账号

    ![image-20200917090205717](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917090205717.png)

    443错误，ping不过去

    ![image-20200917091130353](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917091130353.png)

    大概是网络被封，需用代理，命令如下

    ```
    git config --global http.proxy http://proxyuser:proxypwd@proxy.server.com:8080
    ```

    - change proxyuser to your proxy user
    - change proxypwd to your proxy password
    - change proxy.server.com to the URL of your proxy server
    - change 8080 to the proxy port configured on your proxy server

    代理设置为172.16.13.171:8080，修改之后，连接成功

    ![image-20200917092259900](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917092259900.png)

    一些命令

    ```
    git config --global http.proxy  查询代理
    git config --global --unset http.proxy  取消代理
    git config --global http.proxy http:172.16.13.171:8080 设置代理
    git config --global https.proxy http:172.16.13.171:8080
    ```

+ pull项目

  + checkout

    ![image-20200917092839244](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917092839244.png)

  + 输入项目参数，就克隆how2j这个吧

    ![image-20200917093525137](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200917093525137.png)

+ 创建项目

  + 新建仓库

    ![image-20200921082831347](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921082831347.png)

    输入仓库名

    ![image-20200921082948066](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921082948066.png)

    得到github地址

    ![image-20200921083055583](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083055583.png)

  + 在IDEA新建一个项目

    ![image-20200921083301175](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083301175.png)

  + 建立本地仓库

    ![image-20200921083430639](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083430639.png)

  + 将项目加入本地仓库

    ![image-20200921083647252](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083647252.png)

  + 提交项目

    ![image-20200921083742802](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083742802.png)

    ![image-20200921083901934](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921083901934.png)

    将remote换成github之前仓库地址：https://github.com/pengyirusi/helloworld.git

    ![image-20200921084137395](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200921084137395.png)

    最后点push就可以了

+ IDEA提交和更新

  + 提交改动（本地 -> github）：CTRL + K
  + 更新（github -> 本地）：CTRL + T