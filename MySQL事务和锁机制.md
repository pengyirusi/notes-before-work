### 事务

作为单个逻辑工作单元执行的一系列操作，要么完全成功，要么完全失败

### ACID

原子性

一致性（转账），读一致性

隔离性（并发下）

永久性

### 并发带来的问题

脏读

不可重复读

幻读

![image-20200819201800887](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200819201800887.png)

为什么innodb（聚集索引）可以解决幻读

![image-20200819202237687](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200819202237687.png)

LBCC 相当于串行化

![image-20200819202301598](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200819202301598.png)

MVCC

![image-20200819202307442](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200819202307442.png)

