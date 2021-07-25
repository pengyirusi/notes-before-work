# JVM探究

+ 请你谈谈你对JVM的理解? java8虚拟机和之 前的变化更新?
+ 什么是OOM，什么是栈溢出StackOverFlowError?怎么分析?
+ JVM的常用调优参数有哪些?
+ 内存快照如何抓取，怎么分析Dump文件?知道吗?
+ 谈谈JVM中，类加载器你的认识?

![12be1b7013cdadf2379e609c9286c947](G:\下载\Chrome下载\12be1b7013cdadf2379e609c9286c947.jpg)

1. JVM的位置

   ![image-20200707091314312](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707091314312.png)

2. JVM的体系结构

   ![image-20200707092030752](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707092030752.png)

   所谓JVM调优就是调 方法区 和 堆

3. 类加载器

   作用：加载 Class 文件

   ![image-20200707093649135](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707093649135.png)

   ![image-20200707093851821](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707093851821.png)

   3.1 虚拟机自带的加载器

   3.2 启动类（根）加载器

   3.3 扩展类加载器

   3.4 应用程序加载类

   ![image-20200707103218672](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707103218672.png)

4. 双亲委派机制

   ![image-20200707103508465](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707103508465.png)

   作用：保证安全

   java程序往上找，BOOT( rt.jar 优先执行 ) => EXC  => APP，找不到才执行自己的方法

   ![image-20200707104542801](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707104542801.png)

5. 沙箱安全机制

   字节码校验器(bytecode verifier)：确保Java类文件遵循Java语言规范。这样可以帮助Java程序实现内存
   保护。但并不是所有的类文件都会经过字节码校验，比如核心类。

   JRE = { 类加载器 => 字节码校验器 => 解释器 }

6. Native

   ![image-20200707112436976](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707112436976.png)

   本地方法栈通过JNI连接本地方法接口，本地方法接口调用本地方法库

7. PC寄存器

   程序计数器：Program Counter Register
   每个线程都有一个程序计数器，是线程私有的，就是一个指针，指向方法区中的方法字节码（用来存储指向像一条指令的地址， 也即将要执行的指令代码），在执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不计

8. 方法区

   方法区是被所有线程共享，所有字段和方法字节码，以及一些特殊方法，如构造函数，接口代码也在此定义，简单说，所有定义的方法的信息都保存在该区域，此区域属于共享区间
   **静态变量 static、常量 final、类信息 Class（构造方法、接口定义）、运行时的 常量池 存在方法区中，但是实例变量存在堆内存中，和方法区无关**

9. 栈

   main 先执行，最后结束

   栈：栈内存,主管程序的运行，生命周期和线程同步

   线程结束，栈内存也就是释放，对于栈来说，不存在垃圾回收问题

   栈：8大基本类型 + 对象引用 + 实例的方法

   栈运行原理：栈帧

   ![image-20200707120151055](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707120151055.png)

   栈满了：StackOverFlowError 是错误，不是异常

   栈 + 堆 + 方法区：交互关系

   ![image-20200707120729703](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707120729703.png)

10. 三种JVM

    + Sun公司 HotSpot
    + Bea公司 JRockit
    + IBM公司 J9

11. 堆 Heap

    一个JVM只有一个堆内存，堆内存的大小是可以调节的

    类加载器读取了类文件后，把什么放到堆中？类，方法，常量，变量 ~ 保存所有引用类型的真实变量

    三个区域：

    + 新生区（伊甸园区 Eden，幸存区0区，幸存区1区）
    + 养老区
    + 永久区

    ![image-20200707131956080](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707131956080.png)

    GC垃圾回收，主要是在伊甸园区和养老区

    假设内存满了，OOM，堆内存不够

    ![image-20200707132446869](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707132446869.png)

    在JDK8以后，永久存储区改了名字（元空间）

12. 新生区

    + 类：诞生和成长的地方，甚至死亡
    + 伊甸园区：所有的对象都是在伊甸园区new出来的
    + 幸存者区（0, 1）：

13. 老年区

    ![image-20200707133209127](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707133209127.png)

    经过研究，99%的对象都是临时对象

14. 永久区

    这个区域常驻内存的。用来存放JDK自身携带的Class对象。Interface元数据，存储的是ava运行时的一些环境或类信息，这个区域不存在垃圾回收！关闭虚拟机，就会释放这个区域的内存

    一个启动类加载了大量的第三方jar包，tomcat部署了太多的应用，大量动态生成的反射类。一旦不断地被加载，知道内存满，就会出现OOM

    + jdk 1.6 之前：永久代，常量池在方法区中
    + jdk 1.7：永久代，但是慢慢地退化了，去永久代，常量池在堆中
    + jdk 1.8 之后：无永久代，常量池在元空间

