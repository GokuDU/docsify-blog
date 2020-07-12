# JVM

## 1.JVM的位置



## 2.JVM的结构

![image-20200612204639468](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612204639468.png)



![image-20200612212704459](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612212704459.png)



## 3.类加载器

作用：加载 Class文件

![image-20200612190839526](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612190839526.png)



* 同一个类实例化的不同对象，获取的Class是同一个

```java
public class Car {

    public static void main(String[] args) {
        Car car1 =new Car();
        Car car2 =new Car();
        Car car3 =new Car();

        System.out.println(car1.hashCode());
        System.out.println(car2.hashCode());
        System.out.println(car3.hashCode());
        System.out.println("---------------------");

        Class<? extends Car> aClass1 = car1.getClass();
        Class<? extends Car> aClass2 = car1.getClass();
        Class<? extends Car> aClass3 = car1.getClass();

        System.out.println(aClass1.hashCode());
        System.out.println(aClass2.hashCode());
        System.out.println(aClass3.hashCode());
    }
}
```

![image-20200612191711594](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612191711594.png)



* 获取类加载器

```java
public class Car {

    public static void main(String[] args) {
        Car car1 =new Car();
        Car car2 =new Car();
        Car car3 =new Car();

        Class<? extends Car> aClass1 = car1.getClass();
        ClassLoader classLoader = aClass1.getClassLoader();

        System.out.println(classLoader);        // AppClassLoader
        System.out.println(classLoader.getParent());    // ExtClassLoader
        System.out.println(classLoader.getParent().getParent());    // null  java程序获取不到（BootStrap）
    }
}
```



![image-20200612192133624](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612192133624.png)



### 双亲委派机制

每一个类都有一个对应它的类加载器。系统中的 ClassLoder 在协同工作的时候会默认使用 **双亲委派模型** 。即在类加载的时候，系统会首先判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载。加载的时候，首先会把该请求委派该父类加载器的 `loadClass()` 处理，因此所有的请求最终都应该传送到顶层的启动类加载器 `BootstrapClassLoader` 中。当父类加载器无法处理时，才由自己来处理。当父类加载器为null时，会使用启动类加载器 `BootstrapClassLoader` 作为父类加载器。

