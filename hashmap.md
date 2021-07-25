数组+链表

map在new的时候不初始化，put的时候初始化

第一次put操作时：判断map是否为null或者长度为0，若为空，调用resize()方法，给map的容积等赋初始值16（最大为1<<31），负载因子0.75，threshold为12，然后new一个数组，初始化完成！

然后hash(key)：

![image-20200816202758400](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200816202758400.png)

hashcode是个int !!!

![image-20200816203345969](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200816203345969.png)

map的数组下标计算 `(n-1)&hash`，n是表长，表长必须是2的n次幂，才能这样算。

new一个结点放到该下标，`tab[p] = new Node(...)`，如果有结点了，`新结点.next = 原来的结点`，链表长度达到8，将链表转成红黑树

map数量达到threshold，扩容，代码 `if (++size > threshold) resize();`resize()可太狠了，扩容直接左移一位

hashmap不是线程安全的，

原子性：HashTable，Synchronized Map（put方法前加了锁，性能会变低）

有序性：ConcurrentHashMap，JUC并发包里的

CAS，compare and swap，乐观锁

transient，序列化

volatile，可见性，共享变量

ConcurrentHashMap

![img](file:///D:\TencentQQ\QQ下载文件\2522548509\Image\C2C\{0Q6QVNJOK{S0(7IJ)MH69D.png)

synchronized只锁下标，不锁整个map，狠