15. 堆内存调优

    ![image-20200707135627415](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707135627415.png)

    手动调节堆内存

    ![image-20200707135454801](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707135454801.png)

    ```
    OOM之后怎么解决
    1.扩大堆内存空间，看结果
    -Xms1024m -Xmx1024m -XX:+PrintGCDetails
    2.还不行说明代码有问题，导致内存出问题
    ```

    ![image-20200707135817329](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707135817329.png)

    幸存0区1区，即from区to区

    Heap内存 = Young + Old，Metaspace元空间，不在堆里

    JVM总内存 = Heap + Metaspace，Metaspace增大后会占用更多空间，导致Heap内存减少，Metaspace大到把Heap空间挤没时，报错OOM

    ![image-20200707140747470](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707140747470.png)

    Young满了执行GC，一部分消除，一部分进入Old，Old也满了执行FullGC，一部分消除，一部分进入Metaspace

    在一个项目中，突然出现OOM故障，如何排除 ~ 研究为什么出错

    + 能够看到代码第几行出错：内存快照分析工具，MAT（Eclipse），Jprofiler
    + Dubug，一行行分析代码

    MAT，Jprofiler 作用：

    + 分析Dump内存文件，快速定位内存泄漏
    + 获得堆中的数据
    + 获得大的对象
    + . . .

    IDEA安装插件

    ![image-20200707141825830](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707141825830.png)

    官网下载

    ![image-20200707142341465](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707142341465.png)

    9.2版本的注册码

    ![image-20200707144627247](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707144627247.png)

    如果堆溢出了，丢出文件，可以用JProfiler打开它，查看内存信息，找出问题

    ![image-20200707151833304](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707151833304.png)

    一般式

    ```java
    -Xms //设置初始化内存分配大小，默认1/64
    -Xmx //设置最大内存分配大小，默认1/4
    -XX:+PrintGCDetails //打印GC清理信息
    -XX:+HeapDumpOnOutOfMemoryError //OOM dump，别的错误同理
    ```

    ![image-20200707152044212](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707152044212.png)

    比如这个内存占了89%，这个 ArrayList 肯定有问题啊

    ![image-20200707152312543](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707152312543.png)

16. GC 垃圾回收算法

    GC的作用区域：堆 和 方法区（属于堆）

    + 新生代
    + 幸存区（from，to）
    + 老年区

    GC两种类型：轻GC（普通的GC），重GC（全局GC）

    题目：

    + JVM的内存模型和分区~详细到每个区放什么？
    + 堆里面的分区有哪些? Eden，from，to，老年区，说说他们的特点？
    + GC的算法有哪些？标记清除算法，标记整理，复制算法，引用计数法，怎么用的？
    + 轻GC和重GC分别在什么时候发生？

    **引用计数法**：太low了，不高效，不常用

    ![image-20200707153905935](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707153905935.png)

    **复制算法**：新生代主要用到的算法

    ![image-20200707155200207](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707155200207.png)

    幸存的对象来到幸存区时，会选择空的to区，然后把from区的对象复制到to区，from变成空的to区，原来的to区变成from区；这样可以承上启下，不会卡死

    每次GC之后，Eden是空的，to是空的，GC15次之后还活着，进入下一阶段，老年区

    + 好处：没有内存的碎片
    + 坏处：浪费了内存空间，一半空间永远是空的

    复制算法最佳使用场景：对象存活度较低的时候，新生区~

    **标记清除算法**：

    ![image-20200707160439136](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707160439136.png)

    + 优点：不需要额外的空间
    + 缺点：两次扫描，浪费时间，会产生内存碎片

    **标记压缩算法**：再优化，解决了内存碎片

    ![image-20200707160801572](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707160801572.png)

    + 缺点：又多一次扫描，还有移动成本

    **标记清除压缩**：先标记清除几次，再进行压缩

17. GC 总结

    内存效率：复制算法 > 标记清除算法 > 标记压缩算法（时间复杂度）

    内存整齐度：复制算法 = 标记压缩算法 > 标记清除算法

    内存利用率：标记压缩算法 = 标记清除算法 > 复制算法

    GC ~ 分代收集算法

18. JMM 

    java memory model ~ java内存模型

    作用：缓存一致性协议， 用于定义数据读写的规则（遵守，找到这个规则）。

    JMM定义了线程工作内存和主内存之间的抽象关系：线程之间的共享变量存储在主内存(Main Memory)中，每个线程都有一个私有的本地内存(Local Memory)

    ![image-20200707162919618](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707162919618.png)

    解决共享对象可见性这个问题：volilate，立即改主存，让别人拿到新的数据

    JMM：抽象理论信息

    ![image-20200707163538494](C:\Users\peng\AppData\Roaming\Typora\typora-user-images\image-20200707163538494.png)

    volilate关键字：解决一致性的