[![ClassLoader](https://camo.githubusercontent.com/4311721b0968c1b9fd63bdc0acf11d7358a52ff6/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f636c6173736c6f616465725f5750532545352539422542452545372538392538372e706e67)](https://camo.githubusercontent.com/4311721b0968c1b9fd63bdc0acf11d7358a52ff6/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f636c6173736c6f616465725f5750532545352539422542452545372538392538372e706e67)

每个类加载都有一个父类加载器，我们通过下面的程序来验证。



* 测试

```java
public class String {

    public String toString(){
        return "hello";
    }

    public static void main(String[] args) {
        String s = new String();
        System.out.println(s.getClass().getClassLoader());
        s.toString();
    }
    /*
        1. 类加载器收到类加载的请求
        2. 将这个请求向上委托给父亲加载器去完成（判断当前类是否被加载过。已经被加载的类会直接返回），一直向上委托，一直到启动类加载器
        3. 启动类加载器检查是否能加载这个类，能加载就结束，使用当前加载器。否则，就抛出异常，通知子类加载器加载
        4. 重复步骤3
        ClassNotFound
        
        null ： java加载不到  c、c++
     */
}
```



![image-20200612193959922](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612193959922.png)





## 4.沙箱委派机制



## Native

* native : 带native关键字的，说明java作用范围达不到了，会去调用底层的C语言的库
* ​        进入本地方法栈
* ​        调用本地方法接口    JNI（java Native interface）
* ​        JNI作用，扩展java的使用，融合不同的编程语言的优点
* ​        内存中专门开辟了一块标记区域: Native Method Stack ，登记 native 方法

```java
public class DemoNative {
    public static void main(String[] args) {

//        new Thread("my thread name"){
//            public void run(){
//
//            }
//        }.start();

        new Thread(()->{

        },"my thread name").start();
    }

    /*
        native : 带native关键字的，说明java作用范围达不到了，会去调用底层的C语言的库
        进入本地方法栈
        调用本地方法接口    JNI（java Native interface）
        JNI作用，扩展java的使用，融合不同的编程语言的优点
        内存中专门开辟了一块标记区域: Native Method Stack ，登记 native 方法
     */
    private native void start0();
}
```



## pc寄存器

程序计数器是一块==较小==的内存空间，可以看作是当前线程所执行的字节码的行号指示器。**字节码解释器工作时通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等功能都需要依赖这个计数器来完成。**

另外，**为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。**

**从上面的介绍中我们知道程序计数器主要有两个作用：**

1. 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

**注意：程序计数器是唯一一个不会出现 OutOfMemoryError 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。**



## 方法区

方法区与 Java 堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的==类信息（构造方法、接口定义）、常量、静态变量、即时编译器编译后的代码等数据，运行时的常量池存在方法区中==。（static、final、class）

==实例变量存在堆内存中，和方法区无关。==

虽然 Java 虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做 **Non-Heap（非堆）**，目的应该是与 Java 堆区分开来。

方法区也被称为永久代。很多人都会分不清方法区和永久代的关系



## 栈：数据结构

栈： 先进后出，后进先出（类似于子弹弹夹，先进弹夹的子弹，要等前面的子弹打出去，自己才能弹出去最后）

队列：先进先出（FIFO：first input first output）



main()方法最先执行，最后结束



栈内存：主管程序的运行，生命周期和线程同步

线程结束，栈内存也就释放

栈，不存在垃圾回收的问题（用完就弹出，线程结束后，栈就清空了）

栈： 8大基本类型 + 对象引用 + 实例方法



对象的初始化：

![image-20200612220326346](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612220326346.png)



## 三种JVM

* sun公司：  HopSpot
* BEA：   JRockit
* IBM：  J9 VM







## 堆(Heap)

一个JVM只有一个堆内存，并且堆大小可以调节



堆内存的三个区域

* 新生区（伊甸园区） Young
* 老年区     Old
* 永久区    



Java 虚拟机所管理的内存中最大的一块，Java 堆是所有线程共享的一块内存区域，在虚拟机启动时创建。**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。**

Java 堆是垃圾收集器管理的主要区域，因此也被称作**GC 堆（Garbage Collected Heap）**.从垃圾回收的角度，由于现在收集器基本都采用分代垃圾收集算法，所以 Java 堆还可以细分为：新生代和老年代：再细致一点有：Eden 空间、From Survivor、To Survivor 空间等。**进一步划分的目的是更好地回收内存，或者更快地分配内存。**

[![img](https://camo.githubusercontent.com/4012482f49926b35d8557b63952ee605fd259f62/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d33e5a086e7bb93e69e842e706e67)](https://camo.githubusercontent.com/4012482f49926b35d8557b63952ee605fd259f62/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d33e5a086e7bb93e69e842e706e67)

上图所示的 eden 区、s0 区、s1 区都属于新生代，tentired 区属于老年代。大部分情况，对象都会首先在 Eden 区域分配，在一次新生代垃圾回收后，如果对象还存活，则会进入 s0 或者 s1，并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)，当它的年龄增加到一定程度（默认为 15 岁），就会被晋升到老年代中。对象晋升到老年代的年龄阈值，可以通过参数 `-XX:MaxTenuringThreshold` 来设置。



GC回收主要在新生区和老年区

假设内存满了，OOM，堆内存不够



在JDK8以后，永久区替换成了元空间



### 新生区

* 对象：诞生和成长的地方，甚至死亡
  * 伊甸园（eden）：所有的对象都在eden区new
  * 幸存者区（s0，s1）：



### 永久区

这些区域是常驻内存的。原来存放jdk自身携带的对象。interface元数据，存储的是java运行时的一个环境或类信息（这个区域不存在垃圾回收！关闭VM就会释放这个区域的内存）

一个启动类，加载了大量第三方jar包，大量动态生成的反射类，不断加载，直到内存满。就会出现OOM（Out Of Memory）

* jdk1.6之前：永久代，常量池在方法区
* jdk1.7：永久代，都是慢慢退化了（去永久代），常量池在堆中
* jdk1.8之后：无永久代，常量池在元空间



```java
//  -Xms1024m -Xmx1024m -XX:+PrintGCDetails
public class Demo02 {
    public static void main(String[] args) {
        // 返回虚拟机使用的最大内存
        long max = Runtime.getRuntime().maxMemory();
        // 返回jvm初始化的总内存
        long total = Runtime.getRuntime().totalMemory();

        System.out.println("max="+(max/(double)1024/1024)+"MB");
        System.out.println("total="+(total/(double)1024/1024)+"MB");
    }
}
```



![image-20200612230120254](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612230120254.png)



![image-20200612225648710](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612225648710.png)



![image-20200612230008265](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612230008265.png)



* 测试：  -Xms9m -Xmx9m -XX:+PrintGCDetails

```java
//  -Xms9m -Xmx9m -XX:+PrintGCDetails
public class testJvm {
    public static void main(String[] args) {
        String str="jdisdjsaidjsaid";
        while(true){
            str+=str + new Random().nextInt(88888888) + new Random().nextInt(99999999);
        }
    }
}
```



![image-20200612233650934](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200612233650934.png)



## 元空间

逻辑上存在，物理上不存在堆中

JDK 1.8 的时候，方法区（HotSpot 的永久代）被彻底移除了（JDK1.7 就已经开始了），取而代之是元空间，元空间使用的是直接内存。



在一个项目中，突然出现OOM故障，如何排除

* 能看到代码第几行出错，内存快照工具，MAT，jprofiler
* Debug，一行行分析代码

MAT，jprofiler的作用

* 分析Dump内存文件，快速定位内存泄露
* 获得堆中的数据
* 获得大的对象



* 使用 jprofiler 测试 堆内存溢出
  * -XX:+HeapDumpOnOutOfMemoryError  堆内存溢出时，保存内存快照

```java
// -Xms9m -Xmx9m -XX:+HeapDumpOnOutOfMemoryError
public class TestJvm02 {
    byte[] array = new byte[1*1024*1024]; 

    public static void main(String[] args) {
        ArrayList<Object> list = new ArrayList<>();
        int counts = 0;

        try{
            while(true){
                list.add(new TestJvm02()); // 内存问题
                counts = counts + 1;
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```



![image-20200613013755481](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613013755481.png)





![image-20200613013232141](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613013232141.png)

![image-20200613013343197](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613013343197.png)



![image-20200613013424916](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613013424916.png)



![image-20200613013515399](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613013515399.png)



## GC:垃圾回收

JVM在进行垃圾回收的时候，大部分时候都是回收新生代

* 新生代
  * 辛存区（from【s0】，to【s1】）  from 和 to可以交替（可以这样理解，哪一个区回收后比另一个使用内存更少，那么这个区就是to，即==**谁空谁是to**==）
* 老年区



GC题：

* jvm的内存模型和分区，详细到每个区放什么
* 堆里的分区有哪些。说说他们的特定
  * eden，from，to，老年区

* GC算法有哪些

  * 标记清除

    * 回收时对存活的对象标记，清除掉没标记的对象

    * 该算法分为“标记”和“清除”阶段：首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。它是最基础的收集算法，后续的算法都是对其不足进行改进得到。这种垃圾收集算法会带来两个明显的问题：

    1. **效率问题**
    2. **空间问题（标记清除后会产生大量不连续的碎片）**

    * ​	
      * 优点：不需要额外空间
      * 缺点：两次扫描，严重浪费时间，会产生内存碎片
      *  	 

    [<img src="https://camo.githubusercontent.com/dc1f798e7c7f9aa9a3ab692db10a6b1788e5d505/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f36333730373238312e6a7067" alt="公众号" style="zoom:67%;" />](https://camo.githubusercontent.com/dc1f798e7c7f9aa9a3ab692db10a6b1788e5d505/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f36333730373238312e6a7067)

  * 标记整理（压缩）

    * 相比标记清除，它会再一次扫描，然后向一端移动存活的对象

    * 根据老年代的特点特出的一种标记算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象回收，而是让所有存活的对象向一端移动，然后直接清理掉端边界以外的内存。

      * 缺点：多了一次移动成本

      [![标记-整理算法 ](https://camo.githubusercontent.com/e5223ec7b2460498e1934c14eeaf969bafdcab59/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f39343035373034392e6a7067)](https://camo.githubusercontent.com/e5223ec7b2460498e1934c14eeaf969bafdcab59/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f39343035373034392e6a7067)

  * 复制算法

    * 从from复制到to，然后标记原来的from为to

    * 为了解决效率问题，“复制”收集算法出现了。它可以将内存分为大小相同的两块，每次使用其中的一块。当这一块的内存使用完后，就将还存活的对象复制到另一块去，然后再把使用的空间一次清理掉。这样就使每次的内存回收都是对内存区间的一半进行回收。

      * 好处：没有内存碎片
      * 坏处：浪费内存空间，多了一半空间永远是to，假设对象100%存活（极端）
      * 最佳使用场景：对象存活度较低的时候

      [<img src="https://camo.githubusercontent.com/94cfc5e1fbe9d49b3ed056d2943fd86dac1833a2/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f39303938343632342e6a7067" alt="公众号" style="zoom:67%;" />](https://camo.githubusercontent.com/94cfc5e1fbe9d49b3ed056d2943fd86dac1833a2/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f39303938343632342e6a7067)

  * 引用计数器



* 轻GC和重GC分别在什么时候发送 



## JMM

1.什么是JMM（**Java Memory Model**）

​	**Java内存模型**

* 可见性
* 原子性
* 有序性

2.它有什么作用

* CPU和缓存一致性。用于定义数据读写的规则（遵守这个协议）
* JMM定义了线程工作内存和主内存之间的抽象关系：线程之间的共享变量存储在主内存（Main Memory）中，每个线程都有一个私有的本地内存（Local Memory）
* 解决可见性问题：volilate

3.如何学习

　JMM对这八种指令的使用，制定了如下规则：

- - 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write
  - 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
  - 不允许一个线程将没有assign的数据从工作内存同步回主内存
  - 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作
  - 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
  - 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
  - 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量
  - 对一个变量进行unlock操作之前，必须把此变量同步回主内存

![image-20200613011638483](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200613011638483.png)