



# JUC

## 1.什么是JUC

- java.util.concurrent 工具包

![image-20200629031611229](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629031611229.png)



* **runnable** ：没有返回值，效率相比 Callable 相对较低

![image-20200629032124309](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629032124309.png)





## 2.线程和进程

### 2.1.线程、进程

**进程：**进程是一个**具有一定独立功能的程序关于某个数据集合的一次运行活动**。它是**操作系统动态执行的基本单元**，在传统的操作系统中，进程既是基本的分配单元，也是基本的执行单元。

**线程：**通常在一个进程中可以包含若干个线程，当然一个进程中至少有一个线程，不然没有存在的意义，线程可以利用进程所有拥有的资源。在引入线程的操作系统中，通常都是把**==进程==作为分配资源的基本单位**，而把**==线程==作为独立运行和独立调度的基本单位**，由于线程比进程小，基本上不拥有系统资源，故对它的调度所付出的开销就会小得多，能更高效的提高系统多个程序间并发执行的程度。



**白话；**

进程：就是操作系统中运行的一个程序，QQ.exe, music.exe， word.exe ，这就是多个进程

线程：每个进程中都存在一个或者多个线程，比如用typore写文章时，就会有一个线程默默帮你定时自动保存



* java默认有几个线程？

  2个    main 、 GC



* java真的能开启线程吗？

```java
    /**
     * Causes this thread to begin execution; the Java Virtual Machine
     * calls the <code>run</code> method of this thread.
     * <p>
     * The result is that two threads are running concurrently: the
     * current thread (which returns from the call to the
     * <code>start</code> method) and the other thread (which executes its
     * <code>run</code> method).
     * <p>
     * It is never legal to start a thread more than once.
     * In particular, a thread may not be restarted once it has completed
     * execution.
     *
     * @exception  IllegalThreadStateException  if the thread was already
     *               started.
     * @see        #run()
     * @see        #stop()
     */
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	// 本地方法，调用底层的C++  java无法直接操作硬件
    private native void start0();
```



### 2.2.并发、并行

并发编程：并发、并行

​		**并发和并行**是两个非常容易混淆的概念。它们都可以表示两个或多个任务一起执行，但是偏重点有点不同。并发偏重于多个任务交替执行，而多个任务之间有可能还是串行的。并发是逻辑上的同时发生（simultaneous），而并行是物理上的同时发生。然而并行的偏重点在于”同时执行”

​		严格意义上来说，并行的多个任务是真实的同时执行，而对于并发来说，这个过程只是交替的，一会运
行任务一,一会儿又运行任务二，系统会不停地在两者间切换。但对于外部观察者来说，即使多个任务是
串行并发的，也会造成是多个任务并行执行的错

​		实际上，如果系统内只有一个CPU，而现在而使用多线程或者多线程任务，那么真实环境中这些任务不可能真实并行的，毕竟一个CPU一次只能执行一条指令，这种情况下多线程或者多线程任务就是并发的，而不是并行，操作系统会不停的切换任务。真正的并行也只能够出现在拥有多个CPU的系统中（多核CPU）



* **并发的动机**：在计算能力恒定的情况下处理更多的任务, 就像我们的大脑, 计算能力相对恒定, 要在一天中处理更多的问题, 我们就必须具备多任务的能力. 现实工作中有很多事情可能会中断你的当前任务, 处理这种多任务的能力就是你的并发能力

* **并行的动机**：用更多的CPU核心更快的完成任务. 就像一个团队, 一个脑袋不够用了, 一个团队来一起处理一个任务

* 例子：

  * 你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也**不支持并行**
  * 你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你**支持并发** （不一定是
    同时的）
  * 你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你**支持并行**

  

**==并发编程的目标是充分的利用处理器的每一个核，以达到最高的处理性能==**



* 并发 （多线程同时操作一个资源） 多个任务交替执行
  * cpu一核  ： 模拟出来多条线程，快速交替
* 并行 （多个任务同时执行） 
  * cpu多核 ： 不同核的线程可以同时执行

```java
package com.guo.demo01;

public class Test01 {
    public static void main(String[] args) {
        // 获取cpu核数 （可用处理器）
        System.out.println(Runtime.getRuntime().availableProcessors());
    }
}
```





### 2.3.并发编程的本质

并发编程的目标是充分的利用处理器的每一个核，以达到最高的处理性能



### 2.4.线程有几个状态

```java
public enum State {
    // 新生
    NEW,

    // 运行
    RUNNABLE,

    // 阻塞
    BLOCKED,

    // 等待，死死地等
    WAITING,

    // 超时等待
    TIMED_WAITING,

    // 终止
    TERMINATED;
}
```



### 2.5.wait、sleep 区别

#### 1.来自不同的类

wait  ==》  Object

sleep ==》Thread

sleep是Thread的静态类方法，谁调用的谁去睡觉，即使在a线程里调用了b的sleep方法，实际上还是a去睡觉，要让b线程睡觉要在b的代码中调用sleep

![image-20200629040937439](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629040937439.png)



#### 2.关于锁的释放

wait 会释放锁

sleep 睡觉了，不会释放



​		sleep是线程被调用时，占着cpu去睡觉，其他线程不能占用cpu，os认为该线程正在工作，不会让出系统资源，wait是进入等待池等待，让出系统资源，其他线程可以占用cpu

​		sleep(100L)是占用cpu，线程休眠100毫秒，其他进程不能再占用cpu资源，wait（100L）是进入等待池中等待，交出cpu等系统资源供其他进程使用，在这100毫秒中，该线程可以被其他线程notify，但不同的是其他在等待池中的线程不被notify不会出来，但这个线程在等待100毫秒后会自动进入就绪队列等待系统分配资源，换句话说，sleep（100）在100毫秒后肯定会运行，但wait在100毫秒后还有等待os调用分配资源，所以wait100的停止运行时间是不确定的，但至少是100毫秒



#### 3.使用范围不同

wait，notify和notifyAll： 必须在同步代码块/同步方法中

sleep ：可以在任何地方睡



#### 4.是否需要捕获异常

wait，notify和notifyAll ：不需要捕获异常

sleep ：必须要捕获异常 （会发生超时等待）



## 3.Lock锁（重点）

### 3.1.传统的 synchronized

```java
package com.guo.demo01;

/**
 *  真正的多线程开发
 *  线程就是一个单独的资源类，没有任何附属操作
 *
 *    1. 属性、方法
 */
public class SaleTicketDemo01 {
    public static void main(String[] args) {
        // 并发 ： 多线程操作同一个资源类，把资源丢入线程
        Ticket ticket = new Ticket();

        // @FunctionalInterface     函数式接口
        // 可new一个匿名内部类  接口不可以实例化，但是可以使用匿名内部类来实现它
        // jdk1.8   lambda表达式 简化  ()=>{}   () 是从方法体开始  (方法的参数)->{  代码 }
        new Thread(()->{
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        } ,"A").start();
        new Thread(()->{
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        } ,"B").start();
        new Thread(()->{
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        } ,"C").start();

    }
}

// 资源类  OOP
class Ticket {
    // 属性 、 方法
    private int number = 30;

    // 卖票的方式
    // synchronized   本质：队列、锁
    public synchronized void sale(){
        if( number > 0 ) {
            System.out.println(Thread.currentThread().getName() + "卖出来第" + (number--) + "张票，剩余："+number + "张");
        }
    }
}
```



### 3.2.Lock接口

#### 介绍

![image-20200629121752771](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629121752771.png)

![image-20200629121546103](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629121546103.png)



#### 公平锁、非公平锁

* 公平锁 ： 很公平，可以先来后到
* **非公平锁： 很不公平，可以插队 （默认）**

![image-20200629122409657](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629122409657.png)



#### 测试

```java
package com.guo.demo01;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 *  真正的多线程开发
 *  线程就是一个单独的资源类，没有任何附属操作
 *
 *    1. 属性、方法
 */
public class SaleTicketDemo02 {
    public static void main(String[] args) {
        // 并发 ： 多线程操作同一个资源类，把资源丢入线程
        Ticket2 ticket = new Ticket2();

        // @FunctionalInterface     函数式接口
        // 可new一个匿名内部类  接口不可以实例化，但是可以使用匿名内部类来实现它
        // jdk1.8   lambda表达式 简化  ()=>{}   () 是从方法体开始  (方法的参数)->{  代码 }
        new Thread(()->{ for (int i = 0; i < 40 ; i++) ticket.sale(); } ,"A").start();
        new Thread(()->{ for (int i = 0; i < 40 ; i++) ticket.sale(); } ,"B").start();
        new Thread(()->{ for (int i = 0; i < 40 ; i++) ticket.sale(); } ,"C").start();

    }
}

// 资源类  OOP
//  Lock三步
// 1. new ReentrantLock();
// 2. lock.lock();  // 加锁
// 3. finally ==》 lock.unlock();  // 解锁
class Ticket2 {
    // 属性 、 方法
    private int number = 30;

    Lock lock = new ReentrantLock();

    public void sale(){

        lock.lock();  // 加锁

        try {
            // 业务代码
            if( number > 0 ) {
                System.out.println(Thread.currentThread().getName() + "卖出来第" + (number--) + "张票，剩余："+number + "张");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();  // 解锁
        }
    }
}
```



### 3.3.synchronize 和 Lock 区别

1.synchronize 是内置的java关键字 

​	Lock 是一个Java类

2.synchronize 无法判断锁的状态，  

​	Lock 可以判断是否获取到了锁

3.synchronize 会自动释放锁 (a 线程执行完同步代码会释放锁 ；b 线程执行过程中发生异常会释放锁)， 

​	Lock 需在finally中手工释放锁【unlock（）方法释放锁】，否则容易造成==线程**死锁**==

4.synchronize  中两个线程1和线程2，如果当前线程1获得锁，线程2线程等待。如果线程1阻塞，线程2则会一直等待下去

​	Lock锁 在这种情况下不一定会等待下去，如果尝试获取不到锁，线程可以不用一直等待就结束了 【tryLock（）】

​	手动

5.synchronize  的锁可重入、不可中断、非公平

​	Lock 可重入锁、可以判断锁、锁公平与否可以设置

6.synchronize  适合锁**代码少量**的同步问题

​	Lock	适合锁**大量同步的代码**的同步问题



![image-20200629141207500](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629141207500.png)



## 4.生产者和消费者

### 4.1.synchronize版本

#### 1.使用 if 判断  （代码）

```java
package com.guo.productconsumer;
/**
 * 题目：现在四个线程，可以操作初始值为0的一个变量
 * 实现两个线程对该变量 + 1，两个线程对该变量 -1
 * 实现交替10次
 *
 * 诀窍：
 * 1. 高内聚低耦合的前提下，线程操作资源类
 * 2. 判断 、干活、通知
 * 3. 多线程交互中，必须要防止多线程的虚假唤醒，也即（判断不能用if，只能用while）
 */
public class TestWithSynchronize {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                ;
            }
        } ,"A").start();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.decremnet();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } ,"B").start();
    }
}

// 判断等待，业务，通知
//  数字  资源类
class Data {

    private int number = 0;

    //+1  生产者
    public synchronized void increment () throws InterruptedException {
        if (number != 0) {
            // 等待
            this.wait();
        }
        number ++;
        System.out.println(Thread.currentThread().getName() + "==>" + number);
        // 通知其他线程，生产者 +1 完毕了
        this.notifyAll();
    }

    // -1  消费者
    public synchronized void decremnet () throws InterruptedException {
        if(number == 0) {
            // 等待
            this.wait();
        }
        number --;
        System.out.println(Thread.currentThread().getName() + "==>" + number);
        // 通知其他线程，消费者 -1 完毕了
        this.notifyAll();
    }

}
```



#### 2.存在的问题

* 假如现在有 A、B、C、D 四个线程 ，使用 if 判断回出现 **虚假唤醒** 

![image-20200629153118241](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629153118241.png)



* 需要把 if  改为  while 判断
  * 查看官方文档

![image-20200629152437992](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629152437992.png)



#### 3.使用 while 判断

![image-20200629153305352](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629153305352.png)

![image-20200629153333214](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629153333214.png)





### 4.2.JUC版本

#### 1.通过 Lock 找到 Condition

*  Lock 的 Condition 方法

![image-20200629160430022](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629160430022.png)



* 关于Condition 的使用

![image-20200629155416989](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629155416989.png)

![image-20200629155552720](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629155552720.png)

![image-20200629155140484](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629155140484.png)

![image-20200629155631697](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629155631697.png)



#### 2.对比 synchronize

![image-20200629155724407](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629155724407.png)

#### 3.TestWithJUC 代码

```java
package com.guo.productconsumer;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class TestWithJUC {
    public static void main(String[] args) {
        Data2 data = new Data2();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } ,"A").start();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.decremnet();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } ,"B").start();


        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } ,"C").start();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                try {
                    data.decremnet();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } ,"D").start();

    }
}

// 判断等待，业务，通知
//  数字  资源类
class Data2 {

    private int number = 0;

    Lock lock = new ReentrantLock();
    //  Condition 的使用
    //    condition.await();      // 等待
    //    condition.signalAll();  // 唤醒全部
    Condition condition = lock.newCondition();

    //+1  生产者
    public void increment () throws InterruptedException {
        lock.lock();    // 加锁
        try {
            // 业务代码
            while (number != 0) {
                // 等待
                condition.await();
            }
            number ++;
            System.out.println(Thread.currentThread().getName() + "==>" + number);
            // 通知其他线程，生产者 +1 完毕了
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();  //释放锁
        }

    }

    // -1  消费者
    public void decremnet () throws InterruptedException {
        lock.lock();    // 加锁
        try {
            // 业务代码
            while (number == 0) {
                // 等待
                condition.await();
            }
            number --;
            System.out.println(Thread.currentThread().getName() + "==>" + number);
            // 通知其他线程，消费者 -1 完毕了
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();  //释放锁
        }
    }

}
```



#### 4.Condition的优势

* Condition 精准的 **通知** 和 **唤醒线程**

* 测试发现执行 TestWithJUC  的输出是随机的，我们想让他有序执行。Condition可以帮我们做到
  * 接下来会测试 Condition实现精准通知、唤醒

![image-20200629162021459](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629162021459.png)



#### 5.Condition实现精准通知、唤醒 （代码）

```java
package com.guo.productconsumer;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 *  A 执行完 调用 B
 *  B 执行完 调用 C
 *  C 执行完 调用 A
 */
public class TestWithJUCPrecision {
    public static void main(String[] args) {
        Data3 data = new Data3();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                data.printA();
            }
        } , "A" ).start();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                data.printB();
            }
        } , "B" ).start();

        new Thread(() -> {
            for (int i = 0; i < 10 ; i++) {
                data.printC();
            }
        } , "C" ).start();

    }
}

// 资源类  Lock
class Data3 {

    private Lock lock = new ReentrantLock();
    private Condition conditionA = lock.newCondition();
    private Condition conditionB = lock.newCondition();
    private Condition conditionC = lock.newCondition();
    // 标志位
    // A => 1
    // B => 2
    // C => 3
    private int number = 1;

    public void printA () {
        lock.lock();
        try {
            // 业务代码
            // 判断 --》 执行 --》 通知
            while (number != 1) {
                conditionA.await();
            }
            System.out.println(Thread.currentThread().getName() + " is executing(AAAAA)");
            // 唤醒 B
            number = 2;
            conditionB.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printB () {
        lock.lock();
        try {
            // 业务代码
            // 判断 --》 执行 --》 通知
            while (number != 2) {
                conditionB.await();
            }
            System.out.println(Thread.currentThread().getName() + " is executing(BBBBB)");
            // 唤醒 C
            number = 3;
            conditionC.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printC () {
        lock.lock();
        try {
            // 业务代码
            // 判断 --》 执行 --》 通知
            while (number != 3) {
                conditionC.await();
            }
            System.out.println(Thread.currentThread().getName() + " is executing(CCCCC)");
            // 唤醒 A
            number = 1;
            conditionA.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

![image-20200629165942149](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629165942149.png)



## 5.深刻理解锁：8锁现象

==8锁是关于锁的8个问题==

标准情况下，两个线程谁先打印    Send sms？  Phone call？

如果让 sendSms() 延迟2秒呢？

### 问题1、2

* 问题1：标准情况下，两个线程谁先打印    Send sms？  Phone call？
* 问题2：如果让 sendSms() 延迟2秒呢？

```java
package com.guo.lock8;

import java.util.concurrent.TimeUnit;
 /**
 *  关于锁的8个问题
 *  1. 标准情况下，两个线程谁先打印    Send sms？  Phone call？
 *              1 Send sms --> 2 Phone call
 *  2. 如果让 sendSms() 延迟2秒呢？
 *              结果是一样的，还是   1 Send sms --> 2 Phone call
 *  原因是：锁的对象是方法的调用者  当Phone对象调用 sendSms() 方法后，其他线程需要等它释放锁
 */
public class Test01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendSms();
        },"A").start();
        new Thread(() -> {
            phone.call();
        },"B").start();
    }
}

class Phone {
    // 锁的对象是方法的调用者
    // 两个方法用的是同一个锁 （Phone对象） ==》 谁先拿到谁执行
    public synchronized  void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Send sms");
    }

    public synchronized void call() {
        System.out.println("Phone call");
    }
}
```



### 问题3

增加了**一个普通方法**sayHello()  先执行 sendSms 还是 sayHello ？

注意：这里依然让 sendSms() 延迟2秒

```java
package com.guo.lock8;

import java.util.concurrent.TimeUnit;

/**
 *  3. 增加了一个普通方法sayHello()  先执行 sendSms 还是 sayHello ？
 *     注意：这里依然让 sendSms() 延迟2秒
 *          1 say hello --> 2 Send sms
 *
 */
public class Test02 {
    public static void main(String[] args) {
        Phone2 phone = new Phone2();

        new Thread(() -> {
            phone.sendSms();
        },"A").start();
        new Thread(() -> {
            phone.sayHello();
        },"B").start();
    }
}

class Phone2 {
    // 锁的对象是方法的调用者
    // 两个方法用的是同一个锁 （Phone对象） ==》 谁先拿到谁执行
    public synchronized  void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Send sms");
    }

    public synchronized void call() {
        System.out.println("Phone call");
    }

    // 这里没有锁
    public void sayHello() {
        System.out.println("say hello");
    }
}
```



### 问题4

**两个对象** **两个同步方法**     先执行 Send sms？  Phone call ？

注意：这里依然让 sendSms() 延迟2秒

```java
package com.guo.lock8;

import java.util.concurrent.TimeUnit;

/**
 *  4 两个对象 两个同步方法     先执行 Send sms？  Phone call？
 *    注意：这里依然让 sendSms() 延迟2秒
 *         1 Phone call --> 2 Send sms
 */
public class Test03 {
    public static void main(String[] args) {
        // 两个对象，代表两个调用者
        Phone3 phoneA = new Phone3();
        Phone3 phoneB = new Phone3();

        new Thread(() -> {
            phoneA.sendSms();
        },"A").start();
        new Thread(() -> {
            phoneB.call();
        },"B").start();
    }
}

class Phone3 {
    // 锁的对象是方法的调用者
    // 两个方法用的是同一个锁 （Phone对象） ==》 谁先拿到谁执行
    public synchronized  void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Send sms");
    }

    public synchronized void call() {
        System.out.println("Phone call");
    }
}
```



### 问题5、6

问题5：增加了**两个静态的同步方法**，只有**一个对象**     先执行 Send sms？  Phone call？

问题6：同样是**两个静态的同步方法**，但是有**两个对象**   先执行 Send sms？  Phone call？

```java
package com.guo.lock8;

import java.util.concurrent.TimeUnit;

/**
 *  5. 增加了两个静态的同步方法，只有一个对象     先执行 Send sms？  Phone call？
 *     注意：这里依然让 sendSms() 延迟2秒
 *          类一加载就有了   锁的是class      1 Send sms --> 2 Phone call
 *  
 *  6. 同样是两个静态的同步方法，但是有两个对象     先执行 Send sms？  Phone call？
 *          结果依然是      1 Send sms --> 2 Phone call
 *     原因：因为static 锁的是 class对象，而 Phone4 两个对象的 class类模板只有一个
 *
 *		当然，这里有点偷懒，测试问题5 直接  Phone.sendSms()调用就好了
 */
public class Test04 {
    public static void main(String[] args) {
        //  测试 问题5
//        Phone4 phone = new Phone4();
//
//        new Thread(() -> {
//            phone.sendSms();
//        },"A").start();
//        new Thread(() -> {
//            phone.call();
//        },"B").start();

        //  测试 问题6
        // 两个对象的 class类模板只有一个， 锁的是class
        Phone4 phoneA = new Phone4();
        Phone4 phoneB = new Phone4();

        new Thread(() -> {
            phoneA.sendSms();
        },"A").start();
        new Thread(() -> {
            phoneB.call();
        },"B").start();
    }
}

// Phone4 只有唯一的一个 Class 对象
class Phone4 {
    // synchronized 锁的对象是方法的调用者
    //     两个方法用的是同一个锁 （Phone对象） ==》 谁先拿到谁执行
    // static 静态方法
    //     类一加载就有了   锁的是class
    public synchronized static void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Send sms");
    }

    public synchronized static void call() {
        System.out.println("Phone call");
    }
}
```



### 问题7、8

问题7：一个**静态的同步**方法，一个**普通的同步**方法， 只有**一个对象**     先执行 Send sms？  Phone call？

问题8：一个**静态的同步**方法，一个**普通的同步**方法， **两个对象**     先执行 Send sms？  Phone call？

```java
package com.guo.lock8;

import java.util.concurrent.TimeUnit;

/**
 *  7. 一个静态的同步方法，一个普通的同步方法， 只有一个对象     先执行 Send sms？  Phone call？
 *     注意：这里依然让 sendSms() 延迟2秒
 *                1 Phone call --> 2 Send sms
 *     类是先加载进来的，main是从上到下执行的，线程A先获取到锁，线程B后获取到锁，具体哪个先执行看cpu调度，但是这里线程A睡了2秒
 *     所以结果是 线程B 先执行
 *
 *  8. 一个静态的同步方法，一个普通的同步方法， 两个对象     先执行 Send sms？  Phone call？
 *          结果和问题7是一样的     1 Phone call --> 2 Send sms
 *     new 两个对象，同样是一个 锁的是class ， 另一个 锁的是对象
 *
 */
public class Test05 {
    public static void main(String[] args) {
        // 问题 7
        // 调用静态的同步方法    class类模板只有一个， 锁的是class
        // 调用普通的同步方法    锁的是对象
//        Phone5 phone = new Phone5();
//
//        new Thread(() -> {
//            phone.sendSms();
//        },"A").start();
//        new Thread(() -> {
//            phone.call();
//        },"B").start();

        // 问题 8
        Phone5 phoneA = new Phone5();
        Phone5 phoneB = new Phone5();

        new Thread(() -> {
            phoneA.sendSms();
        },"A").start();
        new Thread(() -> {
            phoneB.call();
        },"B").start();
    }
}

// Phone5 只有唯一的一个 Class 对象
class Phone5 {
    // 静态的同步方法  锁的是class类模板 （类锁）
    public synchronized static void sendSms() {
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Send sms");
    }

    // 普通的同步方法  锁的是调用者 （对象锁）
    public synchronized void call() {
        System.out.println("Phone call");
    }
}
```



### 小结

new：  this 具体的一个phone对象

static： class  唯一的一个模板



## 6.集合类不安全 （重点）

### 6.1.List不安全

#### 1.测试代码

```java
package com.guo.unsale_collection;

import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

// java.util.ConcurrentModificationException    并发修改异常
// 导致原因：add 方法没有加锁
public class ListTest {
    public static void main(String[] args) {
        // 并发下 ArrayList 不安全
        /**
         *  解决方案：
         *  1. List<String> list = new Vector<>();
         *  2. List<String> list = Collections.synchronizedList(new ArrayList<>());
         *  3. List<String> list = new CopyOnWriteArrayList<>();
         */

        /**
         *  CopyOnWriteArrayList
         *  CopyOnWrite 写入时复制   COW 计算机程序设计领域的一种优化策略
         *  多个线程调用的时候  list   读取是固定的  写入（可能覆盖）
         *  在写入的时候避免覆盖，造成数据问题
         *  读写分离
         *  CopyOnWriteArrayList [Lock] 相比 Vector [synchronized] 效率更高
         */
        List<String> list = new CopyOnWriteArrayList<>();

        for (int i = 0; i < 10 ; i++) {
            new Thread(()->{
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```



#### 2.写入时复制（CopyOnWrite）思想

写入时复制（CopyOnWrite，简称COW）思想是计算机程序设计领域中的一种优化策略。其核心思想是，如果有多个调用者（Callers）同时请求相同的资源（如内存或者是磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者试图修改资源内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是透明的（transparently）。此做法主要的优点是如果调用者没有修改资源，就不会有副本（private copy）被创建，因此多个调用者只是读取操作时可以共享同一份资源



#### 3.读写分离

写时复制出一个新的数组，完成插入、修改或者移除操作后将新数组赋值给array



#### 4.CopyOnWriteArrayList为什么并发安全且性能比Vector好

我知道Vector是增删改查方法都加了synchronized，保证同步，但是每个方法执行的时候都要去获得锁，性能就会大大下降，而CopyOnWriteArrayList 只是在增删改上加锁，但是读不加锁，在读方面的性能就好于Vector，CopyOnWriteArrayList支持读多写少的并发情况



### 6.2.Set不安全

#### 1.测试代码

```java
package com.guo.unsale_collection;

import java.util.Collections;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;

/**
 *  和 ArrayList 出现一样的问题     java.util.ConcurrentModificationException
 *
 *  解决方案：
 *  1. Set<String> set = Collections.synchronizedSet(new HashSet<>());
 *  2. Set<String> set = new CopyOnWriteArraySet<>();
 */
public class SetTest {
    public static void main(String[] args) {
//        Set<String> set = new HashSet<>();
//        Set<String> set = Collections.synchronizedSet(new HashSet<>());
        Set<String> set = new CopyOnWriteArraySet<>();

        for (int i = 0; i < 30 ; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }

    }
}
```



#### 2.HashSet底层

```java
	public HashSet() {
		map = new HashMap<>();
	}

	// add  HashSet本质就是 HashMap 的 key（key无法重复） 
	// PRESENT 是一个常量
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }

   private static final Object PRESENT = new Object();
```

![image-20200629202829461](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629202829461.png)

![image-20200629203058679](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629203058679.png)



### 3.Map不安全

#### 1.HashMap初始化

默认等价于什么  new HashMap<>(16,0.75)

![image-20200629204827056](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629204827056.png)



#### 2.测试代码

```java
package com.guo.unsale_collection;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

// java.util.ConcurrentModificationException
public class MapTest {
    public static void main(String[] args) {
        // 工作中不用 HashMap
        // 默认等价于什么  new HashMap<>(16,0.75);
//        Map<String, String> map = new HashMap<>();
        Map<String, String> map = new ConcurrentHashMap<>();

        for (int i = 0; i < 30 ; i++) {
            new Thread(()->{
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0, 5));
                System.out.println(map);
            },String.valueOf(i)).start();
        }
    }
}
```



## 7.Callable（简单）

### 7.1.简介

1.Callable 可以有返回值

2.Callable 可以抛出异常

3.Callable 的方法不同 call( )   ==> 相当于Runnable中的 run( ) 

![image-20200629213854593](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200629213854593.png)



### 7.2.Runnable与Callable的关系

* **FutureTask实现Runnable**

![image-20200630004815794](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630004815794.png)



* **FutureTask 调用 Callable**

![image-20200630005228419](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630005228419.png)



### 7.3.测试代码

```java
package com.guo.callable;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        new Thread(new Runnable()).start();
//        new Thread(new FutureTask<V>()).start();
//        new Thread(new FutureTask<V>(Callable)).start();

//        new Thread(new FutureTask<Integer>(new MyThread()){
//
//        },"A").start();

        MyThread myThread = new MyThread();
        // FutureTask 实现了 Runnable ，FutureTask 能调用 Callable
        FutureTask futureTask = new FutureTask(myThread);

        new Thread(futureTask,"A").start();

        // 这个 get方法 可能会产生阻塞 需要把它放在最后面  或者使用异步通信来处理
        Integer o = (Integer) futureTask.get();
        System.out.println(o);
    }
}

class MyThread implements Callable {

    @Override
    public Integer call() throws Exception {
        System.out.println("call()");
        return 1024;
    }
}
```

![image-20200630011022338](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630011022338.png)



### 7.4.开多个线程

* **Callable返回结果会被缓存**

![image-20200630011257703](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630011257703.png)

![image-20200630011305667](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630011305667.png)



## 8.常用辅助类（必会）

### 8.1.CountDownLatch

- 减法计数器

![image-20200630011734390](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630011734390.png)



#### 测试代码

* 其他线程调用CountDown方法会将计数器减1（调用CountDown方法的线程不会阻塞）
* 当计数器变为0时，await 方法阻塞的线程会被唤醒，继续执行

```java
// 减法计数器
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        // 倒计时 总数是 5
        CountDownLatch countDownLatch = new CountDownLatch(5);
        for (int i = 1; i <= 5  ; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + " go out");
                countDownLatch.countDown(); // 数量 -1
            },String.valueOf(i)).start();
        }
        
		// 阻塞等待计数器归零   归零后 await 方法阻塞的线程会被唤醒，继续执行
        countDownLatch.await();

        System.out.println("close door");
    }
}
```



### 8.2.CyclicBarrier

- 加法计数器

![image-20200630025439331](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630025439331.png)



#### 测试代码

```java
package com.guo.assistance;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 *  集齐七颗龙珠召唤神龙
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) {

        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, ()->{
            System.out.println("召唤成龙成功");
        });

        for (int i = 1; i <= 7 ; i++) {
            // lambda怎么操作到i
            final int temp = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"收集了第"+temp+"颗龙珠");

                try {
                    cyclicBarrier.await();      // 每次+1  等待 CyclicBarrier 变成7
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```



### 8.3.Semaphore

* **Semaphore ：信号量**
  * Semaphore semaphore = new Semaphore(3);   允许运行的线程数量为 3   [可以理解为：停车位]
  * acquire( )    获得    如果acquire( )未释放的执行数量满了 [ 达到了构造方法中定义的数量 ]  ，等待，直到被释放为止
    * 当一个线程调用 acquire 操作时，他要么通过成功获取信号量（信号量-1）
    * 要么一直等下去，直到有线程释放信号量，或超时
  * release( )    释放
    * 实际上会将信号量的值 + 1，然后唤醒等待的线程



- **作用**
  - 多个共享资源互斥的使用
  - 并发限流，控制最大的线程数

![image-20200630031907352](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630031907352.png)



#### 测试代码

```java
package com.guo.assistance;

import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreDemo {
    public static void main(String[] args) {
        // 允许运行的线程数量为 3   [可以理解为：停车位]
        // 使用场景： 限流
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 6 ; i++) {
            new Thread(()->{
                try {
                    // acquire()  获得
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName() + "离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    // release() 释放
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

![image-20200630033329581](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630033329581.png)



## 9.读写锁

### ReadWriteLock

 *  ReadWriteLock
     *  读-读  可以共存
     *  读-写  不能共存
     *  写-写  不能共存



* 独占锁 （写锁） 一次只能被一个线程占有

 *  共享锁 （读锁） 多个线程可以同时占有





![image-20200630034546100](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630034546100.png)



### 测试代码

```java
package com.guo.readwritelock;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 *  独占锁 （写锁） 一次只能被一个线程占有
 *  共享锁 （读锁） 多个线程可以同时占有
 * 
 *  ReadWriteLock
 *  读-读  可以共存
 *  读-写  不能共存
 *  写-写  不能共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
//        MyCache myCache = new MyCache();
        MyCacheLock  myCache = new MyCacheLock();

        // 写入
        for (int i = 1; i <= 5 ; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.put(temp+"",temp+"");
            },String.valueOf(i)).start();
        }

        // 读取
        for (int i = 1; i <= 5 ; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.get(String.valueOf(temp));
            },String.valueOf(i)).start();
        }
    }
}

/**
 *  加锁 的自定义缓存
 */
class MyCacheLock {
    private volatile Map<String,Object> map = new HashMap<>();
    // 读写锁  更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    // 存  写入的时候，只希望同时只有一个线程 写
    public void put(String key,Object value) {
        // 写锁 获得锁
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"写入"+key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName()+"写入OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 写锁 释放锁
            readWriteLock.writeLock().unlock();
        }
    }

    // 取  读的时候，允许所有线程 读
    public void get(String key) {
        // 加读锁之后  正在读的时候就不能写，而多个线程可以同时读
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"读取"+key);
            Object o = map.get(key);
            System.out.println(Thread.currentThread().getName()+"读取OK,读到的值为"+o);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}

/**
 *  自定义缓存	   不加锁，可能某个线程正在写，未完成就被其他线程插入了
 */
class MyCache {
    private volatile Map<String,Object> map = new HashMap<>();

    // 存  写的过程
    public void put(String key,Object value) {
        System.out.println(Thread.currentThread().getName()+"写入"+key);
        map.put(key, value);
        System.out.println(Thread.currentThread().getName()+"写入完成");
    }

    // 取  读的过程
    public void get(String key) {
        System.out.println(Thread.currentThread().getName()+"读取"+key);
        Object o = map.get(key);
        System.out.println(Thread.currentThread().getName()+"读取完成");
    }
}
```



## 10.阻塞队列

![image-20200630172446948](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630172446948.png)

![image-20200630174557313](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630174557313.png)

![image-20200630175405256](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630175405256.png)



### 10.1.简介

**阻塞：必须要阻塞、不得不阻塞**

阻塞队列是一个队列，在数据结构中起的作用如下图

![image-20200630175629520](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630175629520.png)

* 当队列是空的，从队列中==获取==元素的操作将会被阻塞
* 当队列是满的，从队列中==添加==元素的操作将会被阻塞



* 试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素
* 试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多个元素，使队列变得空闲起来并后续新增



### 10.2阻塞队列的用处

* 在多线程领域：所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤起
* 为什么需要 BlockingQueue？
  * 好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这**一切BlockingQueue 都给你一手包办**了
  * 在 concurrent 包发布以前，在多线程环境下，我们每个程序员都必须自己去控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度



### 10.3.接口架构图

![image-20200630180019995](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630180019995.png)



* ==ArrayBlockingQueue：由**数组**结构组成的有界阻塞队列==
* ==LinkedBlockingQueue：由**链表**结构组成的有界（默认值为：integer.MAX_VALUE）阻塞队列==
* PriorityBlockingQueue：支持优先级排序的无界阻塞队列
* DelayQueue：使用优先级队列实现的延迟无界阻塞队列
* ==SynchronousQueue：不存储元素的阻塞队列，也即单个元素的队列==
* LinkedTransferQueue：由链表组成的无界阻塞队列
* LinkedBlockingDeque：由链表组成的双向阻塞队列





### 10.4.BlockingQuene  API的使用

* 4组API
  * 抛出异常
  * 不会抛出异常
  * 阻塞等待
  * 超时等待

| 方式           | 抛出异常    | 有返回值，不抛出异常 | 等待阻塞，一直阻塞 | 等待阻塞，超时退出      |
| -------------- | ----------- | -------------------- | ------------------ | ----------------------- |
| 插入           | add（e）    | offer（e）           | put（e）           | offer(e,Timeout, unit); |
| 移除           | remove（）  | poll（）             | take（）           | poll（Timeout, unit）   |
| 检查队列首元素 | element（） | peek（）             |                    |                         |



####   1.抛出异常

```java
package com.guo.blocking_quene;

import java.util.concurrent.ArrayBlockingQueue;

public class BlockingQueneTest {
    public static void main(String[] args) {
        test01();
    }

    /**
     *  抛出异常
     */
    public static void test01() {
        // 参数是队列大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));
        System.out.println("========= 查看队首元素 blockingQueue.element() =========");
        System.out.println(blockingQueue.element());
        System.out.println("========================================================");
        // Exception in thread "main" java.lang.IllegalStateException: Queue full
        // System.out.println(blockingQueue.add("d"));

        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        // Exception in thread "main" java.util.NoSuchElementException
        // System.out.println(blockingQueue.remove());
    }

}
```

![image-20200630182930414](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630182930414.png)



#### 2.有返回值，不抛出异常

```java
/**
 *  有返回值，不抛出异常
 */
public static void test02() {
    // 参数是队列大小
    ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

    System.out.println(blockingQueue.offer("a"));
    System.out.println(blockingQueue.offer("b"));
    System.out.println(blockingQueue.offer("c"));
    // 返回 false
    System.out.println(blockingQueue.offer("d"));

    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    // 返回 null
    System.out.println(blockingQueue.poll());
}
```

![image-20200630184251212](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630184251212.png)



#### 3.等待阻塞，一直阻塞

```java
/**
 *  等待阻塞，一直阻塞
 */
public static void test03() throws InterruptedException {
    // 参数是队列大小
    ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);


    blockingQueue.put("a");
    blockingQueue.put("b");
    blockingQueue.put("c");
    // 队列没位置了   一直阻塞
    // blockingQueue.put("d");

    System.out.println(blockingQueue.take());
    System.out.println(blockingQueue.take());
    System.out.println(blockingQueue.take());
}
```



#### 4.等待阻塞，超时退出

```java
/**
 *  等待阻塞，超时退出
 */
public static void test04() throws InterruptedException {
    // 参数是队列大小
    ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);


    System.out.println(blockingQueue.offer("a"));
    System.out.println(blockingQueue.offer("b"));
    System.out.println(blockingQueue.offer("c"));
    // 等待阻塞 5 秒  时间超过 2秒 就退出
    blockingQueue.offer("d", 5, TimeUnit.SECONDS);

    System.out.println("----------------------------");

    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    System.out.println(blockingQueue.poll());
    // 等待阻塞 2 秒  时间超过 2秒 就退出
    blockingQueue.poll(2, TimeUnit.SECONDS);
}
```



### 10.5.同步队列 SynchronousQueue

进去一个元素，必须等待取出来之后，才能再往里边放一个元素 （队列容量为1）

put、take

```java
package com.guo.quene;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

/**
 *  同步队列
 *  和其他的BlockingQuene 不同  SynchronousQueue 本身不存储元素
 *  put 一个元素进去，必须先 take 取出来  否则不能再次 put 元素进去
 */
public class SynchronousQueueTest {
    public static void main(String[] args) throws InterruptedException {
//        SynchronousQueue<String> synchronousQueue = new SynchronousQueue<>(); // 同步队列
        BlockingQueue<String> blockingQueue = new SynchronousQueue<>();  // 同步队列

        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName()+" put 1");
                blockingQueue.put("1");
                System.out.println(Thread.currentThread().getName()+" put 2");
                blockingQueue.put("2");
                System.out.println(Thread.currentThread().getName()+" put 3");
                blockingQueue.put("3");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+" getValue: "+blockingQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+" getValue: "+blockingQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+" getValue: "+blockingQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

![image-20200630195220095](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630195220095.png)



## 11.线程池（重点）

线程池： 三大方法、7大参数、4种拒绝策略

### 11.1.池化技术

* 程序运行的本质：占用系统的资源！
  * 优化资源的使用 （不停地创建、销毁十分浪费资源）  ==》 池化技术
    * 线程池、连接池、内存池、对象池
    * 资源池事先准备好一些资源，有人要用就来拿，用完资源就归还资源池
    * 好处：
      * 1.降低资源的消耗
      * 2.提高响应的速度
      * 3.方便管理
      * ==线程复用，可以控制最大的并发数、管理线程==



#### 1.程序运行的本质

程序的运行，其本质上，是对系统资源(CPU、内存、磁盘、网络等等)的使用。如何高效的使用这些资源是我们编程优化演进的一个方向。今天说的线程池就是一种对CPU利用的优化手段

通过学习线程池原理，明白所有池化技术的基本设计思路。遇到其他相似问题可以解决



#### 2.池化技术

* 前面提到一个名词——池化技术，那么到底什么是池化技术呢 ?
  * 池化技术简单点来说，就是提前保存大量的资源，以备不时之需。在机器资源有限的情况下，使用池化技术可以大大的提高资源的利用率，提升性能等

* 在编程领域，比较典型的池化技术有：**线程池、连接池、内存池、对象池**等
  * 其中比较简单的线程池的实现原理，希望读者们可以举一反三，通过对线程池的理解，学习并掌握所有编程中池化技术的底层原理
  * 我们通过创建一个线程对象，并且实现Runnable接口就可以实现一个简单的线程。可以利用上多核CPU。当一个任务结束，当前线程就接收
  * 但很多时候，我们不止会执行一个任务。如果每次都是如此的创建线程 --> 执行任务 --> 销毁线程，会造成很大的性能开销
  * 那能否一个线程创建后，**执行完一个任务后，又去执行另一个任务，而不是销毁**。==这就是线程池==
  * 这也就是池化技术的思想，通过预先创建好多个线程，放在池中，这样可以在需要使用线程的时候直接获取，避免多次重复创建、销毁带来的开销



#### 3.线程池的优势

线程池做的工作主要是：控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

它的**主要特点**为：**线程复用，控制最大并发数，管理线程**

* 1：降低资源消耗，通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
* 2：提高响应速度。当任务到达时，任务可以不需要等待线程创建就能立即执行。
* 3：提高线程的可管理性，线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配，调优和监控



### 11.2.线程池的三大方法

Java中的线程池是通过 Executor 框架实现的，该框架中用到了 Executor ，Executors，ExecutorService，ThreadPoolExecutor 这几个类

![image-20200630202540635](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630202540635.png)



#### 测试代码

```java
package com.guo.pool;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

// Executors 工具类  3大方法
// 使用线程池之后  应该使用线程池来创建线程
public class ExecutorsMethod {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newSingleThreadExecutor(); // 单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5); // 创建一个固定的线程池大小
        ExecutorService threadPool = Executors.newCachedThreadPool(); // cached 缓存 可伸缩的 并发线程数多少，线程池的线程数量就多少


        try {
            for (int i = 0; i < 30 ; i++) {
    //            new Thread(()->{},"oldWay").start();
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完  程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```



#### 控制台输出

* Executors.newSingleThreadExecutor()

![image-20200630221553993](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630221553993.png)

* Executors.newFixedThreadPool(5)

![image-20200630221630656](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630221630656.png)



* Executors.newCachedThreadPool()

![image-20200630221441501](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630221441501.png)



### 11.3.七大参数

![image-20200630222947149](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630222947149.png)

```java
// 	Executors 三个创建线程池的方法
//	本质：创建 ThreadPoolExecutor
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}

public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}

public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,		// 21亿
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}

// ThreadPoolExecutor
public ThreadPoolExecutor(int corePoolSize,		// 核心线程池大小
                          int maximumPoolSize,	// 最大线程池大小
                          long keepAliveTime,	// 超时了没被调用就会释放
                          TimeUnit unit,		// 超时的单位
                          BlockingQueue<Runnable> workQueue,  	// 阻塞队列
                          ThreadFactory threadFactory,		  	// 线程工厂
                          RejectedExecutionHandler handler) {	// 拒绝策略
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```



### 11.4.四种拒绝策略

![image-20200630224112604](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630224112604.png)



**循环30次测试**

**代码来自： 11.5**

 *   new ThreadPoolExecutor.AbortPolicy()           最大承载满了，还有线程进来，不处理这个线程直接抛出异常

![image-20200630231252255](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630231252255.png)



 *   new ThreadPoolExecutor.CallerRunsPolicy()      队列满了，哪来的去哪里   这里是线程进不来 当前线程就是 main 线程

![image-20200630230325060](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630230325060.png)



 *   new ThreadPoolExecutor.DiscardPolicy()         队列满了,丢掉任务，不会抛出异常

![image-20200630230804240](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630230804240.png)



 *   new ThreadPoolExecutor.DiscardOldestPolicy()   队列满了,尝试和最早的线程竞争，不会抛出异常

![image-20200630231104343](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200630231104343.png)



### 11.5.手动创建一个线程池



```java
package com.guo.pool;

import java.util.concurrent.*;

// Executors 工具类  3大方法
// 使用线程池之后  应该使用线程池来创建线程


/**
 *   new ThreadPoolExecutor.AbortPolicy()           最大承载满了，还有线程进来，不处理这个线程直接抛出异常
 *   new ThreadPoolExecutor.CallerRunsPolicy()      队列满了，哪来的去哪里   这里是线程进不来 当前线程就是imain线程
 *   new ThreadPoolExecutor.DiscardPolicy()         队列满了,丢掉任务，不会抛出异常
 *   new ThreadPoolExecutor.DiscardOldestPolicy()   队列满了,尝试和最早的线程竞争，不会抛出异常
 */
public class ExecutorsMethod {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newSingleThreadExecutor(); // 单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5); // 创建一个固定的线程池大小
//        ExecutorService threadPool = Executors.newCachedThreadPool(); // cached 缓存 可伸缩的 并发线程数多少，线程池的线程数量就多少

        // 自定义线程池  工作中使用 ThreadPoolExecutor
        // 最大承载  maxPoolSize + Deque    5 + 3
        // * 使用 AbortPolicy() 拒绝策略   当超出最大承载时
        //      * 抛出 java.util.concurrent.RejectedExecutionException
        //      * 最大承载满了，还有线程进来，不处理这个线程直接抛出异常
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
                2,
                5,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy());

        try {
            for (int i = 0; i < 30 ; i++) {
    //            new Thread(()->{},"oldWay").start();
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完  程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```



### 11.6.cpu密集型 和 io密集型 （调优）

```java
package com.guo.pool;

import java.util.concurrent.*;

// Executors 工具类  3大方法
// 使用线程池之后  应该使用线程池来创建线程


public class ExecutorsMethod {
    public static void main(String[] args) {
        // 自定义线程池  工作中使用 ThreadPoolExecutor

        // 如何定义最大线程数
        // 1. cpu密集型  cpu多少核，线程池最大值就开启多少条  这样可以保持cpu的效率最高  //		
        //			Runtime.getRuntime().availableProcessors()
        // 2. io密集型  判断程序中十分耗io的线程有多少个  线程池最大值设置要大于这个数，一般设置为这个数的两倍
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
                2,
                Runtime.getRuntime().availableProcessors(),
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());

        try {
            for (int i = 0; i < 30 ; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完  程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```



## 12.四大函数式接口（必会）

普通码农必须掌握：lambda表达式、链式编程、函数式接口、Stream流式计算

### 12.1.函数式接口

@FunctionalInterface  在java中 经常被使用

比如 Runnable 接口就是

它能简洁编程，在新版本的框架底层被 大量运用 

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

![image-20200701003800805](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701003800805.png)



### 12.2.Function 函数式接口

![image-20200701005114909](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701005114909.png)

```java
package com.guo.functional_interface;

import java.util.function.Function;

/**
 *  Function  函数式接口   有一个输入参数，有一个输出
 *        只要是函数式接口  就可以使用 lambda表达式 简化
 */
public class FunctionTest {
    public static void main(String[] args) {
        // 输入什么值，返回什么值
//        Function<String,String> function = new Function<String,String>() {
//            @Override
//            public String apply(String str) {
//                return str;
//            }
//        };

//        Function function = (str) -> {
//            return str;
//        };

        Function<String,String> function = str -> { return str; };

        Function function = str -> { return str; };
        
        System.out.println(((Function<String, String>) function).apply("abcd"));
    }
}
```





### 12.3.Predicate  断定式接口

![image-20200701010631789](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701010631789.png)

```java
package com.guo.functional_interface;

import java.util.function.Predicate;

/**
 *  Predicate  断定式接口    有一个输入参数，返回值只能是 boolean
 */
public class PredicateTest {
    public static void main(String[] args) {
//        Predicate<String> predicate = new Predicate<String>(){
//            // 判断字符串是否为空
//            @Override
//            public boolean test(String str) {
//                return str.isEmpty();
//            }
//        };

        Predicate<String> predicate = (str) -> { return str.isEmpty(); };

        System.out.println(predicate.test(""));
    }
}
```



### 12.4.Consumer 消费式接口

![image-20200701021132686](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701021132686.png)

```java
package com.guo.functional_interface;

import java.util.function.Consumer;

/**
 *  Consumer 消费式接口  只有输入 没有返回值
 */
public class ConsumerTest {
    public static void main(String[] args) {
//        Consumer<String> consumer = new Consumer<String>() {
//            // 打印字符串
//            @Override
//            public void accept(String str) {
//                System.out.println(str);
//            }
//        };

        Consumer<String> consumer = (str) -> {
            System.out.println(str);
        };

        consumer.accept("abc");
    }
}
```



### 12.5.Supplier 供给式接口

![image-20200701022111710](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701022111710.png)

```java
package com.guo.functional_interface;

import java.util.function.Supplier;

/**
 *  Supplier 供给式接口     没有参数，只有返回值
 */
public class SupplierTest {
    public static void main(String[] args) {
//        Supplier supplier = new Supplier<String>() {
//            @Override
//            public String get() {
//                return "abcde";
//            }
//        };

        Supplier supplier = () -> { return "abcde"; };
        Supplier supplierInteger = () -> { return 1024; };

        System.out.println(supplier.get());
        System.out.println(supplierInteger.get());
    }
}
```



## 13.Stream流式计算

**流（Stream）到底是什么呢？**

是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列



大数据 = 存储 + 计算

存储 ： 集合，MySQL 本质都是存储东西

计算 ： 应该交给流来做



**“集合讲的是数据，流讲的是计算！”**

* 特点
  * Stream 自己不会存储元素。
  * Stream 不会改变源对象，相反，他们会返回一个持有结果的新Stream。
  * Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行



![image-20200701022606753](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701022606753.png)

![image-20200701040327013](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701040327013.png)

​							![image-20200701035951728](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701035951728.png)

![image-20200701040412210](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701040412210.png)

![image-20200701041927273](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701041927273.png)



#### 代码测试

##### User

```java
package com.guo.stream;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;
}
```



##### StreamTest

```java
package com.guo.stream;

import java.util.Arrays;
import java.util.List;

/**
 *  要求  只能用一行代码实现
 *
 *  现在有5个用户！ 筛选：
 *  1. id为偶数
 *  2. 年龄大于25
 *  3. 用户名转为大写字母
 *  4. 用户名字母倒序排列
 *  5. 只输出一个用户
 */
public class StreamTest {
    public static void main(String[] args) {
        User userA = new User(1, "a", 21);
        User userB = new User(2, "b", 22);
        User userC = new User(3, "c", 27);
        User userD = new User(4, "d", 26);
        User userE = new User(6, "e", 30);

        // 集合就是存储
        List<User> userList = Arrays.asList(userA, userB, userC, userD, userE);

        //计算交个Stream流
        userList.stream()
                .filter(user -> {return user.getId()%2==0;})
                .filter(user -> {return user.getAge()>25;})
                .map(user -> {return user.getName().toUpperCase();})
                .sorted((user1,user2)->{return user2.compareTo(user1);})
                .limit(1)
                .forEach(System.out::println);
    }
}
```



## 14.ForkJoin 分支合并

### 14.1.什么是ForkJoin

从JDK1.7开始，Java提供Fork/Join框架用于并行执行任务，它的思想就是讲一个大任务分割成若干小任务，最终汇总每个小任务的结果得到这个大任务的结果

这种思想和MapReduce很像（input --> split --> map --> reduce --> output）

主要有两步：

* 任务切分
* 结果合并

![image-20200701043412138](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701043412138.png)



### 14.2工作窃取

另外，forkjoin有一个**工作窃取**的概念。简单理解，就是一个工作线程下会维护一个包含多个子任务的**双端队列**。而对于每个工作线程来说，会从头部到尾部依次执行任务。这时，总会有一些线程执行的速度较快，很快就把所有任务消耗完了。那这个时候怎么办呢，总不能空等着吧，多浪费资源啊。

工作窃取（work-stealing）算法是指**某个线程从其他队列里窃取任务来执行**(能者多劳)。工作窃取的运行流程图如下：

![image-20200701043755809](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701043755809.png)





### 14.3.核心类

#### ForkJoinPool

WorkQueue是一个ForkJoinPool中的内部类，它是线程池中线程的工作队列的一个封装，支持任务窃取

什么叫线程的任务窃取呢？就是说你和你的一个伙伴一起吃水果，你的那份吃完了，他那份没吃完，那你就偷偷的拿了他的一些水果吃了。存在执行2个任务的子线程，这里要讲成存在A,B两个个WorkQueue在执行任务，A的任务执行完了，B的任务没执行完，那么A的WorkQueue就从B的WorkQueue的ForkJoinTask数组中拿走了一部分尾部的任务来执行，可以合理的提高运行和计算效率。每个线程都有一个WorkQueue，而WorkQueue中有执行任务的线程（ForkJoinWorkerThreadowner），还有这个线程需要处理的任务（ForkJoinTask<?>[] array）。那么这个新提交的任务就是加到array中



#### ForkJoinTask

**ForkJoinTask**代表运行**在ForkJoinPool**中的任务

* **主要方法**：
  * **fork()**  在当前线程运行的线程池中安排一个异步执行。简单的理解就是再创建一个子任务
  * **join()**   当任务完成的时候返回计算结果
  * **invoke()**  开始执行任务，如果必要，等待计算完成

* 子类： **Recursive** ：递归
  * **RecursiveAction**   （递归事件） 一个递归无结果的ForkJoinTask（没有返回值）
  * **RecursiveTask**       （递归任务） 一个递归有结果的ForkJoinTask（有返回值）



### 14.4.测试代码

#### ForkJoinDemo

```java
package com.guo.forkjoin;

import java.util.concurrent.RecursiveTask;

/**
 *  求和计算
 *
 *  使用 ForkJoin
 *  1. ForkJoinPool   通过它来执行
 *  2. 计算任务     forkJoinPool.execute(ForkJoinTask<?> task)
 */
public class ForkJoinDemo<L extends Number> extends RecursiveTask<Long> {

    private Long start;
    private Long end;

    // 临界值
    private Long temp = 10000L;

    public ForkJoinDemo(Long start, Long end) {
        this.start = start;
        this.end = end;
    }

    // 计算方法
    @Override
    protected Long compute() {
        if ((end - start) <= temp ) {
            Long sum = 0L;
            for (Long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        }else {
            // forkJoin   递归
            Long middle = (start + end) / 2;    // 中间值
            ForkJoinDemo<Number> task1 = new ForkJoinDemo<Number>(start, middle);
            ForkJoinDemo<Number> task2 = new ForkJoinDemo<Number>(middle+1, end);

            // 拆分任务 把任务压人线程队列
            task1.fork();
            task2.fork();

            return task1.join() + task2.join();
        }
    }
}
```



#### Test

```java
package com.guo.forkjoin;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.stream.LongStream;

// 计算类型  Long
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        test01();   // sum：500000000500000000===》执行时间：12032
//        test02();   // sum：500000000500000000===》执行时间：6078
        test03();   // sum：500000000500000000===》执行时间：906
    }

    // 普通计算方法
    public static void test01 () {
        Long sum = 0L;
        long startTime = System.currentTimeMillis();
        for (Long i = 0L; i <= 10_0000_0000 ; i++) {
            sum += i;
        }
        long endTime = System.currentTimeMillis();
        System.out.println("sum："+sum+"===》执行时间：" +(endTime - startTime));
    }

    // 会使用 ForkJoin
    public static void test02 () throws ExecutionException, InterruptedException {
        long startTime = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinDemo<Long> task = new ForkJoinDemo<>(0L, 10_0000_0000L);
        //  forkJoinPool.execute(task);    // 没有返回结果 public void execute(ForkJoinTask<?> task)
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);  // 提交任务  public <T> ForkJoinTask<T> submit(ForkJoinTask<T> task)
        Long sum = submit.get();

        long endTime = System.currentTimeMillis();
        System.out.println("sum："+sum+"===》执行时间：" +(endTime - startTime));
    }

    // 会使用  Stream并行流
    public static void test03 () {
        long startTime = System.currentTimeMillis();

        // Stream 并行流    (]
        Long sum = LongStream.rangeClosed(0, 10_0000_0000L).parallel().reduce(0, Long::sum);

        long endTime = System.currentTimeMillis();
        System.out.println("sum："+sum+"===》执行时间：" +(endTime - startTime));
    }
}
```



## 15.异步回调

### 15.1.概述

==**Future**设计的初衷==：对将来某个时刻会发生的结果进行建模。

当我们需要调用一个函数方法时。如果这个函数执行很慢,那么我们就要进行等待。但有时候,我们可能并不急着要结果。

因此,我们可以让被调用者立即返回,让他在后台慢慢处理这个请求。对于调用者来说,则可以先处理一些其他任务,在真正需要数据的场合再去尝试获取需要的数据

为了让程序更加高效，让CPU最大效率的工作，我们会采用异步编程。首先想到的是开启一个新的线程去做某项工作。再进一步，为了让新线程可以返回一个值，告诉主线程事情做完了，于是乎Future粉墨登场。然而Future提供的方式是主线程主动问询新线程，要是有个回调函数功能就更强大了。所以，为了满足Future的某些遗憾，强大的CompletableFuture随着Java8一起来了



![image-20200701141801226](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701141801226.png)

![image-20200701141806987](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701141806987.png)





![image-20200701153507750](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701153507750.png)



### 测试代码

#### 没有返回值的 runAsync 异步回调

```java
package com.guo.future;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

/**
 *  异步调用 ： CompletableFuture
 *      异步执行
 *      成功回调
 *      失败回调
 */
public class FutureTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 没有返回值的 runAsync 异步回调
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+" runAsync return void and sleep 2 seconds");
        });

        // 由于  CompletableFuture.runAsync（）是异步调用   延迟了2s的同时，其他的任务能执行
        System.out.println("12345");

        completableFuture.get();    // 获取阻塞执行的结果

    }
}
```

![image-20200701154324268](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701154324268.png)



#### 有返回值的 runAsync 异步回调

![image-20200701155130247](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701155130247.png)

##### 成功回调

```java
package com.guo.future;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

/**
 *  异步调用 ： CompletableFuture
 *      异步执行
 *      成功回调
 *      失败回调
 */
public class FutureTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 有返回值的 runAsync 异步回调
        // 在 ajax 中，有成功和失败的回调
        //     成功 ： 返回值
        //     失败 ； 返回错误信息
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName()+" supplyAsync return Integer");
            return 1024;
        });

        Integer returnMessage = completableFuture.whenComplete((t, u) -> {
            System.out.println("t==>" + t); // 正常的返回结果
            System.out.println("u==>" + u); // 错误的信息
        }).exceptionally((e) -> {
            System.out.println(e.getMessage());
            return 233;  // 可以获取 错误 的返回结果
        }).get();

        System.out.println("return message : "+returnMessage);
    }
}
```

![image-20200701155917886](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701155917886.png)



##### 错误回调

![image-20200701160433180](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701160433180.png)

![image-20200701160612240](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701160612240.png)



## 16.JMM

### 16.1问题：对volatile的理解

* volitile 是 Java 虚拟机提供的轻量级的同步机制，三大特性：
  * 1.保证可见性		
  * 2.==不保证原子性==
  * 3.禁止指令重排



### 16.2.什么是JMM

**JMM（java 内存模型）**  本身是一种抽象的概念，并不真实存在，它描述的是一组规则或者规范



**JMM 关于同步的规定：**

1.线程解锁前，必须把==共享变量的值== ==立刻== 刷新回主内存

2.线程加锁前，必须==读取主内存的最新值==到自己的工作内存

3.加锁解锁是**同一把锁**



![image-20200701174357498](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701174357498.png)



当我们在线程B执行过程中修改了状态值status时

线程A感知不到线程B操作了值的变化！如何能够保证线程间可以同步感知这个问题呢？

只需要使用==Volatile==关键字即可！volatile **保证线程间变量的可见性**，简单地说就是当线程A对变量X进行了修改后，在线程A后面执行的其他线程能看到变量X的变动，更详细地说是要符合以下两个规则 ：

* 线程对变量进行修改之后，要立刻回写到主内存

* 线程对变量读取的时候，要从主内存中读，而不是缓存

各线程的工作内存间彼此独立，互不可见，在线程启动的时候，虚拟机为每个内存分配一块工作内存，不仅包含了线程内部定义的局部变量，也包含了线程所需要使用的共享变量（非线程内构造的对象）的副本，即，为了提高执行效率



### 16.3.内存交互操作

内存交互操作有8种，虚拟机实现必须保证每一个操作都是原子的，不可在分的（对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许例外）

* lock （锁定）：作用于主内存的变量，把一个变量标识为线程独占状态* 
* unlock （解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定

* read （读取）：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用

* load （载入）：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中

* use （使用）：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使到这个指令

* assign （赋值）：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中

* store （存储）：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用

* write （写入）：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中



**JMM对这八种指令的使用，制定了如下规则：**

* 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write

* 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存

* 不允许一个线程将没有assign的数据从工作内存同步回主内存

* 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作

* 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁

* 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值

* 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量

* 对一个变量进行unlock操作之前，必须把此变量同步回主内存



## 17.Volatile

* volitile 是 Java 虚拟机提供的轻量级的同步机制，三大特性：
  * 1.保证可见性		
  * 2.==不保证原子性==
  * 3.禁止指令重排 （由于有内存屏障）



### 17.1.保证可见性

```java
package com.guo.volatile_test;

import java.sql.Time;
import java.util.concurrent.TimeUnit;

public class JMMTest {

    // 加 volatile 保证可见性
    private static volatile int num = 0;

    public static void main(String[] args) throws InterruptedException {

        // 除了main线程 再开启一条线程
        new Thread(() -> {
            // 当 number == 0 时线程一直执行
            while (num == 0){

            }
        }).start();


        TimeUnit.SECONDS.sleep(3);
        num = 1;
        System.out.println(num);
    }
}
```



### 17.2.不保证原子性

**原子性：不可分割**

线程A在执行任务的时候，不可分割、完整的，也就是某个线程正在做某个具体的业务的时候，中间不可以被加塞或者被分割，需
要整体完整，要么同时成功，要么同时失败

#### 1.测试代码

```java
package com.guo.volatile_test;

/**
 *  Volatile 不保证原子性
 */
public class VolatileTest02 {

    // Volatile 不保证原子性
    private volatile static int num = 0;

    public static void add() {
        num++;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 20 ; i++) {
            new Thread(()->{
                for (int j = 0; j < 5000 ; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2) {
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName()+" "+num);
    }
}
```

![image-20200701215522293](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701215522293.png)



#### 2.使用原子类解决原子性问题

* **如果不加 lock 和 synchronize ，如何保证原子性**

我们需要知道这里的 **num++** 并不是一个原子性操作

![image-20200701220249216](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701220249216.png)



* **这时可以使用原子类解决原子性问题**
  * 这些类都是直接合操作系统挂钩！在内存中修改值

![image-20200701220815489](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701220815489.png)



### 17.3.使用原子类 AtomicInteger 保证原子性

* 测试代码

```java
package com.guo.volatile_test;

import java.util.concurrent.atomic.AtomicInteger;

/**
 *  Volatile 不保证原子性
 */
public class VolatileTest02 {

    // Volatile 不保证原子性
//    private volatile static int num = 0;
    private volatile static AtomicInteger num = new AtomicInteger();

    public static void add() {
//        num++;  // 不是一个原子性操作
        num.getAndIncrement();  // AtomicInteger+1  底层： CAS
    }

    public static void main(String[] args) {
        for (int i = 0; i < 20 ; i++) {
            new Thread(()->{
                for (int j = 0; j < 5000 ; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2) {  // main  gc
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName()+" "+num);
    }
}
```

![image-20200701221411350](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701221411350.png)



* getAndIncrement() 源码
  * unsafe是一个很特殊的类  （详情在后文）

![image-20200701221108615](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701221108615.png)

![image-20200701221222566](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701221222566.png)



### 17.4.指令重排

要知道，计算机并不是按照我们写的程序顺序执行的

计算机在执行程序时，为了提高性能，编译器和处理器的常常会对==**指令做重排**==，一般分以下3种：

![image-20200701222006913](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701222006913.png)

* 单线程环境里面确保程序最终执行结果和代码顺序执行的结果一致

* 处理器在进行重排序时必须要==**考虑指令之间的数据依赖性**==

* 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测

```java
int x = 1;	// 1 
int y = 2;	// 2
x = x + 10;	// 3
y = x + x;	// 4

// 执行顺序 3、4 不能早于 1、2  因为处理器在进行重排序时 必须要考虑指令之间的数据依赖性
```



* 当 a、b、x、y都为0 的情况下  

  * 正常的结果是： x =0  , y =0   (没有指令重排的情况下)

  * | 线程A | 线程B |
    | ----- | ----- |
    | x = a | y = b |
    | b = 1 | a = 2 |

  

* **指令重排**导致的不同结果： x =2  ,  y =1

    * | 线程A | 线程B |
      | ----- | ----- |
      | b = 1 | a = 2 |
      | x = a | y = b |



#### volatile 避免指令重排

**内存屏障**  （CPU指令）

* 作用

  * 保持特定的操作的执行顺序
  * 可以保证某些变量的内存可见性 （也就是利用这些 实现了Volatile的可见性）

  ![image-20200701225341844](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701225341844.png)



## 18.单例模式

### 18.1.饿汉

饿汉式一上来就把所有东西加载了，这样很浪费内存资源

在Hungry类中，我定义了四个byte数组，当代码一运行，这四个数组就被初始化，并且放入内存了，如果长时间没有用到getInstance方法，不需要Hungry类的对象，这不是一种浪费吗？我希望的是 只有用到了 getInstance方法，才会去初始化单例类，才会加载单例类中的数据。所以就有了 第二种单例模式：懒汉式

```java
public class Hungry {
    
        // 饿汉式一上来就把所有东西加载了，这样很浪费内存资源
        private byte[] data1 = new byte[1024*1024];
        private byte[] data2 = new byte[1024*1024];
        private byte[] data3 = new byte[1024*1024];
        private byte[] data4 = new byte[1024*1024];
    
        private Hungry(){

        }

        private final static Hungry HUNGRY = new Hungry();

        public static Hungry getInstance(){
            return HUNGRY;
        }
}
```



### 18.2.懒汉

懒汉式在多线程下出现问题（需要加锁）

```java
public class LazyMan {

    private LazyMan(){}

    private static LazyMan lazyMan;

    public static LazyMan getInstance(){
        if (lazyMan == null){
            lazyMan = new LazyMan();
        }
        return lazyMan;
    }
}
```



![image-20200617125039790](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617125039790.png)



### 18.3.双重检测加锁懒汉式

当然，这种情况可以被反射破解，我们需要在构造器加锁，但其实也没用，都可以被反射破解，这时候就要用到枚举了

> 原本的双重检测加锁

```java
public class DCLLazyMan {

    private DCLLazyMan(){
        System.out.println(Thread.currentThread().getName()+" is doing");
    }

    // 加 volatile 避免指令重排
    private volatile static DCLLazyMan lazyMan;

    // 双重检测加锁   懒汉式单例     DCL懒汉式
    public static DCLLazyMan getInstance(){
        if (lazyMan == null){  // 避免不必要的同步
            synchronized (DCLLazyMan.class){
                if(lazyMan == null){
                    lazyMan = new DCLLazyMan();  // 不是一个原子性操作  指令重排
                    /**
                     * 1. 分配内存空间
                     * 2. 执行构造方法，初始化对象
                     * 3. 把这个对象指向这个空间
                     *
                     * 本来是走 123
                     *
                     * 但是如果 线程A 走 132 （指令重排）  此时LazyMan还没有完成构造 lazyMan == null
                     * 这个时候 线程B进来了  这个时候就可能出问题了，因为线程A和B都进入这儿判断了
                     *          需要对lazyman静态成员变量加个 volatile
                     *          以保证不同线程的可见性
                     */
                }
            }

        }
        return lazyMan;
    }
}
```



> 使用反射破解（通过无参构造器获取）

![image-20200617131816754](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617131816754.png)

* 解决方案：在构造方法加锁

修改构造方法

```java
private DCLLazyMan(){
   synchronized (DCLLazyMan.class){
       if(lazyMan != null){
           throw new RuntimeException("不要试图使用反射破坏移除");
       }
   }
}
```

![image-20200617134246665](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617134246665.png)



虽然这个看似可以加锁，但如果两个实例都是通过反射获取的，又出问题了

> 通过反射获取两个实例破解     这时可以使用标志位（红绿灯）加锁

![image-20200617134614560](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617134614560.png)



* 解决方案：在构造方法加锁，然后使用标志位判断，只有第一次能进来

```java
// 红绿灯标志位
private static boolean flag;

private LazyMan(){
    synchronized (LazyMan.class) {
        if (!flag) {
            // 第一次进入，修改标志位flag为 true
            flag = true;
        }else {
            throw new RuntimeException("不要试图使用反射破坏异常");
        }
    }
}
```

![image-20200617135544343](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617135544343.png)



但是其实这样也不安全，可以通过反射找到这个标志位字段，然后修改标志位以通过标志位判断（如：在这里设置标志位为false）



> 通过反射修改标志位破解    这时通过分析源码，应该使用枚举来阻止反射

![image-20200617135446179](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617135446179.png)



* 解决方案：通过源码分析，如果这个类是枚举，就不能通过反射破解



### 18.4.静态内部类

```java
// 静态内部类
public class Holder {

    private Holder(){

    }

    public static class InnerClass {
        private static final Holder HOLDER = new Holder();
    }

    public static Holder getInstance() {
        return InnerClass.HOLDER;
    }
}
```





### 18.5枚举单例

源码：如果这个类是枚举，就不能通过反射破解

![image-20200617141551851](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617141551851.png)

* 测试枚举单例

```java
package com.guo.single;

import java.lang.reflect.Constructor;

// enum 本身也是一个Class类
public enum  EnumSingle {

    INSTANCE;

    public EnumSingle getInstance() {
        return INSTANCE;
    }
}

class Test {
    public static void main(String[] args) throws Exception{
        EnumSingle instance = EnumSingle.INSTANCE;
        // 反射不能破坏枚举单例
        // * 通过源码能看到构造方法没有参数  但是反射获取构造方法不填参数的情况下，并非如我们想象的
        //   * Exception in thread "main" java.lang.NoSuchMethodException  它是没有这个构造方法的
        // * 通过 jad 工具反编译，得出构造方法有参数的  EnumSingle.class.getDeclaredConstructor(String.class,int.class)
        //   * Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects
        //   * 这个输出才是我们想要的  Cannot reflectively create enum objects
//        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(null);
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        declaredConstructor.setAccessible(true);
        EnumSingle instance2 = declaredConstructor.newInstance();
        System.out.println(instance);
        System.out.println(instance2);
    }
}
```



没有空参构造方法

![image-20200617141028322](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617141028322.png)



* 通过 javap -p 反编译
  * 并没有进展 （使用更强大的jad）

![image-20200701234647879](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200701234647879.png)



* 通过jad工具反编译

发现构造方法中是有参的

![image-20200617141214065](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617141214065.png)



* 修改代码

  * 反射构造方法时参数为  String.class  int.class

  抛出正确异常

![image-20200617141310118](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200617141310118.png)





## 19.深入理解CAS

### 19.1.什么是CAS

前言：互联网缩招之下，初级程序员大量过剩，高级程序员重金难求，除非你不吃这碗饭，否则就要逼自己提升

**修内功： 操作系统，计算机网络，数据结构与算法**



* CAS：比较当前 **工作内存中的值** 和 **主内存中的值**！如果这个值是期望的，就执行操作，如果不是就一直循环
  *  缺点
    * 循环会耗时
    * 一次性只能保证一个共享变量的原子性
    * 存在ABA问题



#### 测试代码

```java
package com.guo.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 *  CAS   compareAndSet(int expect, int update) ：比较并交换
 */
public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(200);

        // public final boolean compareAndSet(int expect, int update)
        // 参数   期望，更新
        // 如果和期望的值相同，就更新  否则，就不更新
        // CAS 是CPU的并发原语
        System.out.println(atomicInteger.compareAndSet(200, 201));
        System.out.println(atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(200, 201));
        System.out.println(atomicInteger.get());
    }
}
```

![image-20200702035401114](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702035401114.png)



### 19.2.UnSafe

![image-20200702022306560](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702022306560.png)

![image-20200702034509684](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702034509684.png)

![image-20200702034614516](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702034614516.png)



### 19.3.CAS中的ABA问题

左边线程在执行的过程中，期望值已经被右边线程改动了两次（改回原来的值），左边线程还以为是原来的样子

![image-20200702040123824](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702040123824.png)

#### 测试代码

```java
package com.guo.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 *  CAS   compareAndSet(int expect, int update) ：比较并交换
 */
public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(200);

        // public final boolean compareAndSet(int expect, int update)
        // 参数   期望，更新
        // 如果和期望的值相同，就更新  否则，就不更新
        // CAS 是CPU的并发原语
        // ============================== 捣乱的线程 ==============================
        System.out.println(atomicInteger.compareAndSet(200, 201));
        System.out.println(atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(201, 200));
        System.out.println(atomicInteger.get());

        // ============================== 期望的线程 ==============================
        System.out.println(atomicInteger.compareAndSet(200, 1024));
        System.out.println(atomicInteger.get());
    }
}
```

![image-20200702040733781](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702040733781.png)

可以加版本号来解决这个问题



## 20.原子引用

解决ABA问题，引入原子引用 ：对应的思想（乐观锁）

**带版本号的原子操作**

 **注意：**==Integer 使用了对象缓存机制，默认范围是 -127 ~ 128，推荐使用静态过程方法 valueOf获取对象实例，而不是new，因为 valueOf 使用缓存 ，而 new 的方式会创建新的对象而分配新的内存空间==



![image-20200702040953050](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702040953050.png)

```java
package com.guo.cas;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicStampedReference;

/**
 *  CAS   compareAndSet(int expect, int update) ：比较并交换
 */
public class CASDemo {
    public static void main(String[] args) {
//        AtomicInteger atomicInteger = new AtomicInteger(200);
        // 原子引用
        // AtomicStampedReference 如果泛型是包装类，注意对象的引用问题
        // 实际业务中  这里比较多是一个个对象
        AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(20,1);

        new Thread(()->{
            // 获取当前版本号
            int stamp = atomicStampedReference.getStamp();
            System.out.println("a1==>" + stamp);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("a2 executeing "+atomicStampedReference.compareAndSet(20, 25,
                    atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("a2==>" + atomicStampedReference.getStamp());

            System.out.println("a3 executeing "+atomicStampedReference.compareAndSet(25, 20,
                    atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("a3==>" + atomicStampedReference.getStamp());

        },"a").start();


        // 和乐观锁的原理相似
        new Thread(()->{
            int stamp = atomicStampedReference.getStamp();
            System.out.println("b1==>" + stamp);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("b2 executeing "+atomicStampedReference.compareAndSet(20, 6,
                    atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("b2==>" + atomicStampedReference.getStamp());

        },"b").start();


        // public final boolean compareAndSet(int expect, int update)
        // 参数   期望，更新
        // 如果和期望的值相同，就更新  否则，就不更新
        // CAS 是CPU的并发原语
        // ============================== 捣乱的线程 ==============================
//        System.out.println(atomicInteger.compareAndSet(200, 201));
//        System.out.println(atomicInteger.get());
//
//        System.out.println(atomicInteger.compareAndSet(201, 200));
//        System.out.println(atomicInteger.get());
//
//        // ============================== 期望的线程 ==============================
//        System.out.println(atomicInteger.compareAndSet(200, 1024));
//        System.out.println(atomicInteger.get());
    }
}
```



## 21.锁的理解

### 21.1.公平锁、非公平锁

公平锁：很公平，不能插队，必须先来后到

非公平锁： 不公平，可以插队 （默认都是非公平锁）

![image-20200702165614206](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702165614206.png)

![image-20200702165748197](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702165748197.png)

```java
/**
 * Creates an instance of {@code ReentrantLock}.
 * This is equivalent to using {@code ReentrantLock(false)}.
 */
public ReentrantLock() {
    sync = new NonfairSync();
}

/**
 * Creates an instance of {@code ReentrantLock} with the
 * given fairness policy.
 *
 * @param fair {@code true} if this lock should use a fair ordering policy
 */
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```



### 21.2.可重入锁

**可重入锁（递归锁）**

![image-20200702170355638](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702170355638.png)



#### 1.synchronized版本

```java
package com.guo.lock;

// synchronized
public class LockSynchronized {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B").start();
    }
}

class Phone {
    public synchronized void sms() {
        System.out.println(Thread.currentThread().getName() + " sms");
        call(); // 这里也有锁
    }

    public synchronized void call() {
        System.out.println(Thread.currentThread().getName() + " call");
    }
}
```

![image-20200702171844310](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702171844310.png)



#### 2.Lock版本

**注意：lock.lock();   lock.unlock();   锁必须配对，否则造成死锁**

```java
package com.guo.lock;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockReentrantLock {
    public static void main(String[] args) {
        Phone2 phone = new Phone2();
        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B" +
                "").start();
    }
}

class Phone2 {

    // lock.lock(); lock.unlock();   锁必须配对，否则造成死锁
    Lock lock = new ReentrantLock();

    public void sms() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " sms");
            call();  // 这里也有锁
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }

    }

    public void call() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " call");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

![image-20200702171826260](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702171826260.png)



### 21.3.自旋锁

**SpinLock**

![image-20200702034614516](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702034614516.png)

#### 1.自定义锁 SpinLockDemo

```java
package com.guo.lock;

import java.util.concurrent.atomic.AtomicReference;

/**
 *  自旋锁
 */
public class SpinLockDemo {

    // int      默认：0
    // Thread   默认：null
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    // 加锁
    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"==>myLock()");
        // 自旋锁
        while(! atomicReference.compareAndSet(null, thread)) {

        }
    }

    // 解锁
    public void unMyLock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"==>unMyLock()");
        atomicReference.compareAndSet(thread, null);
    }
}
```



#### 2.测试自定义锁

```java
package com.guo.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class TestSpinLock {
    public static void main(String[] args) {
//        Lock lock = new ReentrantLock();
//        lock.lock();
//        lock.unlock();

        // 底层使用自旋锁 CAS
        SpinLockDemo lock = new SpinLockDemo();

        // SpinLockDemo 中的 AtomicReference 保证了 T1拿到锁之后，T2必须等T1解锁完，才能拿到锁
        new Thread(()->{
            lock.myLock();

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unMyLock();
            }

        },"T1").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // T2线程 需要等待 T1 释放锁 ，这个期间 T2 一直在自旋
        //  当 T1 释放锁之后 atomicReference的期望值至null [expect ==》 null]  --》 T2才能拿到锁
        new Thread(()->{
            lock.myLock();

            try {
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.unMyLock();
            }

        },"T2").start();

    }
}
```

![image-20200702194916990](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702194916990.png)



### 21.4.死锁

![image-20200702195557473](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702195557473.png)

#### 死锁的四个必要条件

* 互斥条件：一个资源每次只能被一个进程使用，即在一段时间内某 资源仅为一个进程所占有。此时若有其他进程请求该资源，则请求进程只能等待。

* 请求与保持条件：进程已经保持了至少一个资源，但又提出了新的资源请求，而该资源 已被其他进程占有，此时请求进程被阻塞，但对自己已获得的资源保持不放。

* 不可剥夺条件:进程所获得的资源在未使用完毕之前，不能被其他进程强行夺走，即只能 由获得该资源的进程自己来释放（只能是主动释放)。

* 循环等待条件: 若干进程间形成首尾相接循环等待资源的关系



***这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之一不满足，就不会发生死锁***



#### 测试代码

```java
package com.guo.lock;

import java.util.concurrent.TimeUnit;

public class DeadLock {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new MyThread(lockA,lockB),"Thread 1").start();
        new Thread(new MyThread(lockB,lockA),"Thread 2").start();
    }
}

class MyThread implements Runnable{

    private String lockA;
    private String lockB;

    public MyThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName()+" lock: "+lockA+" want to get "+lockB);
     
            // 停止一秒是为了保证两个线程先获取资源的锁，防止被抢占
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName()+" lock: "+lockB+" want to get "+lockA);
            }
        }
    }
}
```

![image-20200702202830258](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702202830258.png)



#### 解决问题

* 使用 ==**jps**== 定位**进程号**

```shell
jps -l
```

![image-20200702203006471](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702203006471.png)



* 使用  **==jstack==**  通过**进程号**查看**堆栈信息** ，以找到死锁问题

```shell
jstack [进程号]
```



![image-20200702203336386](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702203336386.png)

![image-20200702203558267](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20200702203558267.png)