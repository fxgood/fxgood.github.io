---
date: 2020-07-30
title: Java高级
category: Java
tag: 编程语言
excerpt: 记录Java高级方面知识，包括多线程、IO、泛型、集合、网络编程、反射等。
top: 10
---

# 1.多线程

## 1.1 基本概念：程序、进程、线程
程序：一段静态的代码
进程：正在运行的程序，是**资源分配的基本单位**。
线程：**是CPU执行和调度的最小单位**，每个线程拥有**独立的运行栈和程序计数器pc**，线程切换的开销比较小。多个线程共享一个进程的资源（共享进程的方法区和堆，但存在安全隐患）。

 ![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/a7437d2699d34cc582cbbd041ec7fefa.png)
 在Java的内存区域中，**栈区（虚拟机栈）和程序计数器是每个线程一份**，**方法区和堆区，是每个进程一份，各个线程共享**。

**使用多线程的优点**
1.资源利用率提升，程序处理效率提高
2.改善程序结构，代码会相对简单
3.软件运行速度提升，提升应用程序的响应速度

**何时需要多线程**
1.程序需要同时执行两个或多个任务
2.程序需要实现一些需要等待的任务，如用户输入、文件读写操作、网络操作、搜索等
3.需要一些后台运行的程序时

**Java程序至少有3个线程**
1.主线程
2.垃圾回收线程
3.异常处理线程（会影响主线程）

## 1.2 线程的创建和使用
Java语言的JVM允许程序运行多个线程，它通过java.lang.Thread类来体现 

**Thread类的特性**
1.每个线程都是通过特定Thread对象的run()方法来完成操作的，经常把run()方法的主体称为**线程体**
2.通过该Thread对象的start()方法来启动这个线程，而非直接调用run()
3.**Thread类实现了Runnable接口**

**start方法作用**
1.启动当前线程
2.调用当前线程的run方法

**注意**
1.不能通过调用run方法来启动线程（仍然在主线程中执行）
2.额外再启动一个线程，需要重新创建一个线程的对象

**两种创建线程的方法**
1.继承Thread类，重写run方法
2.实现Runnable接口，实现run方法，在Thread构造器中传入一个Runnable接口实现对象

**两种创建线程方法的比较**
开发中，优先选择Runnable接口的方法
1.实现的方式没有类单继承性的限制
2.实现的方式更适合来处理多个线程共享数据的情况

**注意，这里ticket没有加static，就实现了三个线程共享ticket**

```java
class Sell implements Runnable {
    private int ticket = 100;

    @Override
    public void run() {
        while (ticket > 0) {
            System.out.println(Thread.currentThread().getName() + "卖" + ticket + "号票");
            ticket--;
        }
    }
}
/*
 * 死锁的四个必要条件
 * 1.互斥访问：共享资源互斥访问
 * 2.请求并保持：已经持有资源的情况下，申请别的资源未果，也不释放自己持有的资源（站在自己角度）
 * 3.不可剥夺：进程申请的资源除了完成任务释放，别人无法将其释放（站在别人角度）
 * 4.循环等待：系统中存在资源循环等待的链条*/

public class Demo {
    public static void main(String[] args) {
        Sell sell = new Sell();
        new Thread(sell).start();
        new Thread(sell).start();
        new Thread(sell).start();
    }

```

![image-20220424100238580](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220424100238580.png)

![image-20220424100254726](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220424100254726.png)

**Thread类常用方法**
1.`void start()`：启动当前线程；调用当前线程的run()
2.`run()`：通常需要重写Thread类中的此方法，将创建的线程需要执行的操作声明在此方法中
3.`String getName()`
4.`void setName()`
5.`static Thread currentThread()`:返回当前线程。在Thread子类中就是this，通常用于主线程和Runnable实现类。
6.`yield()`：释放cpu的执行权（有可能紧接着又获取了cpu的执行权）
7.`h.join()`：在线程a中执行`h.join()`，会阻塞当前线程a，直到h线程执行完，线程a才结束阻塞状态
8.`stop()`：强制线程生命周期结束，**deprecated 不推荐使用，因为不安全，过时的api**
9.`static void sleep(long millis)`：睡眠(阻塞)若干毫秒；静态方法，可以直接调用。
注意：如果是在run方法中使用sleep，则处理sleep的异常只能try-catch，无法throws；因为是重写Thread的run方法，其并无异常抛出，故子类重写也无法抛出异常。
10.`bool isAlive()`：判断当前线程是否存活（是否执行完了），阻塞状态下返回值也是true。

**获取线程名称**
`Thread.currentThread().getName();`

**更改线程名称**
1.通过`Thread.currentThread().setName();`
2.通过构造器

```java
class MyThread extends Thread{
    public MyThread(String name){
        super(name);
    }

    public static void main(String[] args) {
        Thread t1=new MyThread("一号线程");
    }
}
```



**使用匿名对象执行开启线程**
```java
new Thread(){
     private  int count=0;
     @Override
     public void run(){
         while(true)
             System.out.println(count++);
     }
 }.start();
```
## 1.3 线程的调度
- Java的调度方法
1.同优先级线程组成先进先出队列（先到先服务），使用时间片策略
2.对于高优先级，使用优先调度的抢占式策略

- 线程的优先级
1.线程的优先级等级：`MAX_PRIORITY:10`,`MIN_PRIORITY:1`,`NORM_PRIORITY:5`
2.获取和设置当前线程的优先级：`getPriority()`,`setPriority(int p)`

> 注意：并不是优先级高的执行完再执行优先级低的，而是优先级高的获取CPU的概率更高

## 1.4 线程的分类
在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) 

用个比较通俗的比如，任何一个守护线程都是整个JVM中所有非守护线程的保姆：

只要当前JVM实例中尚存在任何一个非守护线程没有结束，守护线程就全部工作；只有当最后一个非守护线程结束时，守护线程随着JVM一同结束工作。
Daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者。

User和Daemon两者几乎没有区别，唯一的不同之处就在于虚拟机的离开：如果 User Thread已经全部退出运行了，只剩下Daemon Thread存在了，虚拟机也就退出了。 因为没有了被守护者，Daemon也就没有工作可做了，也就没有继续运行程序的必要了。


值得一提的是，守护线程并非只有虚拟机内部提供，用户在编写程序时也可以自己设置守护线程。下面的方法就是用来设置守护线程的。

```java
Thread daemonTread = new Thread();
 
  // 设定 daemonThread 为 守护线程，default false(非守护线程)
 daemonThread.setDaemon(true);
 
 // 验证当前线程是否为守护线程，返回 true 则为守护线程
 daemonThread.isDaemon();
```
## 1.5 线程的生命周期
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/217a185420c343c588eace6cab65a7ee.png)

## 1.6 线程的同步
线程同步，主要用来解决线程的安全问题。

**例：创建三个线程卖票，总共100张**
1.问题：卖票过程中，出现了重票、错票（0，-1号票）-->出现了线程的安全问题
2.问题出现的原因：某线程操作车票时，别的线程也参与进来
3.如何解决：某线程操作车票时，即使出现阻塞（例如sleep(100);)，别人也不能参与进来。
4.在Java中，我们通过同步机制，来解决线程的安全问题

**解决方式**

1. 同步代码块
a.同步监视器（又叫锁）：任何一个对象都可以作为锁；**要求多个线程必须要共用同一把锁。**
b.同步原理：当一个线程执行代码碰到一个锁时，如果已经有别的线程使用了锁并且没有归还，那么本线程就阻塞在这里。


**继承Thread方式**
```java
public class p1 {
    public static void main(String[] args) {
        Sell s1 = new Sell();
        Sell s2 = new Sell();
        Sell s3 = new Sell();
        s1.start();
        s2.start();
        s3.start();
    }
}

class Sell extends Thread {
    public static int ticket = 10000;
    @Override
    public void run() {
        while (true) {
            synchronized(Sell.class) {
                if (ticket > 0) {
                    System.out.println(getName() + "出售第" + ticket + "张票");
                    ticket--;
                } else
                    break;
            }
        }
    }
}
```
这里的`Sell.class`充分说明了，在Java中**类也是对象**


**实现Runnable接口方式**
```java
public class p2 {
    public static void main(String[] args) {
        Sell2 sell=new Sell2();
        Thread t1=new Thread(sell);
        Thread t2=new Thread(sell);
        Thread t3=new Thread(sell);
        t1.start();
        t2.start();
        t3.start();
    }
}

class Sell2 implements Runnable {
    private int ticket=10000;
    @Override
    public void run() {
        while(true){
            synchronized(this){
                if(ticket>0)
                    System.out.println(Thread.currentThread().getName()+"出售"+ticket--+"号票");
                else
                    break;
            }
        }
    }
}
```


2. 同步方法
如果操作共享数据的代码完整地声明在一个方法中，我们不妨将此方法声明为同步的。

**实现Runnable接口方式**

```java
public class p3 {
    public static void main(String[] args) {
        Runnable r = new Window();
        Thread t1 = new Thread(r);
        Thread t2 = new Thread(r);
        Thread t3 = new Thread(r);
        t1.start();
        t2.start();
        t3.start();
    }
}

class Window implements Runnable {
    private int ticket = 10000;

    @Override
    public void run() {
        while (ticket > 0) {
            sell();
        }
    }

    private synchronized void sell() {
        if (ticket > 0) {
            System.out.println(Thread.currentThread().getName() + "出售" + ticket-- + "号票");
        }
    }
}

```

**继承Thread方式**，注意这里的sell方法必须要加static，否则会出现安全问题（不加static，同步方法所用的锁，不是同一个）

<font color='red'>同步方法不加static，使用的是`this`作为锁。加了static，使用的是`类名.class`作为锁</font>

```java
public class p4 {
    public static void main(String[] args) {
        Window4 w1=new Window4();
        Window4 w2=new Window4();
        Window4 w3=new Window4();
        w1.start();
        w2.start();
        w3.start();
    }
}


class Window4 extends Thread{
    private static int ticket=10000;
    @Override
    public void run(){
        while(ticket>0)	//这里可能会出现卖第0张票的情况
            sell();
    }

    private static synchronized void sell(){
        if(ticket>0)
            System.out.println(Thread.currentThread().getName()+"出售"+ticket--+"号票");
    }

}
```
3. 使用同步锁
```java
public class Main {
    public static void main(String[] args) {
        Runnable r = new Window();
        Thread t1 = new Thread(r);
        Thread t2 = new Thread(r);
        Thread t3 = new Thread(r);
        t1.setName("线程1");
        t2.setName("线程2");
        t3.setName("线程3");
        t1.start();
        t2.start();
        t3.start();
    }
}

class Window implements Runnable {
    private int ticket = 10000;
    ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            try {
                lock.lock();
                if (ticket > 0)
                    System.out.println(Thread.currentThread().getName() + "出售" + ticket-- + "号票");
                else
                    break;
            } finally {
                /*保证无论发生什么特殊情况，一定能够释放锁*/
                lock.unlock();
            }
        }
    }
}
```

**可重入锁 ReentrantLock**
```可重入锁指的是在一个线程中可以多次获取同一把锁，比如：
一个线程在执行一个带锁的方法，该方法中又调用了另一个需要相同锁的方法，则该线程可以直接执行调用的方法，而无需重新获得锁；如果有两个不同的方法用到同一个锁，其中方法A里面又调用了方法B，那就不会死锁了
```

面试题：synchronized和Lock的区别？
synchronized在执行完同步代码以后，自动释放同步监视器
Lock方式手动加锁和释放锁，更加灵活。


## 1.7 改造懒汉单例模式使之线程安全
**注意，写了两次判断`instance==null`**，这样效率最高。只在首次访问的时候进行同步，后续已经有instance的情况下无需同步，直接返回instance，最大化效率。

```java
//单例模式之懒汉式

class Tool {
    private static Tool instance = null;

    //私有化构造方法
    private Tool() {

    }

    public static Tool getInstance() {
        if (instance == null) {
            synchronized (Tool.class) {
                if (instance == null)
                    instance = new Tool();
            }
        }
        return instance;
    }
}
```

## 1.8 线程的死锁问题
**死锁**：
1.不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁
2.出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续
（操作系统中学到的定义：多个进程在执行过程中，由于竞争资源或者彼此通信而造成的一种阻塞现象，若无外力作用，它们都将无法推进下去，这些永远都在互相等待的进程，称为死锁进程）

**解决方法**
1.专门的算法、原则
2.尽量减少同步资源的定义
3.尽量避免嵌套同步

**死锁发生的四个必要条件**
1.互斥：共享资源互斥访问
2.请求并保持：进程请求资源未果，不会释放自身已经占有的资源
3.不可剥夺：只有进程自身使用完资源后才会释放，别人无法将其释放
4.循环等待：多个进程之间存在资源请求的环路

**预防死锁**
1.破坏请求并保持条件：一次性请求所有需要的资源，否则就不申请
2.破坏不可剥夺条件：请求资源未果，释放自身占有的资源
3.破坏循环等待条件：可用资源线性排序，只能从低到高申请资源

**避免死锁**
银行家算法：已分配资源表、可用资源表、所需资源表；每次分配给一个能够满足其需求的进程，该进程执行完后返还所有的资源，不断循环。

```
在避免死锁方法中允许进程动态地申请资源，但系统在进行资源分配之前，应先计算此次分配资源的安全性，若分配不会导致系统进入不安全状态，则分配，否则等待。
```
**预防死锁和避免死锁的区别**
预防死锁是通过破坏死锁发生的必要条件中的一个，限制条件比较严格，可能造成系统资源利用率和系统吞吐率的下降。
避免死锁施加的条件比较宽松，**在资源的动态分配中，避免系统出现不安全状态**。

## 1.9 线程的通信
例题：使用两个线程打印1~100。线程1，线程2交替打印
```java
public class Main {
    public static void main(String[] args) {
        Runnable r = new Count();
        Thread t1 = new Thread(r);
        Thread t2 = new Thread(r);
        t1.start();
        t2.start();
    }
}

class Count implements Runnable {
    private int num = 100;

    @Override
    public void run() {
        while (true) {
            synchronized (this) {
                notify();	//或者this.notify,  如果用的是某个obj，则obj.notify
                if (num > 0) {
                    System.out.println(Thread.currentThread().getName() + "数" + num--);
                } else
                    break;
                try {
                    wait(); /*自我阻塞，wait会自动释放锁（与sleep的区别）*/
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
**涉及到的三个方法**
1.`wait()`：一旦执行此方法，当前线程就进入**阻塞状态**， **并释放同步监视器**
2.`notify()`：一旦执行此方法。就会唤醒被wait的一个线程。如果有多个，则唤醒优先级最高的
3.`notifyAll()`：唤醒所有wait的线程

```
补充：
this.notify();//唤醒同步监视器等待池中的一个线程（放入锁池中，开始竞争锁）
this.wait(); //当前线程停止执行，并释放锁，进入同步监视器的等待池中（不竞争锁）
```





**说明**
1.wait(), notify(), notifyAll() 三个方法必须使用在同步代码块或者同步方法中
2.<font color='red'>三者的调用者**必须**是同步代码块或同步方法中的**同步监视器**，否则会报异常</font>，不加调用者默认是`this.`
3.三者是定义在java.lang.Object类中的

上述例题也可以使用公平锁来实现（存在一个等待队列，先进先出）,**注意要使用finally来保证unlock的执行**
```java
public class Main {
    public static void main(String[] args) {
        Runnable r=new Count();
        Thread t1=new Thread(r);
        Thread t2=new Thread(r);

        t1.start();
        t2.start();
    }
}

class Count implements Runnable {
    private int num = 100;
    ReentrantLock lock = new ReentrantLock(true);/*fair lock*/

    @Override
    public void run() {
        while(true){
            try{
                lock.lock();
                if(num>0)
                    System.out.println(Thread.currentThread().getName()+":"+num--);
                else
                    break;
            }
            finally {
                lock.unlock();
            }
        }
    }
}

```

**生产者消费者问题**
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/0ff201a797884d01ab10b9011b7dcada.png)

```java
public class Main {
    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        new Thread(new Consumer(clerk)).start();
        new Thread(new Consumer(clerk)).start();
        new Thread(new Producer(clerk)).start();
    }
}

class Clerk {
    private int products = 0;

    public synchronized void produce() {
        //notity(); 也可以写这里
        if (products < 20) {
            System.out.println("生产者生产第" + (++products) + "个商品");
            this.notify();
        } else {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public synchronized void consume() {
        if (products > 0) {
            System.out.println("消费者消费第" + products-- + "个商品");
            if (products < 20)
                this.notify();
        } else {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Producer implements Runnable {
    private Clerk clerk;

    public Producer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.produce();
        }
    }
}

class Consumer implements Runnable {
    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.consume();
        }
    }
}

```
> 这里有个疑问：如果把sleep写在两个同步方法中，while中只有调用，则会出现每次生产者生产满20个消费者才去消费，每次消费者消费完了生产者才开始生产。即每轮while循环结束同步方法都不释放锁。（在while中随意加入一个语句如print，则此现象消失，正常交替执行）
## 1.10 JDK5.0新增线程创建方式
**新增方式一：实现Callable接口**
与Runnable相比，Callable功能更强大
1.相比`run()`方法，可以有返回值
2.方法可以抛出异常（重写Runnable中的run不能抛出异常，因为Runnable接口中的run没有抛  ）
3.支持泛型的返回值
4.需要借助`FutureTask`类，比如获取返回值 

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Demo {
    public static void main(String[] args) {
        Count count = new Count();
        FutureTask futureTask = new FutureTask(count);
        new Thread(futureTask).start();
        Object ans = null;
        try {
            ans = futureTask.get();
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        System.out.println(ans);
    }
}

class Count implements Callable {
    @Override
    public Integer call() {
        int i = 100000000;
        int sum = 0;
        while (i-- > 0)
            sum += i;
        return sum;
    }
}

```
**使用泛型**
```java
public class Demo {
    public static void main(String[] args) {
        Count count = new Count();
        FutureTask<Integer> futureTask = new FutureTask<>(count);
        new Thread(futureTask).start();
        int ans = 0;
        try {
            ans = futureTask.get();
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        System.out.println(ans);
    }
}

class Count implements Callable<Integer> {
    @Override
    public Integer call() {
        int i = 100000000;
        int sum = 0;
        while (i-- > 0)
            sum += i;
        return sum;
    }
}
```

**新增方式二：使用线程池**
背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大
思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。
好处：
1.提高响应速度（减少了创建新线程的时间）
2.降低资源消耗（重复利用线程池中的线程，不需要每次都创建）
3.便于线程管理：
`corePoolSize`核心池的大小
`maximumPoolSize`最大线程数 
`keepAliveTime` 线程没有任务时最多保持多长时间后会终止

**线程池相关API**
1.JDK5.0起提供了线程池相关API：`ExecutorService`和`Executors`
2.`ExecutorService` 真正的线程池接口.常见子类`ThreadPoolExecutor`
- `void execute(Runnable command)`:执行任务/命令,没有返回值,一般用来执行Runnable
- `<T>Future<T>submit(Callable<T>task):`执行任务,有返回值,一般用来执行Callable
- `void shutdown()`:关闭连接池

3.Executors:工具类、线程池的工厂类,用于创建并返回不同类型的线程池

- `Executors.newCachedThreadPool()`:创建一个可根据需要创建新线程的线程池
- `Executors.newFixedThreadPool(n)`:创建一个可重用固定线程数的线程池
- `Executors.newSingleThreadExecutor()`:创建一个只有一个线程的线程池
- `Executors.newScheduledThreadPool(n)`:创建一个线程池,它可以安排在给定延迟后运行命令或者定期地执行

```java
public class Main {
    public static void main(String[] args) {
        /*Executors是线程池的工厂类*/
        ExecutorService server= Executors.newFixedThreadPool(10);
        server.execute(new Count());
        server.submit(new Calculate());
        /*或者使用FutureTask包装Calculate()以获取线程返回值*/
        FutureTask<Integer> task = new FutureTask<>(new Calculate());
        server.submit(task);
        try {
            System.out.println(task.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}

class Count implements Runnable{
    @Override
    public void run() {
        int i=0;
        while(i++<100)
            System.out.print(i+" ");
    }
}

class Calculate implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        int sum=0;
        for(int i=0;i<100;i++)
            sum+=i;
        return sum;
    }
}


```
## 练习题
1. 谈谈你对程序、进程、线程的理解
**程序**：一段静态代码
**进程**：关于进程我想谈两点，一个是为什么需要进程这个概念，另一个是进程的作用。首先，现代操作系统的一大特点就是多道程序设计，那么在此之前是单道程序设计，一个程序运行时占有所有的资源，那么就无需资源的分配和调度，也就无需进程这个概念。到了多道程序设计中，多个程序并发执行，这就涉及到了系统资源的分配和调度，诸如CPU资源、存储资源、IO资源、文件资源，那么进程这个概念就应运而生。因此进程的作用有两大点，第一，它是资源分配和调度的基本单位。第二，它是程序独立运行的载体，保障程序正常执行。
**线程**：线程是CPU调度的基本单位，多个线程之间共享进程的资源；线程包含在进程之中，是进程中实际运行工作的单位。

> 操作系统四大特性：虚拟、异步、共享、并发。
2. 对比两种线程的创建方式
3. sleep和wait方法的异同？
相同点：都能使当前线程阻塞
不同点：1.sleep声明在Thread类中，wait声明在Object类中 2.`sleep()`可以在任何地方使用，`wait()`需要在同步方法或者同步代码块中调用 3.如果都使用在同步代码块或者同步方法中，sleep方法不会释放锁，wait会释放锁

# 2.常用类
## 2.1 字符串相关的类
### String类
**String特性**
1.String类:代表字符串.Java程序中,所有字符串的字面值(如"abc")都是String类的实例
2.String是一个**final类**,不可以继承
3.字符串是**常量**,值在创建之后不可改变,字符内容是存储在一个字符数组value[]中的
4.通过字面量的方式(区别于new)给一个字符串赋值,此时的字符串值声明在字符串常量池中
5.字符串常量池(**位于方法区中,后来改叫meta space**)中是不会存储相同内容的字符串的
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/368db7198d97447795b9c31952568c7f.png)
**String对象的创建**
1.字面量的方式,`String str="abc";`
2.`String s=new String()`
3.`String s=new String(String s2)`
4.`String s=new String(char[] arr)`
5.`String s=new String(char[] arr,int startIndex, int count)`

> 字符串常量池存储在字符串常量池,目的是共享; 字符串非常量的对象存储在堆中

面试题1:下面的`p1.name==p2.name?`
```java
public class Main {
    public static void main(String[] args) {
        Person p1 = new Person("yfx");
        Person p2 = new Person("yfx");
        System.out.println(p1.name == p2.name); //true 地址相同
    }
}

class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
}
```

面试题2:`String s=new String("abc");` 在内存中创建了几个对象? 答:**如果常量池中没有abc,则创建了两个,有的话则创建一个**(现在常量池创建"abc",然后在堆中创建一个String对象,内容**fianl char[]value**指向常量池的"abc")

面试题3:下面几种字符串连接后,在内存中实际是怎么样的?
```java
public static void main(String[] args) {
    String s1 = "java", s2 = "docker";

    String s3="javadocker";
    String s4="java"+"docker";	//编译阶段会直接合成"javadocker"
    String s5=s1+"docker";
    String s6="java"+s2;

    System.out.println(s3==s4); //true
    System.out.println(s3==s5); //false
    System.out.println(s3==s6); //false
    System.out.println(s5==s6); //false

	String s7=s5.intern();	
//intern():如果常量池中没有s5的字符串值,那么就在常量池中创建该字符串常量,并返回其引用;如果常量池中已经有,则直接返回引用
    System.out.println(s3==s7); //true
    
    final String s8="java";	//final修饰的变成常量
    String s9=s8+"docker";
    System.out.println(s3==s9); //true
}
```
> 涉及到对象的连接,则都在堆中产生新的String对象; 

面试题4:java的值传递
```java
public class Main {
    String str = new String("good");

    char[] ch = {'t', 'e', 's', 't'};

    public void change(String str, char ch[]) {
        str = "test ok";	//将常量池中"test ok"的地址赋值给str
        ch[0] = 'b';
    }

    @Test
    public void test() {
        Main obj = new Main();
        obj.change(obj.str, obj.ch);
        System.out.println(obj.str);    //good
        System.out.println(obj.ch);     //best
    }
}
```

### String类常用方法
1.`int length()`
2.`char charAt(int index)`
3.`boolean isEmpty()`
4.`String toLowerCase()`
5.`String toUpperCase()`
6.`String trim()`:返回字符串的副本,忽略前导空白和尾部空白
7.`boolean equalsIgnoreCase(String s)`
8.`String concat(String str)`:将指定字符串连接到此字符串的结尾,等价于`+`
9.`int compareTo(String s)`:比较两个字符串字典序
10.`String substring(int beginIndex)`:
11.`String substring(int beginIndex, int endIndex)`:按照下标截取字符串,左闭右开
12.`boolean endsWith(String suffix)`:是否以指定的后缀结束
13.`boolean startsWith(String prefix)`:是否以指定的前缀开始
14.`boolean startsWith(String prefix,int offset)`:测试此字符串从指定索引开始的子串是否以指定前缀开始

15.`boolean contains(CharSequence s)`:当且仅当此字符串包含指定的char值序列时,返回true
16.`int indexOf(String str)`:返回子串第一次出现的索引,未找到返回-1
17.`int indexOf(String s,int fromIndex)`:返回子串从偏移下标开始首次出现的索引
18.`int lastIndexOf(String s)`:反向搜索
19.`int lastIndexOf(String s,int fromIndex)`,从指定偏移量开始反向搜索

20.`String replace(char oldChar,char newChar)`:替换所有指定字符
21.`String replace(CharSequence target, CharSequence replacement)`:替换所有指定子串
22.`String replaceAll(String regex, String replacement)`:使用给定的子串替换所有匹配正则表达式的子串
23.`String replaceFirst(String regex, String replacement):` 

24.`boolean matches(String regex)`:告知此字符串是否匹配给定的正则表达式

25.`String[] split(String regex)`:根据给定正则表达式的匹配拆分此字符串
26.`String[] split(String regex, int limit)`:根据给定正则表达式来拆分此字符串,最多不超过limit个,如果超过了,剩下的全部放到最后一个元素中
### String与基本数据类型转换
1.String转基本数据类型、包装类: 调用包装类的静态方法:`parseXXX(str)`
2.基本数据类型、包装类转Stirng:`String.valueOf(xxx)`
3.String转char[]:`str.toCharArray()`
4.char[]转String:`new String(char[] arr)`
5.String转byte[]:`str.getBytes()`
6.byte[]转String:`new String(bytes[] arr)` 重载函数`new String(bytes[]arr,字符集)`

```java
public class Main {
    public static void main(String[] args) throws UnsupportedEncodingException {
        String s="abc123中国";
        byte[] bytes = s.getBytes();//使用默认的字符集
        System.out.println(Arrays.toString(bytes));
        //[97, 98, 99, 49, 50, 51, -28, -72, -83, -27, -101, -67]  utf-8编码一个汉字3个字节
        byte[] gbks = s.getBytes("gbk");
        System.out.println(Arrays.toString(gbks));
        //[97, 98, 99, 49, 50, 51, -42, -48, -71, -6]       gbk编码一个汉字2个字节

        System.out.println(new String(bytes));
        //abc123中国
        System.out.println(new String(gbks));
        //abc123�й�  字符集指定错误,出现乱码
        System.out.println(new String(gbks,"gbk"));
        //abc123中国
    }
}
```
### StringBuffer,StringBuilder
**String**:
1.不可变字符序列

**StringBuffer**:
1.可变字符序列
2.线程安全,效率低 (除了构造方法,所有方法都加了**synchronized**)

**StringBuilder**:
1.可变字符序列
2.线程不安全,效率高

> 三者底层都是用char[]存储

**StringBuffer和StringBuilder的扩容**
默认情况下,扩容为原来的2倍+2,同时将原有数组中的元素复制到新的数组中
**指导意义**: 如果需要对字符串进行频繁的添加, 建议使用此构造器`StringBuffer(int capacity)` 或 `StringBuilder(int capacity)`,提前指定容量,以免频繁扩容,降低效率

**三者效率对比**

```java
    @Test
    public void testTimeConsume() {
        String s = "";
        StringBuffer buffer = new StringBuffer();
        StringBuilder builder = new StringBuilder();
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 100000; i++) {
            buffer.append(i);
        }
        System.out.println("StringBuffer use " + (System.currentTimeMillis() - startTime) + " ms");

        for (int i = 0; i < 100000; i++) {
            builder.append(i);
        }
        System.out.println("StringBuilder use " + (System.currentTimeMillis() - startTime) + " ms");

        for (int i = 0; i < 100000; i++) {
            s += i;
        }
        System.out.println("String use " + (System.currentTimeMillis() - startTime) + " ms");
    }
```
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/1446daae771048868a2cce95c2072908.png)

## 2.2 JDK8之前的时间API
### System静态方法
`System.currentTimeMillis()`: 返回1970年1月1日0点到现在经历了多少毫秒,返回值是long类型

### Date类
有两个Date类
java.util.Date和java.sql.Date(继承前边的类)

**java.util.Date**
1.构造器一:`Date()` 创建了一个对应当前时间的Date对象

2.构造器二`Date(long time)` 创建指定毫秒数的Date类


**java.sql.Date**
对应着数据库中的日期

```java
public class Main {
    public static void main(String[] args) {
        Date date=new Date();
        System.out.println(date);
        //Wed Aug 25 10:49:42 CST 2021
        System.out.println(date.getTime());
        //1629859782884
        Date date2=new Date(1629859782884L);
        System.out.println(date2);
        //Wed Aug 25 10:49:42 CST 2021
        Date date3=new java.sql.Date(2131243242334L);
        System.out.println(date3);
        //2037-07-15
        /*java.util.Date转换为java.sql.Date*/
        //情形一 多态,对象本身就是sql下的Date
        Date date4=new java.sql.Date(2315432424345L);
        java.sql.Date date5= (java.sql.Date) date4;
        //情形二 对象本身就是util下的Date,无法直接强制转换.但是他们拥有一个共同的东西,就是毫秒数
        Date date6=new Date();
        java.sql.Date date7=new java.sql.Date(date6.getTime());
    }
}
```
### Calendar类
`Calendar`是一个**抽象基类**,主要用于完成日期字段之间相互操作的功能

获取Calendar实例的方法
1.使用`Calendar.getInstance()`方法 (抽象类无法实例化,这里返回的是子类GregorianCalendar的对象,多态,用Calendar接收)
2.调用它的子类`GregorianCalendar`的构造器

一个Canlendar的实例是系统时间的抽象表示,通过`get(int field)`方法来取得想要的时间信息.比如YEAR, MONTH, DAY_OF_WEEK, HOUR_OF_DAY,MINUTE, SECOND
1.`public void set(int field, int value)`
2.`public void add(int field, int amount)`
3.`public final Date getTime()`
4.`public final void setTime(Date date)`

**注意**
1.获取月份时:一月是0, 二月是1, 以此类推, 12月是11
2.获取星期时,周日是1,周二是2,...,周六是7

```java
public class Main {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance(); //多态
        System.out.println(calendar.getClass());    //java.util.GregorianCalendar

        //get()
        System.out.println(calendar.get(Calendar.YEAR));
        System.out.println(calendar.get(Calendar.MONTH) + 1);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));

        //add()
        calendar.add(Calendar.DAY_OF_MONTH, -10);   //往前倒10个月

        //getTime()
        Date date=calendar.getTime();

        //setTime()
        calendar.setTime(date); //用date来设置calendar
        calendar.setTimeInMillis(23214325798L); //用毫秒数设置calendar

    }
}
```
### SimpleDateFormat类
用于对Date类的格式化和解析

Date类的API不易于国际化,大部分都被废弃了,`java.text.SimpleDateFormat`类是一个**不与语言环境有关**的方式来格式化和解析日期的具体类

它允许进行**格式化: 日期->文本**, **解析: 文本->日期**

1.格式化
`SimpleDateFormat()`: 默认的模式和语言环境创建对象
`public SimpleDateFormat(String pattern)` 该构造方法可以用参数pattern指定的格式创建一个对象,该对象调用
`public String format(Date date):` 方法格式化时间对象date

2.解析
`public Date parse(String source): ` 从给定字符串的开始解析文本,以生成一个日期

```java
public class Main {
    public static void main(String[] args) throws ParseException {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        /*格式化:date转String*/
        String format = sdf.format(new Date());
        System.out.println(format); //2021-08-25 04:00:54
        /*解析*:String转Date*/
        Date date = sdf.parse("2021-08-25 16:30:20");
        System.out.println(date);   //Wed Aug 25 16:30:20 CST 2021
    }
}
```
**如果用SimpleDateFormat类解析字符串成java.sql.Date**,就先解析为java.util.Date,在用毫秒数转换为java.util.Date

**例题**
1990-01-01开始,三天打鱼两天晒网,请问给定日期在做什么事情?
答:用SimpleDateFomart类解析两个日期,算出毫秒值之差,然后向上整除`1000*3600*24`(这里用向上除法$(m+n-1)/n$), 然后结果+1(代表一共有多少天), 然后对5取模. `0~2`在打渔,`3~4`在晒网

## 2.3 JDK8中新日期的API
JDK1.0包含了一个`java.util.Date`类,但是它的大多数方法已经在JDK1.1引入Calendar类后被弃用了.而Calendar类并不比Date好多少.它们面临的问题是:
1.**可变性**:像日期和事件这样的类,应该是不可变得
2.**偏移性**:Date中的年份是从1900开始的,而月份是从0开始的
3.**格式化**:格式化只对Date有用,Calendar则不行
此外,它们也不是线程安全的; 不能处理润秒等

总结:对日期和时间的操作一直是Java程序员最痛苦的地方之一

第三次引入的API是成功的,并且Java8中引入的`java.time`API已经纠正了过去的缺陷,将在很长一段时间内它都会为我们服务
### LocalDate,LocalTime,LocalDateTime
其中**LocalDateTime**最常用

1.now(): 当前的日期、时间
2.of(): 设置指定的年、月、日、时、分、秒   无偏移量，方便
3.getXxx(): 获取某项时间信息
4.with(): 修改日期,返回修改后的时间对象,**不修改原来的时间对象**
5.plus()/minus(): 增减日期
```java
    public void test(){
        /*now(): 当前的日期、时间*/
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();  //LocalDateTime用的最频繁
        System.out.println(localDate);
        System.out.println(localTime);
        System.out.println(localDateTime);

        /*of(): 设置指定的年、月、日、时、分、秒   无偏移量，方便*/
        LocalDateTime time1 = LocalDateTime.of(2021, 8, 25, 20, 34);
        System.out.println(time1);

        /*getXxx()*/
        System.out.println(localDateTime.getYear());
        System.out.println(localDateTime.getMonth());
        System.out.println(localDateTime.getDayOfMonth());
        System.out.println(localDateTime.getDayOfWeek());
        System.out.println(localDateTime.getDayOfYear());
        System.out.println(localDateTime.getMinute());

        /*with(): 修改日期,返回修改后的时间对象,不修改原来的时间对象*/
        LocalDateTime localDateTime1 = localDateTime.withDayOfMonth(1); //不修改原来的日期,重新返回一个修改后的日期
        System.out.println(localDateTime1);
        System.out.println(localDateTime1);

        LocalDateTime localDateTime2 = localDateTime1.withHour(4);
        System.out.println(localDateTime2);

        /*plus()/minus(): 增减日期*/
        LocalDateTime localDateTime3 = localDateTime.plusMonths(3);
        System.out.println(localDateTime3);
        LocalDateTime localDateTime4 = localDateTime.minusYears(2);
        System.out.println(localDateTime4);
    }
```
### Instant
类似于`java.util.Date`类

| 方法                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| now()                         | 静态方法,返回默认UTC时区的Instant类的对象                    |
| ofEpochMilli(long epochMilli) | 静态方法,返回在1970年1月1日0时0分0秒基础上指定毫秒数后的Instant类对象 |
| atOffset(ZoneOffset offset)   | 结合即时的偏移来创建一个OffsetDateTime                       |
| toEpochMilli()                | 返回1970年1月1日0时0分0秒(UTC)开始的毫秒数 即时间戳          |

```java
    public void test2(){
        /*now(): 获取本初子午线对应的时间标准*/
        Instant time1 = Instant.now();//默认是 本初子午线 的时区

        /*atOffset()结合即时的偏移来创建一个OffsetDateTime量*/
        OffsetDateTime time2 = time1.atOffset(ZoneOffset.ofHours(8));//改成东八区
        System.out.println(time1);
        System.out.println(time2);

        /*toEpochSecond():返回1970年1月1日0时0分0秒(UTC)开始的毫秒数 即时间戳*/
        long l = time2.toEpochSecond();
        System.out.println(l);

        /*ofEpochMilli()静态方法,返回在1970年1月1日0时0分0秒基础上指定毫秒数后的Instant类对象*/
        Instant instant = Instant.ofEpochMilli(1532324124325L);
        System.out.println(instant);
    }
```
### DateTimeFormatter
1.方式一:预定义的标准格式
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/fbce2f0eaa654fb983fce7738ee031ab.png" width=100%>
2.本地化相关的格式
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/b37e9f32887744b0bc1349993c1f2767.png)

3.**自定义的格式**(一般都用这个)
```java
public void test3(){
    //自定义格式
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
    //格式化
    String format = formatter.format(LocalDateTime.now());
    System.out.println(format);
    //解析
    TemporalAccessor parse = formatter.parse("2021-08-25 09:34:46");   //LocalDateTime,LocalDate,LocalTime都实现了TemporalAccessor接口
    System.out.println(parse);
}
```
### 其他类
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/7e50b194a59c4f3ab77971be9f5487e4.png)
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/1f12c768903643f2ab209cb65deaf7a7.png)

## 2.4 Java比较器
### Comparable接口

1. String、包装类等实现了Comparable接口，重写了compareTo()方法，给出了比较两个对象大小的方法
2. 重写comparaTo()的规则：如果当前对象this大于形参对象obj，则返回正整数；当前对象this小于形参对象obj，则返回负数；相等返回0

```java
class Student implements Comparable{
    String name;
    int id;

    public Student() {
    }

    public Student(String name, int id) {
        this.name = name;
        this.id = id;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", id=" + id +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    @Override
    public int compareTo(Object o) {
        if(o instanceof Student){
            Student s=(Student)o;
            return Integer.compare(this.id,s.id);
        }
        throw new RuntimeException("比较的数据类型不是Student");
    }
}
public class p1 {
    @Test
    public void test(){
        Student[] arr=new Student[3];
        arr[0]=new Student("小小余",1);
        arr[1]=new Student("余丰旭",3);
        arr[2]=new Student("陈香玉",2);
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```



### Comparator接口 

1.当元素的类型没有实现java.lang.Comparable接口又不方便修改代码，或者实现饿了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序，强行对多个对象进行整体排序的比较。

2.重写compare(Object o1,Object o2)方法，比较o1和o2的大小：**如果方法返回正整数，则表示o1大于o2来；如果返回0，则表示相等；返回负数，表示o1小于o2**

3.可以将Comparator传递给sort方法（如Collections.sort或Arrays.sort)，从而允许在排序上实现精确控制。

4.还可以使用Comparator来控制某些数据结构（如有序set或者有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序

```java
public class p1 {
    @Test
    public void test(){
        Student[] arr=new Student[3];
        arr[0]=new Student("小小余",1);
        arr[1]=new Student("余丰旭",3);
        arr[2]=new Student("陈香玉",2);
        //Arrays.sort(arr);
        Arrays.sort(arr, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return -Double.compare(o1.id,o2.id);
            }
        });
        System.out.println(Arrays.toString(arr));
    }
}
```

### 两种比较器对比

1. Comparable具有永久性，Comparator是临时使用的
   - Comparable接口一旦指定，保证其实现类对象在任何位置都可以比较大小；Comparator接口属于临时性的比较

## 2.5 System类

![image-20211015165822331](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015165822331.png)

![image-20211015170036496](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015170036496.png)

## 2.6 Math类

![image-20211015170515904](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015170515904.png)

## 2.7 BigInteger和BigDecimal类

![image-20211015170758744](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015170758744.png)

![image-20211015170832228](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015170832228.png)



***



BigInteger对应的是整型，BigDecimal对应的是浮点型

![image-20211015170923073](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211015170923073.png)



# 3.枚举类 

1.枚举类的理解：类的对象只有有限个，确定的。我们称此类为枚举类。

2.当需要定义一组常量时，强烈建议使用枚举类。

3.如果枚举类中只有一个对象，则可以作为单例模式的实现方式

![image-20211018115847328](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211018115847328.png)

**如何定义枚举类**

1.JDK5.0前，自定义枚举类

2.JDK5.0开始，可以使用enum关键字自定义枚举类

```java
class Season{
    //私有化构造器
    private Season(String seasonName,String seasonDesc){
        this.seasonName=seasonName;
        this.seasonDesc=seasonDesc;
    }
    //常量内容（带final的需要赋值）
    private final String seasonName;
    private final String seasonDesc;
    //静态对象
    public static final Season SPRING=new Season("春天","春天开花");
    public static final Season SUMMER=new Season("夏天","夏天下雨");
    public static final Season AUTUMN=new Season("秋天","秋天收麦子");
    public static final Season WINTER=new Season("冬天","冬天堆雪人");

    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    @Override
    public String toString() {
        return "Season{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }
}
public class p6 {
    public static void main(String[] args) {
        Season s1=Season.AUTUMN;
        System.out.println(s1);
    }
}
```



```java
public class p7 {
    public static void main(String[] args) {
        Family father = Family.Father;
        Family mother=Family.Mather;
        System.out.println(father); //打印当前对象的名字
        System.out.println(mother);

        Family[] values = Family.values();
        System.out.println(Arrays.toString(values));

        //看看Family的父类是什么
        System.out.println(Family.class.getSuperclass());
        System.out.println(Family.class.getSuperclass().getSuperclass());
        System.out.println(Object.class);
    }

    @Test
    public void f1(){
        Country[] values = Country.values();
        System.out.println(Arrays.toString(values));
        Country china = Country.valueOf("China");
        System.out.println(china.name());
        System.out.println(china);  //toString方法默认返回name
    }
}

/*如果没有属性，则连小括号也不需要*/
enum Country{
    China,USA,Canada,India;
}

enum Family{
    Father("男生","挣钱养家"),    //注意这里是逗号
    Mather("女生","貌美如花");

    private final String name,duty;

    Family(String mem,String duty){     //默认是private的
        this.name=mem;
        this.duty=duty;
    }

    public String getName() {
        return name;
    }

    public String getDuty() {
        return duty;
    }
}public class p7 {
    public static void main(String[] args) {
        Family father = Family.Father;
        Family mother=Family.Mather;
        System.out.println(father); //打印当前对象的名字
        System.out.println(mother);

        //看看Family的父类是什么
        System.out.println(Family.class.getSuperclass());
        System.out.println(Family.class.getSuperclass().getSuperclass());
        System.out.println(Object.class);
    }
}

enum Family{
    Father("男生","挣钱养家"),    //注意这里是逗号
    Mather("女生","貌美如花");

    private final String name,duty;

    Family(String mem,String duty){     //默认是private的
        this.name=mem;
        this.duty=duty;
    }

    public String getName() {
        return name;
    }

    public String getDuty() {
        return duty;
    }
}
```

**enum类的主要方法**

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211018200409123.png" alt="image-20211018200409123" style="zoom: 80%;" />

> 重点关注values()（返回常量对象数组） ,valueOf(String str), toString()方法

**枚举类实现接口**  

1.在enum类中实现抽象方法

2.让枚举类的对象分别实现接口中的抽象方法

```java
interface Info{
    void show();    //默认是public abstract的
}
//由枚举类来实现抽象方法
enum Planet implements Info{
    Earth,Mars,Sun,Moon;
    @Override
    public void show(){     //必须是public，因为抽象方法是public，重写权限必须大于等于原来的权限
        System.out.println("这颗星球是"+this.name());
    }
}

//每个对象分别实现接口的抽象方法
enum Lanuage implements Info{
    Chinese{
        @Override  
        public void show(){
            System.out.println("中文");
        }
    },English{
        @Override
        public void show() {
            System.out.println("英文");
        }
    },French {
        @Override
        public void show() {
            System.out.println("法语");
        }
    };
}
```



# 4.注解

## 4.1概述

- 从JDK5开始，java增加了对元数据（MetaData）的支持，也就是Annotation（注解）
- Annotation其实就是代码里的**特殊标记**，这些标记可以在编译，类加载，运行时被读取，并执行相应的处理。通过使用Annotation，程序员可以在不改变原有逻辑的情况下，在源文件中**嵌入一些补充信息**。代码分析工具、开发工具和部署工具可以通过这些补充信息进行验证或者进行部署。
- Annotation可以像修饰符一样被使用，可用于**修饰包、类、构造器、方法、成员变量、参数、局部变量的声明**，这些信息被保存在Annotation的“name=value”对中。
- 在JavaSE中，注解的使用目的比较简单，例如标记过时的功能，忽略警告等。在JavaEE/Android中注解占据了更重要的角色，例如用来配置应用程序的任何切面，代替JavaEE旧版中所遗留的繁冗代码和XML配置等。
- 未来的开发模式都是基于注解的，JPA（Java Persistence API持久层API）是基于注解的，Spring2.5以上都是基于注解的，Hibernate3.X以后也是基于注解的，现在的Struts2有一部分也是基于注解的了，注解是一种趋势，一定程度上可以说：**框架=注解+反射+设计模式**

## 4.2常见的Annotation示例

常见的三种用法：

1. 生成文档相关注释
2. 在编译时进行格式检查（JDK内置的三个基本的注解）
3. 跟踪代码依赖性，实现替代配置文件功能

![image-20211019205807182](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211019205807182.png)



![image-20211019205833959](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211019205833959.png)

![image-20211019205905165](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211019205905165.png)



![image-20211019210011651](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211019210011651.png)

![image-20211019210314315](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211019210314315.png)

## 4.3自定义Annotation

1. 注解声明为`@interface`
2. 内部定义成员，通常使用value标识
3. 可以指定成员的默认值，使用default定义
4. 如果自定义注解没有成员，表明是一个标识作用
5. 如果注解有成员，在使用注解时，需要指明成员的值（除非有默认值）

![image-20211020185549611](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211020185549611.png)

## 4.4JDK中的元注解

jdk提供了四种元注解，**元Annotation用于修饰其他Annotation定义**。

- Retention：指定所修饰的Annotation的生命周期：SOURCE\CLASS（默认行为）\RUNTIME

  - 只有声明为RUNTIME生命周期的注解，才能通过反射获取

- Target：用于修饰Annotation定义，用于指定被修饰Annatation能用于哪些程序元素。`@Target`也包含了一个名为value的成员变量

  - ![image-20211020193009794](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211020193009794.png)

- Documented：用于指定被该元Annotation修饰的Annotation类将被javadoc工具提取成文档。默认情况下，javadoc是不包括注解的。

  - 定义为Documented的注解必须设置Retention值为Runtime。

- Interited：被它修饰的Annotation将具有**继承性**。如果某个类使用了被`Inherited`修饰的Annotation，则其子类自动具有该注解。

  - 实际应用中，使用较少

  - ```java
    @Inherited
    @Retention(RetentionPolicy.RUNTIME)
    @interface MyAnnotation{
        String values() default "aaa";
    }
    
    
    @MyAnnotation(values = "hello")
    public class p1 {
        @Test
        public void test() {
            for(Annotation a:p2.class.getAnnotations())
                System.out.println(a);
        }
    }
    
    class p2 extends p1{
    
    }
    
    //执行结果：@October.day04.MyAnnotation(values="hello")
    ```

> 自定义注解一般都会指明Retention，Target

## 4.5利用反射获取注解信息



## 4.6JDK8中的新注解

## 可重复注解

1.在`OneAnnotation`上声明`@Repeatable`，成员值为`OneAnnotations.class`

2.`OneAnnotation`的Target和Retention，必须和`OneAnnotations`相同

```java
@Repeatable(OneAnnotations.class)
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE,ElementType.FIELD,ElementType.METHOD})
@interface OneAnnotation {

}

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE,ElementType.FIELD,ElementType.METHOD})
@interface OneAnnotations {
    OneAnnotation[] value();
}
```

## 类型注解

- JDK1.8以后，关于元注解`@Target`的参数类型ElementType枚举值多了两个：`TYPE_PARAMETER,TYPE_USE`

- 在Java 8之前，注解只能是在声明的地方所使用，Java8开始，注解可以应用在任何地方

```java
public class P4<@SecondAnnotation T> {
    public void show() throws @SecondAnnotation RuntimeException{
        ArrayList<@SecondAnnotation String>list=new ArrayList<>();
        int num=(@SecondAnnotation int)10L;
    }
}


@Target({ElementType.TYPE_PARAMETER,ElementType.TYPE_USE})
@interface SecondAnnotation{

}
```

# 5.Java集合

## Java集合框架概述

Java集合可以分为`Collection`和`Map`两种体系

- Collection接口：单列数据，定义了存取一组对象的方法的集合
  - List：元素有序、可重复的集合
  - Set：元素无序、不可重复的集合
- Map接口：双列数据，保存具有映射关系`key-value`对的集合

> Collection接口中存储自定义类的对象，除了TreeSet(自然排序重写compareTo，定制排序重写compare)，其他基本都需要重写equals，Set中需要重写hashCode和equals自不用说， List中要重写equals，主要是后续用于remove，contents等方法。



Collection接口继承树

![image-20211020213052703](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211020213052703.png)

Map接口继承树

![image-20211020213128277](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211020213128277.png)

## Collection接口方法

![image-20211021093805529](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211021093805529.png)

> 注意 contains(target)内部比较调用的是equals方法，**不需要**target和内部对应元素拥有相同的引用。
>
> A.retainAll(B)求交集，结果存放在A
>
> col1.equals(col2)，要求两者元素相同，并且顺序相同
>
> hashcode()，根据内容返回哈希值；内容不同，哈希值也不同。



- 将数组元素全部读入ArrayList中
  - `List<Integer> col = new ArrayList<>(Arrays.asList(123,456));`
  - 注意Arrays.asList返回的不是ArrayList类型，而是`java.util.Arrays$ArrayList`类型，实现了List接口。
  - 而ArrayList的构造函数可以传入一个Collection类型的数据。

## Iterator迭代器接口

1.Iterator对象称为迭代器（设计模式的一种），主要用于遍历Collection集合中的元素。

2.**GOF给迭代器模式的定义为：提供一种方法访问一个容器（Collection）对象中各个元素，而又无需暴露该对象的内部细节。迭代器模式，就是为容器而生**。类似于公交车上的售票员，火车上的乘务员。

3.Collection接口继承了java.lang.Iterable接口，该接口有一个iterator()方法，那么所有实现了Collection接口的集合类都有一个iterator()方法，用于返回一个实现了Iterator接口的对象。

4.**Iterator仅用于遍历集合**，Iterator本身并不提供承装对象的能力。如果需要创建Iterator对象，则必须有一个被迭代的集合。

5.**集合对象每次调用iterator()方法都得到一个全新的迭代器对象**，默认游标都在集合的第一个元素之前。

```java
@Test
public void test1(){

    Collection col=new ArrayList<>(Arrays.asList("abc","def","..."));

    Iterator it= col.iterator();

    while(it.hasNext())
        System.out.println(it.next());

}
```

`default void remove()`方法的使用

> 注：default方法指的是接口中可以写方法体的方法，实现类直接继承，无需重写

```java
public void test2(){
    Collection<String>col=new ArrayList<>(Arrays.asList("abc","yfx","cxy"));
    Iterator<String>it=col.iterator();
    while(it.hasNext()){
        Object obj=it.next();
        if("abc".equals(obj))
            it.remove();
    }
    //col.removeIf("abc"::equals); //等价写法
    it=col.iterator();  //需要从头获取
    while(it.hasNext())
        System.out.println(it.next());
}
```

注意：

1.Iterator可以删除集合的元素，但是是遍历过程中通过迭代器对象的remove方法，不是集合对象的remove方法

2.如果还未调用next()或者在上一次调用next方法之后已经调用了remove方法，再调用remove都会报`IllegalStateException`



**Iterator的执行原理**：

iterator首先指向第一个对象之前的位置。

![image-20211026101834161](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211026101834161.png)

> 注：Iterator不可用于Map集合



**增强for循环，内部也是使用了Iterator来实现的。**

```java
for(String s:arr)	//将arr中的元素赋值给s
    s="abc";	//无法改变原有的内容
```

## Collection子接口一：List

List集合类中元素**有序、且可重复**，集合中的每个元素都有其对应的顺序索引。



List三个实现类的不同点：

- ArrayList：作为List接口的主要实现类；线程不安全，效率高；底层使用`Object[] elementData`存数组储
- LinkedList：对于频繁的插入、删除操作，使用此类效率比ArrayList高；底层使用双向链表存储；
- Vector：作为List接口的古老实现类；线程安全的，效率低；底层使用`Object[] elementData`数组存储

相同点：

- 都实现了List接口，存储数据的特点相同：存储有序的、可重复的数据

### ArrayList

**源码分析**（jdk7和jdk8中有所不同）

> IDEA ctrl+f12 查看类的所有方法

结论：建议开发中使用带参的构造器：`new ArrayList(int capacity);`，如果使用空参构造器，可能会发生频繁扩容（复制数组），降低效率

### LinkedList

略

### Vector

略

作为List接口的古老实现类；线程安全；效率低；底层使用`object[]`



### List接口中的常用测试方法

List中除了从Collection集合继承的方法外，List集合里添加了一些根据索引来操作集合元素的方法

- `void add(int index,Object ele)`
- `Object get(int index)`
- `Object remove(int index)`
- `Object set(int index,Object ele)`



## Collection子接口二：Set

Set：存储无序的、不可重复的数据

以HashSet为例（TreeSet底层不是数组，是二叉树了）

1. 无序性：不等于随机性，存储的数据在底层数组（TreeSet底层不是数组）中并非按照数据索引的顺序添加，而是根据数据的hash值
2. 不可重复性：hash值不同，或者hash值相同，但equals不同
   - 底层是用数组+链表/二叉树，来实现存储的。
   - 向HashSet中添加元素a，先调用a的HashCode()方法，计算a的hash值，然后通过某种算法计算出a在底层数组中的存放位置（索引位置），判断数组在此位置上是否已经有元素，如果没有则添加成功，如果有则跟此位置挂载的链表/红黑树，上的元素进行比较（调用equals），如果均不相同则插入，否则产生重复不插入。

### HashSet

底层用的是HashMap

特点：

1. 线程不安全
2.  可以存储null值

### LinkedHashSet

是HashSet的子类，可以按照添加顺序遍历元素。

底层是hash表+双向链表（leetcode有一题LRU算法就是用这个数据结构来实现的）

### TreeSet

特点：

1. **只能放同一类的对象**
2. 两种排序方式：自然排序（Comparable）、定制排序(Comparator)
   - **自然排序，自定义类型必须实现Comparable接口**
3. 可以按照添加对象的指定属性，进行排序
4. **TreeSet中判断是否相同，使用的是CompareTo（自然排序）/compare（定制排序），而非equals**
5. TreeSet底层用的是TreeMap

## Map接口

Map中的key：无序、不可重复，使用Set存储所有key（key的类要重写equals和hashCode方法，TreeMap则需要自然排序或定制排序）

Map中的value：无序、可重复，使用Collection存储所有的value

`key-value`键值对：构成了一个Entry对象

Map中的entry：无序、不可重复，使用Set存储所有的entry

**注意，Map没有Iterator，不可直接迭代，需要使用元视图keySet()、entrySet()、values()的结果才能迭代**



<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211224214935079.png" alt="image-20211224214935079" style="zoom:67%;" />

> 注意，Hashtable的t是小写

**Map接口常用方法**

1. 增删改
   - `Object put(Object key,Object value)`
   - `void putAll(Map m)`
   - `Object remove(Object key)`
   - `void clear（）`
2. 查询操作
   - `Object get(Object key)`
   - `boolean containsKey(Object key)`
   - `boolean containsValue(Object value)`
   - `int size()`
   - `boolean isEmpty()`
   - `boolean equals(Object obj)`
3. 元视图操作
   - `Set keySet()`：返回所有key构成的set集合
   - `Collection values()`：返回所有value构成的Collection集合
   - `Set entrySet`：返回所有key-value构成的Set集合

### HashMap

HashMap是Map接口的主要实现类

HashMap特点：

1. 线程不安全
2. 效率高
3. 可以存储null的key和value
4. **和ArrayList一样，即使涉及线程安全问题，也可以用Collections来改造使之线程安全**

HashMap存储数据的过程：

![image-20211225150203537](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211225150203537.png)

![image-20211225150801352](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211225150801352.png)

读源码注释的收获：

1. 扩容会将原有的hash表结构破坏，所有key的位置需要重新计算，因此代价非常高。如果HashMap需要存储的数据量很大，则最好预先制定好capacity。
2. capacity大小需要合适，如果太大占用空间且迭代效率低（迭代时间复杂度O(N+capacity)，N是key的数量），如果太小需要经常扩容，扩容需要全部重新计算hash，即重新计算key的位置，代价非常大。
3. 状态因子，如果大了，空间利用率高，但是容易产生冲突；如果小了，空间利用率低，但不容易产生冲突；一般用0.75比较合适
4. threshold阈值=capacity*loadfactor，hash表上元素个数（不包括链表/红黑树上的元素）超过此阈值要扩容（两倍）

### Hashtable

Hashtable是古老实现类。

其子类Properties常用来处理配置文件。

![image-20211226093527904](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211226093527904.png)

**LinkedHashMap**

保证在遍历map元素时，可以按照添加的顺序实现遍历。

原理：hash表+双向链表

对频繁的遍历操作，此类执行效率高于HashMap（支持LRU算法）

HashMap的底层：

1. 数组+链表（jdk7及之前）
2. 数字+链表/红黑树（jdk8）



### TreeMap

按照key-value中的key进行排序（**要求key是同一个类的对象**），需要实现自然排序/定制排序



### ConcurrentHashMap

使用分段锁（数据分段加lock），在多线程使用时安全且高效

## 其他常用类

### PriorityQueue

默认是小根堆

如果要生成大根堆，则传入比较器，重写compare方法

```java
A=new PriorityQueue<>(new Comparator<Integer>() {
			@Override
			public int compare(Integer o1, Integer o2) {
				return o2-o1;
			}
});
```

或者用lamdba表达式

```java
B=new PriorityQueue<>((x,y) -> (y-x));
//注意，lambda表达式内部不用写逗号
```



## Collections工具类

Collections是操作**Set,List,Map**等集合的工具类

1. 排序操作（均为static方法）
   - reverse(List)
   - shuffle(List)
   - sort(List)
   - sort(List,Comparator)
   - swap(List,int,int)
2. 查找、替换
   - Object max(Collection)
   - Object max(Collection, Comparator)
   - Object min(Collection)
   - Object min(Collection, Comparator)
   - int frequency(Collection, Object) ：返回指定集合中指定元素出现的次数
   - void copy(List dest, List src)：将src中的内容复制到dest中
   - boolean replaceAll(List list, Object oldVal, Object newVal)：使用新值替换List对象的所有旧值
3. 同步控制（将对应类型扔进参数即可），底层仅仅用synchronized包装了原有的方法
   - synchronizedCollection
   - synchronizedList
   - synchronizedMap
   - synchronizedSet
   - synchronizedSortedMap
   - synchronizedSortedSet

# 6.泛型

1. 泛型类可能有多个参数，此时应该将多个参数一起放在尖括号内，如`E1,E2,E3`
2. 泛型类的构造器如下：`public GenericClass(){}`
   - 这种写法是错误的：`public GenericClass<E>(){}`
3. 实例化后，操作原来泛型位置的结构必须与指定的泛型类型一致
4. 泛型不同的引用不能相互赋值
   - 尽管在编译时ArrayList\<String>和ArrayList\<Integer>是两种类型，但是在运行时都只会被当做ArrayList被加载到JVM中。
5. 泛型如果不指定，将被擦除，泛型对应的类型均按照Object处理，但不等价于Object
   - 即泛型要用，就一路都用；要不用，就都不用
6. 如果泛型类是一个接口或抽象类，则不可创建泛型类的对象
7. 泛型的指定中不能使用基本数据类型，可以使用包装类替换
8. 在类/接口上声明的泛型，在本类或本接口中即代表某种类型，可以作为非静态属性的类型、非静态方法的参数类型、非静态方法的返回值类型。**但在静态方法中，不能使用类的泛型**（本质原因：泛型是在生成对象的时候指定的，静态结构早于对象创建）
   - 泛型方法可以是静态方法，因为泛型参数是在调用方法时确定的，并非在实例化类的时候确定。
9. 异常类，不能是泛型的
10. 不能使用`new E[]`，但是可以`E[]elements=(E[])new Object[capacity]`
    - 参考：ArrayList源码中生命：`Object[] elementData`，而非泛型参数类型数组
11. 父类有泛型，子类可以选择保留泛型也可以选择指定泛型类型
    - 子类不保留父类的泛型，按需实现
      - 没有类型 擦除
      - 具体类型
    - 子类保留父类的泛型，泛型子类
      - 全部保留
      - 部分保留

```java
class Father<T1,T2>{}

//1. 没有类型，擦除
class Son1 extends Father{}
//2. 具体类型
class Son2 extends Father<Integer,String>{}
//3. 子类保留父类的泛型
//3.1 全部保留
class Son3<T1,T2>extends Father<T1,T2>{}
//3.2 部分保留
class Son4<T2>extends Father<Integer,T2>{}
```

![image-20211226143304180](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211226143304180.png)



## 为何需要泛型

1. 解决元素存储的安全性问题
2. 解决获取数据元素时，需要强制类型转换的问题

## 在集合中使用泛型

如果没有使用泛型，默认是java.lang.Object类型

## 自定义泛型结构

泛型类、泛型接口、泛型方法



泛型方法：在方法中出现了泛型的结构，**泛型参数与类的泛型参数没有任何关系！**

- 换句话说，类中指明了`<E>`，而方法中用到了`<T>`，才能叫泛型方法。**泛型方法所属的类是不是泛型类都没有关系**

```java
public <E> List<E> copyFromArrayToList(E[] arr){...}
//最前面需要声明泛型E，否则编译器认为E是一个类名，会报错
```

**泛型方法可以是静态的！泛型方法可以是静态方法，因为泛型参数是在调用方法时确定的，并非在实例化类的时候确定。**

## 泛型在继承上的体现

子类在继承泛型时，指明了泛型类型。则实例化子类对象时，不再需要指明泛型

```java
public class SubOrder extends Order<Integer>{
    ...
} //SubOrder:不是泛型类

public class SubOrder2<T> extends Order<T>{
    ...
}	//SubOrder<T>仍然是泛型类
```

1. 类A是类B的父类，G\<A>和G\<B>二者不具备子父类关系，二者是并列关系

![image-20211226145634905](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211226145634905.png)

2. 下面这个是可以的

![image-20211226150152685](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211226150152685.png)

## 通配符的使用

 ![image-20211226150512810](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211226150512810.png)

需要泛型通配符的根本原因在于，泛型之间都平行关系（即使两个泛型类型自身存在父子关系），因此如果没有泛型的话，要兼容同一子类下的所有所有类型，必须要重载很多函数。

### 有限制条件的通配z符

- `<?>`允许所有泛型的引用调用
- 通配符指定上限，上限extends：使用时指定的类型必须是继承某个类，或者实现某个接口，即`<=`
- 通配符指定下限，下限super：使用时指定的类型不能小于操作的类，即`>=`
- 举例
  - `<? extends Number>`
  - `<? super Number>`
  - `<? externds Comparable>` 只允许泛型为实现了Comparable接口的实现类的引用调用

# 7.IO流

## File类的使用

###　基本知识

- 绝对路径与相对路径
  - File中的相对路径（IDEA）：
    - 如果在main方法中写，则是相对于工程的根目录（一个工程下可以有多个module）
    - 如果是使用Junit单元测试测试方法，相对路径即为当前module下
  - Eclipse中，均是相对于Project根目录下

- 路径分隔符

  - windows和DOS系统采用`\`来标识

  - UNIX和URL使用`/`来标识

  - 为了解决这个隐患，File类提供一个常量`public static final String separator`，根据操作系统，动态地提供分隔符

  - 注：在windows下写成`/`也能识别

- 三种构造器

  1. 直接写地址
  2. parent地址+child地址
  3. File+child地址

- 创建File时，若硬盘中有对应文件，则File对象的各属性会显式赋值；否则，除了指定目录和路径外，其余属性都取默认值。

### 常用方法

- File类的获取功能
  - `public String getAbsolutePath()`：获取绝对路径
  - `public String getPath()`：获取路径
  - `public String getName()`：获取名称
  - `public String getParent()`：获取上层文件目录路径，若无，返回null
    - 如果用相对路径创建File，路径名只有一级的话，返回null
  - `public long length()`：获取文件长度（字节数）不能获取目录的长度
  - `public long lastModified`：获取最后一次的修改时间，毫秒值
  - `public String[]list()`：获取指定目录下的所有文件或文件目录的**名称**数组
  - `public File[]listFiles()`：获取指定目录下的所有文件或文件目录的File数组
- File类的重命名功能
  - `public boolean renameTo(File des)`：把文件重命名为指定的文件路径
    - 底层是native方法，跟平台相关
    - linux下des只能在同一文件夹下，我的理解：其目的是重命名，所以只能在同一目录下进行。
- File类的判断功能
  - `public boolean isDirectory()`：判断是否是文件目录
  - `public boolean isFile()`：判断是否是文件
  - `public boolean exists()`是否存在
  - `public boolean canRead()`是否可读
  - `public boolean canWrite()`是否可写
  - `public boolean isHidden()`是否隐藏

## IO流原理及流的分类

- 按操作**数据单位**不同分为：字节流，字符流
- 按数据流的**流向**不同分为：输入流，输出流
- 按流的角色的不同分为：节点流，处理流

|            | **字节流**   | **字符流** |
| ---------- | ------------ | ---------- |
| **输入流** | InputStream  | Reader     |
| **输出流** | OutputStream | Writer     |

- Java的IO流共40多个类，实际上非常规则，都是以上四个抽象基类派生的
- 由这四个类派生出来的子类名称都是以其父类名作为后缀

| 分类            | 字节输入流           | 字节输出流            | 字符输入流        | 字符输出流         |
| --------------- | -------------------- | --------------------- | ----------------- | ------------------ |
| 抽象基类        | InputStream          | OutputStream          | Reader            | Writer             |
| 访问文件/节点流 | FileInputStream      | FileOutputStream      | FileReader        | FileWriter         |
| 访问数组        | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader   | CharArrayWriter    |
| 访问管道        | PipedInputstream     | PipedOutputStream     | PipedReader       | PipedWriter        |
| 访问字符串      |                      |                       | StringReader      | StringWriter       |
| 缓冲流          | BufferedInputStream  | BufferOutputStream    | BufferedReader    | BufferedWriter     |
| 转换流          |                      |                       | InputStreamReader | OutputStreamWriter |
| 对象流          | ObjectInputStream    | ObjectOutputStream    |                   |                    |
|                 | FilterInputStream    | FilterOutputStream    | FilterReader      | FilterWriter       |
| 打印流          |                      | PrintStream           |                   | PrintWriter        |
| 推回输入流      | PushbackInputStream  |                       | PushbackReader    |                    |
| 特殊流          | DataInputStream      | DataOutputStream      |                   |                    |

- 字符流一次读几个字节？
  - 我的理解：看到FileReader源码中提到字符集的问题，应该是根据字符集来的
- 为什么不能用字符流处理图片（如复制图片后会损坏打不开）？
  - 我的理解：字符流在读取文件后，会根据指定的（或默认）字符集转换原有的编码，因此二进制文件已经被改变，所以图片损坏。（如utf-32是4字节一个字符，utf-8是可变长编码字符）

- 文本文件：`.txt` `.java` `.c` 使用字符流进行处理
- 非文本文件：`.jpg` `.mp3` `.doc` `.ppt` 使用字节流进行处理

## 节点流（又叫文件流）

**FileReader如果要一个一个读，直接用read()方法，不加参数，返回的是int类型，如果值是-1代表文件末尾，如果不是-1则转化为char类型即可**

```java
public void test1() {
    File file = new File("demo");

    FileReader reader = null;
    try {
        reader = new FileReader(file);
        int data;
        while ((data = reader.read()) != -1)
            System.out.print((char) data);

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if(reader!=null)
                reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

为什么不推荐使用throws IOException?

- 因为一共有三个地方抛异常，如果中间抛异常，会导致流没有close
- java垃圾回收机制，只能回收JVM堆内存里的对象空间；对于其他物理连接，比如数据库连接、输入输出流、Socket连接无能为力。

```java
public void test2() {
    File file = new File("demo");
    FileReader fr = null;
    try {
        fr = new FileReader(file);
        char[] buff = new char[3];
        int len;//需要len的原因是，结尾处可能读不满buff
        while ((len = fr.read(buff)) != -1) { //每次按照buff的长度来读取字符，返回读取的字符的数量
            //for (int i = 0; i < len; i++)
            	//System.out.print(buff[i]);
            //方式二
            System.out.print(new String(buff, 0, len));
        }
        ;
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fr != null)
                fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



为什么流需要关闭？

```
因为当我们用了虚拟机以外的资源，比如端口、显存、文件的时候（访问本地资源），超出了虚拟机能够释放资源的界限，这时候虚拟机并不能通过垃圾回收机制对你占用的资源进行释放，如果在没有关闭的情况下第一次运行程序是不会发生问题的，但是随着资源占有量逐渐增多而你又不自己进行close释放资源的话，垃圾只会越积越多，导致系统无法存储其他的资源，甚至会导致系统崩溃。如果你再次对未关闭流的文件进行读写，编译就会报错，告诉你这个文件被占用。

举例说明：如果你再堆区new一个对象的时候，如果等号左边是对端口、显存、文件进行操作的时候，虚拟机就无法利用垃圾回收机制对堆区占用的资源进行释放。
```



```
虽然都是 InputStream，但不同的物理实现，close() 的物理作用也有所不同，所以忘记了 close() 的后果也不一样。

比如 ByteArrayInputStream 这种输入流，close() 与否就没什么关系。

楼主的例子中，实际的输入流是一个网络连接。如果忘记 close() 的话，它会一直占用着，直到 timeout 后系统自动关闭这个网络连接。也就是说，如果你忘记了关闭这个资源，到了一定的时候，系统会替你关闭。但如果类似这样的程序被执行很多次，就会有大量资源被长时间无效占用，极端情况下可能导致后续程序资源不足而失败。

也不是所有的资源都能自动回收，比如 FileInputStream，你要是忘记关闭了，它就一直占用着，直到 JVM 退出。

所以，回过来说，好的编程习惯是：主动用程序严格控制对资源的释放，而不必区分其具体的物理实现，不要指望系统级的回收机制。这样能保证程序的可读性和可靠性。

```

总结一下，针对内存的流可以不关闭，但是使用了JVM外界的资源的流如文件、端口、数据库连接等，JVM无法在垃圾回收时进行关闭并清理对应的内存对象，因此需要手动close（可以使用try-with-resources方式进行关闭）。如果一直不关，会导致外界资源一直被占用，对应的内存对象无法回收。

```java
public void test1() {
    File file = new File("demo");
    FileWriter fw = null;
    try {
        fw = new FileWriter(file,true); //第二个参数表示是否追加写入
        fw.write("\n你在赣神魔！");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fw != null)
                fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    System.out.println("成功！");
}
```

为什么close要放在finally中？

- 如果不写在finally中，如写在try或catch或try-catch代码块后面，try或者catch中可能会出现跳过close的代码，如thow、return、continue、break(如下代码所示)

- 不放在catch中，是因为可能有多个具体的异常（多个catch），需要写很多close，不如统一写在finally中

> 注意：try-catch的目的就是为了处理异常，使得后续代码可以继续运行。因此try-catch中出现了异常，程序不会直接退出，后续代码会继续执行

```java
for(int i=0;i<100;i++){
    try{
    	if(..)
            continue;
    }catch(){
        
    }
    close();	//如果continue执行，则无法close
}
```

使用filereader和filewriter进行文件的复制

```java
/*复制文件*/
@Test
public void test2() {
    File f1 = new File("src.txt");
    File f2 = new File("target.txt");
    FileReader fr = null;
    FileWriter fw = null;
    try {
        fr = new FileReader(f1);
        fw = new FileWriter(f2,true);   //true:追加写入，false：清空文件重新写入
        char[]buf=new char[10];
        int len;
        while((len=fr.read(buf))!=-1){
            fw.write(buf,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fr != null)
                fr.close();
        }catch (IOException e){
            e.printStackTrace();
        }
        try {
            if (fw != null)
                fw.close();
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
```

使用字节流复制图片

```java
/*使用字节流复制图片*/
@Test
public void test3() {
    File f1 = new File("皮卡丘.jfif");
    File f2 = new File("皮卡丘副本.png");

    FileInputStream fis = null;
    FileOutputStream fos = null;
    try {
        fis = new FileInputStream(f1);
        fos = new FileOutputStream(f2);
        byte[]buf=new byte[1000];
        int len;
        while((len=fis.read(buf))!=-1){
            fos.write(buf,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fis != null)
                fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if (fos != null)
                fos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    System.out.println("复制完成！");
}
```



## 缓冲流

处理流的一种

缓冲流是为了提高文件的读写效率

- 我认为其效果就如同中断方式的IO和DMA方式的IO

![image-20211231155043835](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211231155043835.png)

没有缓冲意味着：包工头指着一块砖头对着工人说你给我从A搬到B地，然后再发出命令你把下一块砖头从A搬到B，重复直至搬完；有了缓冲，包工头可以说你把这100块砖头从A搬到B，然后等工人搬完再下达命令，直到所有砖头搬完（方法二节省了很多无效时间）

```java
public static void copyFileByBuf(String url) {
    File file = new File(url);
    File copy = new File("副本" + file.getName());
    FileInputStream fis = null;
    FileOutputStream fos = null;
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    try {
        fis = new FileInputStream(file);
        fos = new FileOutputStream(copy);
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);
        int len;
        byte[]buf=new byte[1024];	//buf的大小影响文件读写效率
        while((len=bis.read(buf))!=-1){
            bos.write(buf,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (bis != null)
                bis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if (bos != null)
                bos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**注意，将外层处理流关闭后，内层的节点流会自动关闭**

## 转换流

处理流的一种

1. 转换流提供了在字节流和字符流之间的转换
2. Java API提供了两个转换流
   - `InputStreamReader`：将InputStream转换为Reader
   - `OutputStreamWriter`：将Writer转换为OutputStream
3. 字节流中的数据都是字符时，转换成字符流操作更高效（为什么？没查到原因）
4. 很多时候我们使用转换流来处理**文件乱码**的问题，实现编码和解码的功能
5. 字符集
   - ASCII：美国标准信息交换码
   - ISO8895-1：拉丁码表：欧洲码表。
   - GB2312：中国的中文编码表。最多两个字节编码所有字符
   - GBK:中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码。
   - Unicode：国际标准码，融合了目前人类使用的所有字符。为每一个字符分配唯一的字符码。
     - Unicode只是定义了一个庞大的、全球通用的字符集，并为每个字符规定了唯一确定的编号，具体存储成什么样的字节流，取决于字符编码方案。推荐的Unicode编码是**UTF-8和UTF-16**。
   - UTF-8:变长的编码方法，可用1-4个字节来表示一个字符。

![image-20211231163746622](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211231163746622.png)

```java
public void test1() {
    InputStreamReader isr = null;
    OutputStreamWriter osw=null;
    try {
        isr = new InputStreamReader(new FileInputStream("src.txt"), StandardCharsets.UTF_8);
        osw=new OutputStreamWriter(new FileOutputStream("gbk_src.txt"),"gbk");
        int len;
        char[] buf = new char[1024];
        while ((len = isr.read(buf)) != -1) {
            //                System.out.println(new String(buf, 0, len));
            osw.write(buf,0,len);
        }

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if(isr!=null)
                isr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            if(osw!=null)
                osw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



![image-20220101103822036](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220101103822036.png)

![image-20220101103915239](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220101103915239.png)

## 标准输入输出流

```java
/*从键盘输入字符串，要求将读取到的整行字符串转换成大写输出。然后继续进行输入操作
     * 直至输入e和exit时，退出程序*/
/*方法1：使用Scanner实现
     * 方法2：使用System.in实现。 System.in --> 转换流 --> BufferedReader的readline()*/
public void test2() {
    BufferedReader br = null;
    try {
        InputStreamReader reader = new InputStreamReader(System.in, StandardCharsets.UTF_8);
        br = new BufferedReader(reader);
        while (true) {
            String t = br.readLine();
            if ("e".equalsIgnoreCase(t) || "exit".equals(t.toLowerCase())) {
                System.out.println("程序结束...");
                return;
            }
            System.out.println(t.toUpperCase());
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (br != null) {
            try {
                br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

模拟实现Scanner效果

```java
public static void main(String[] args) throws IOException {
    MyInput myInput = new MyInput(System.in);
    while (true) {
        //            int a= myInput.readInt();
        //            System.out.println(a);
        //            System.out.println(myInput.readString());
        System.out.println(myInput.readFloat());
    }
}

class MyInput {
    private BufferedReader reader = null;

    public MyInput(InputStream input) {
        reader = new BufferedReader(new InputStreamReader(input));
    }

    /*输出字符，直到遇到空格或回车停止*/
    public int readInt() throws IOException {
        StringBuilder sb = new StringBuilder();
        while (true) {
            char c = (char) reader.read();
            if (c >= '0' && c <= '9') {
                sb.append(c);
            } else {
                if (sb.length() == 0)   //防止返回空的数字
                    continue;
                return Integer.parseInt(sb.toString());
            }
        }
    }

    public String readString() throws IOException {
        StringBuilder sb = new StringBuilder();
        while (true) {
            char c = (char) reader.read();
            if (c == ' ' || c == '\t' || c == '\n') {
                if (sb.length() == 0)
                    continue;
                return sb.toString();
            } else
                sb.append(c);

        }
    }
    public float readFloat()throws IOException{
        StringBuilder sb=new StringBuilder();
        while(true){
            char c=(char)reader.read();
            if(c>='0' && c<='9' || c=='.'){
                sb.append(c);
            }else{
                if(sb.length()==0)
                    continue;
                /*输入的浮点数格式不正确*/
                try{
                    return Float.parseFloat(sb.toString());
                }catch (NumberFormatException e){
                    e.printStackTrace();
                }
            }
        }
    }
}
```



## 打印流

了解即可

Sytem.out就是一个打印流

![image-20220101134612613](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220101134612613.png)

**将System.out的打印流从控制台重定向到自定义的输出流中，然后写入文件**

```java
FileOutputStream fos=new FileOutputStream("testSystemOutRedirect.txt",true);	//追加写入
PrintStream ps=new PrintStream(fos);	
System.setOut(ps);	//重定向System输出，到自定义的ps打印流
InputStreamReader reader=new InputStreamReader(System.in);
int t;
while((t=reader.read())!=-1){
    char c=(char)t;
    System.out.print(c);
}
```

## 数据流

了解即可

- 作用：为了方便操作Java语言的基本数据类型和String的数据，可以使用数据流（**可以理解为专门为Java基本数据类型和String类型持久化和读取的流，其生成的文件可以理解为一种特殊文件，用于持久化存储java基本数据类型和String**）
- 读取不同类型的数据的顺序，要与当初写入文件时的顺序一致！
- 数据流有两个类：用于读取和写出基本数据类型、String类的数据
  - DataInputStream和DataOutputStream
  - 分别“套接”在InputStream和OutputStream子类的流上
- DataInputStream中的方法
  - `boolean readBoolean()`
  - `char readChar()`
  - `double readDouble`
  - ...
  - `String readUTF()`
  - `void readFully(byte[] b)`

- DataOutputStream中的方法
  - 将上述的方法中的read改为相应的write即可

```java
//数据流进行java基本数据类型和String的持久化
@Test
public void test3()throws IOException{
    DataOutputStream dos=new DataOutputStream(new FileOutputStream("dataflow"));
    dos.writeBoolean(false);
    dos.writeUTF("张三丰");
    dos.writeInt(12345);
    dos.flush();    //将内存中的数据写入文件
    dos.close();
}
//数据流进行java基本数据类型和String的持久化文件的读取
@Test
public void test4()throws IOException{
    DataInputStream dis=new DataInputStream(new FileInputStream("dataflow"));
    boolean b=dis.readBoolean();
    String s=dis.readUTF();
    int i=dis.readInt();
    System.out.println(b);
    System.out.println(s);
    System.out.println(i);
}
```



## 对象流

### 对象的序列化

- 对象序列化机制允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。当其他程序获取了这种二进制流，就可以恢复成原来的Java对象
- 序列化的好处在于：可将任何实现了Serializable接口的对象转换成字节数据，使其在保存和传输时可以被还原。
- 序列化是RMI（Remote Method Invoke 远程方法调用）过程的参数和返回值都必须实现的机制，而RMI是JavaEE的基础。因此序列化机制是JavaEE平台的基础
- 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一。否则会抛出`NotSerializableException`异常。
  - `Serializable`
  - `Externalizable`
- **对象序列化需要满足两个要求**
  1. 实现接口Serializable（仅仅是个标识接口，无具体内容）
  2. 当前类提供一个全局常量：serialVersionUID
     - serialVersionUID用来表示类的不同版本之间的兼容性。简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容。
     - 如果类没有显示定义这个静态常量，它的值是Java运行时环境根据类的内部细节自动生成。**若类的实例变量做了修改，serialVersionUID可能发生变化**。所以，建议使用显式声明。
     - 简单来说，Java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本的一致性的。在进行反序列化时，JVM会把传过来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现版本不一致的异常（InvalidCastException）
  3. 必须保证内部所有属性可序列化（默认情况下，基本数据类型可序列化）

### 对象流

- ObjectInputStream, ObjectOutputStream
- 用于存储和读取**基本数据类型**数据或**对象**的处理流。可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。
- 序列化：用ObjectOutputStream类保存基本数据类型或对象的机制
- 反序列化：用ObjectInputStream类读取基本数据类型或对象的机制
- 不能序列化**static**和**transient**修饰的成员变量

```java
//java对象序列化
@Test
public void test5()throws IOException{
    ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream("serializationObjects"));
    oos.writeObject("你是什么念");
    oos.writeObject(new Date());
    oos.flush();
    oos.close();
}
//java对象反序列化
@Test
public void test6() throws IOException, ClassNotFoundException {
    ObjectInputStream ois=new ObjectInputStream(new FileInputStream("serializationObjects"));
    Object o1=ois.readObject();
    Object o2=ois.readObject();
    System.out.println(o1);
    System.out.println(o2);
}
```



## 随机存取文件流

RandomAccessFile类

- 声明在`java.io`包下，但直接继承于java.lang.Object类。（而非继承于流的四个基类）并且它实现了DataInput、DataOutput这两个接口，意味着既可以读也可以写（但是要造两个对象）
- `RandomAccessFile`类支持“随机访问”方式，程序可以直接跳到文件的任意地方来读、写文件
  - 支持只访问文件的部分内容
  - 可以向已存在的文件后追加内容
- `RandomAccessFile`对象包含一个记录指针，可以标示当前读写处的位置。该对象可以自由移动记录指针
  - `long getFilePointer()`：获取文件记录指针的当前位置
  - `void seek(long pos)`：将文件记录指针定位到pos位置
- 创建对象需要指定一个mode参数，该参数指定RandomAccessFIle对象的访问模式
  - `r`：以只读方式打开
  - `rw`：打开以便读取和写入
  - `rwd`：打开以便读取和写入；同步文件的内容的更新
  - `rws`：打开以便进行读取和写入；同步文件内容和元数据的更新
  - 注：如果模式为r，则不会创建文件，而是会读取一个已存在的文件，如果读取的文件不存在则会抛出异常。如果模式为rw读写。如果文件不存在则会去创建文件，如果存在则不会创建，而是覆盖。

## NIO.2中Path、Paths、Files类的使用

- Java NIO(Non-Blocking IO)是从java1.4开始引入的一套新的IO API，可以替代标准Java IO API。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的（IO是面向流的）、基于通道的IO操作。**NIO将以更高效的方式进行文件的读写操作**。
- Java API中提供了两套NIO，一套是针对标准输入输出的NIO，另一套是网络编程NIO
  - `java.nio.channels.Channel`：
    - `FileChannel`：处理本地文件
    - `SocketChannel`：TCP网络编程的客户端Channel
    - `ServerSocketChannel`：TCP网络编程的服务器端的Channel
    - `DatagramChannel`：UDP网络编程中发送端和接收端的Channel
- 随着JDK7的发布，Java对NIo进行了极大的扩展，增强了对文件处理和文件系统特性的支持，以至于我们称他们为NIO.2，因为NIO提供的一些功能，NIO已经成为文件处理中越来越重要的部分。

![image-20220103103453508](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103453508.png)

![image-20220103103515417](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103515417.png)

![image-20220103103526943](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103526943.png)

![image-20220103103543846](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103543846.png)

![image-20220103103603452](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103603452.png)

![image-20220103103611895](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220103103611895.png)

## 补充

一般实际工作中，会导入第三方jar包，如Apache的`commons-io`中的FileUitls来实现文件的操作。

# 8.网络编程

## 网络通信要素概述

如何实现网络中的主机相互通信

- 通信双方地址
  - IP
  - 端口号
- 一定的规则（网络通信协议）
  - OSI参考模型：模型过于理想化，未在因特网上进行广泛推广
  - TCP/IP参考模型（TCP/IP协议）：事实上的国际标准

## 通信要素1：IP和端口号

在Java中使用InetAddress类代表IP

![image-20220105095131010](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220105095131010.png)

- 端口号标识正在计算机上运行的进程
  - 不同的进程有不同的端口号
  - 被规定为一个16位的整数0~65535
  - 端口分类
    - 公认端口：0~1023。被预先定义的服务通信占用（如HTTP80，FTP21，Telnet23）
    - 注册端口：1024~49151.分配给用户进程或应用程序。（如Tomcat8080，MySQL3306，Oracle1521）
    - 动态/私有端口：49152~65535
  - 端口号和IP地址的组合得出一个网络套接字：Socket

## 通信要素2：网络协议

TCP和UDP

TCP协议：

- 使用前须建立TCP连接，形成传输通道
- 传输前，采用**三次握手**方式，点对点通信，可靠的
- TCP协议进行通信的两个应用进程：客户端、服务端
- 在连接中可进行大量数据的传输
- 传输完毕，需释放已建立的连接，效率低

![image-20220105100554138](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220105100554138.png)

UDP协议：

- 将数据、源、目的封装成数据包，无需建立连接
- 每个数据报的大小限制在64K内
- 发送不管对方是否准备好，接收方也收不到确认，故不可靠
- 可以广播发送
- 发送结束后无需释放资源，开销小，速度快



为什么TCP需要三次握手、四次挥手?

- B站掌知识：https://www.bilibili.com/video/BV1kV411j7hA?from=search&seid=5623693464798890863&spm_id_from=333.337.0.0

## TCP网络编程

注：用教研室电脑作为服务器端进行测试，由于ISP会动态给路由器分配IP地址，需要临时查看路由器IP；此外需要固定路由器给本机MAC分配的IP地址，然后固定NAT内外端口映射。

注：为何使用节点流、缓冲流，有时接收方的read无法结束，持续阻塞？而使用DataOutputStream和DataInputStream则没问题？

- 答：因为如果不关闭流，接收方read无法得知何时结束接收（貌似关闭流，接收方read就会返回-1，结束while）。因此如果发送方的发送文件代码下方没有别的代码阻塞，则函数结束，自动关闭流；如果下方有别的代码在阻塞，则不会自动关闭流，此时需要手动关闭流。
- 答：DataOutputStream的writeUTF和DataInputStream的readUTF，每次传输后会自动关闭流。

**客户端/服务器聊天案例**

```java
//服务器端：监听指定端口号，一旦有socket请求，则创建两个持续执行的线程，一个用来接收消息，一个用来发送消息
package MyChat;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Scanner;

public class ChatServer {
    public static void main(String[] args) {
        ServerSocket serverSocket = null; //服务端socket
        Socket clientSocket = null;   //客户端socket
        final int port = 12345;
        try {
            serverSocket = new ServerSocket(port);
            while (true) {    //监听端口，一旦收到新的scoket，则建立两个线程用于接收和发送消息
                clientSocket = serverSocket.accept();
                new Thread(new SendMessageToClient(clientSocket)).start();
                new Thread(new ReceiveClientMessage(clientSocket)).start();

            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(clientSocket!=null) {
                try {
                    clientSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(serverSocket!=null) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}

class SendMessageToClient implements Runnable {
    DataOutputStream dataOutputStream=null;
    private Scanner sc=new Scanner(System.in);

    public SendMessageToClient(Socket s) {
        try {
            dataOutputStream=new DataOutputStream(s.getOutputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        String message=null;
        try{
            while(true){
                message=sc.nextLine();
                dataOutputStream.writeUTF(message);
            }
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}

class ReceiveClientMessage implements Runnable {
    private DataInputStream dataInputStream = null;

    public ReceiveClientMessage(Socket s) {
        try {
            dataInputStream = new DataInputStream(s.getInputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        String message=null;
        try{
            while(true){    //一直运行，一旦接收到消息，就打印
                message=dataInputStream.readUTF();
                System.out.println("收到客户端消息："+message);
            }
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}

```

```java
//客户端：向指定ip和端口发起socket请求，创建两个线程，一个用于发送消息，一个用于接收消息
package MyChat;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;
import java.util.Scanner;

public class ChatClient {
    private static final String ip = "219.230.70.7";
    private static final int port=12345;

    public static void main(String[] args) {
        Socket serverSocket = null;
        try {
            serverSocket = new Socket(ip, port);

            new Thread(new MessageToServer(serverSocket)).start();
            new Thread(new MessageFromServer(serverSocket)).start();

        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}

class MessageToServer implements Runnable {
    private DataOutputStream dataOutputStream = null;
    private Scanner sc = new Scanner(System.in);

    public MessageToServer(Socket s) {
        try {
            dataOutputStream = new DataOutputStream(s.getOutputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        String message = null;
        while (true) {
            message=sc.nextLine();
            try {
                dataOutputStream.writeUTF(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

class MessageFromServer implements Runnable {
    private DataInputStream dataInputStream = null;

    public MessageFromServer(Socket s) {
        try {
            dataInputStream = new DataInputStream(s.getInputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        String info=null;
        while(true){
            try {
                info=dataInputStream.readUTF();
                System.out.println("收到服务端消息："+info);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```



**客户端/服务器文件传输案例**

注：

- 只有发送端关闭outputstream，接收端的read才能返回-1

- **失败经验**：我采用在发送数据的前4个字节添加数据长度，接收方根据长度来判断何时终止while循环，来解决read阻塞的问题（不阻塞，则接受完文件可以继续向发送方反馈信息）。但是失败了，文件没接受完就一直阻塞。

  - 我思考可能的原因是：底层tcp协议有个缓冲区，每次缓冲区接收满，或者收到FIN包，才会将缓冲区中内容复制到用户程序缓冲区。（这样做的原因是，数据包不是按顺序接收的，要保证这一段数据全部完整接收才会交给应用程序）。因此我的程序依然会阻塞在read处。

  - ```java
    while((len=bufferedInputStream.read(buf))!=-1){
        bufferedOutputStream.write(buf,0,len);
        fileLength-=len;
        System.out.println("本轮传输"+len+"B，剩余"+fileLength+"B");
        if(fileLength<=0)
            break;
    }
    
    //控制台消息
    文件长度为12850B
    本轮传输1024B，剩余11826B
    本轮传输1024B，剩余10802B
    本轮传输1024B，剩余9778B
    本轮传输1024B，剩余8754B
    本轮传输1024B，剩余7730B
    本轮传输1024B，剩余6706B
    本轮传输1024B，剩余5682B
    //卡在这里不动了
    ```

  - 解决办法：使用NIO。

```java
//服务器端：监听指定端口，一旦有socket请求，则创建一个线程发送文件
package day05;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

public class TransferFileServer {
    public static void main(String[] args) {
        //持续监测端口，一旦发现socket连接，则启动发送文件的线程
        int port=12345;
        ServerSocket serverSocket=null;
        Socket socket=null;
        try {
            serverSocket=new ServerSocket(port);
            while(true){
                socket= serverSocket.accept();  //阻塞线程
                new Thread(new SendFileThread(socket)).start();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(serverSocket!=null) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(socket!=null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}

class SendFileThread implements Runnable {
    private final Socket socket;
    private final String filename="副本曼达洛人.The.Mandalorian.S01E01.中英字幕.WEB.1080p-人人影视.V4.mp4";

    public SendFileThread(Socket s) {
        this.socket = s;
    }

    @Override
    public void run() {
        BufferedOutputStream output=null;
        BufferedInputStream input=null;
        try {
            output=new BufferedOutputStream(socket.getOutputStream());
            input=new BufferedInputStream(new FileInputStream(filename));
            byte[]buf=new byte[1024];
            int len;
            while((len=input.read(buf))!=-1){
                output.write(buf,0,len);
//                System.out.println("len="+len);
            }
            System.out.println("本次传输完成！");
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(input!=null) {
                try {
                    input.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(output!=null) {
                try {
                    output.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

```java
//客户端：向指定ip地址和端口发起socket请求，接收文件
package day05;

import java.io.*;
import java.net.Socket;

public class TransferFileClient {
    public static void main(String[] args) {
        String ip="219.230.74.51";
        int port=12345;
        int count=2;
        while(count>0){
            new Thread(new ReceiveFileThread(ip,port)).start();
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            count--;
        }
    }

}

/*接收文件*/
class ReceiveFileThread implements Runnable {
    private final String ip;
    private final int port;
    private static int count=0;
    public ReceiveFileThread(String ip, int port) {
        this.port = port;
        this.ip = ip;
    }

    @Override
    public void run() {
        Socket socket=null;
        BufferedInputStream bufferedInputStream=null;
        BufferedOutputStream bufferedOutputStream=null;
        try {
            socket = new Socket(ip, port);
            bufferedInputStream=new BufferedInputStream(socket.getInputStream());
            int tmp=count;
            bufferedOutputStream=new BufferedOutputStream(new FileOutputStream("File_"+tmp+".mp4"));
            count++;
            byte[]buf=new byte[1024];
            int len;
            while((len=bufferedInputStream.read(buf))!=-1){
                bufferedOutputStream.write(buf,0,len);
            }
            System.out.println("客户端接收文件"+tmp+"结束！");

        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(bufferedOutputStream!=null) {
                try {
                    bufferedOutputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(bufferedInputStream!=null) {
                try {
                    bufferedInputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(socket!=null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```



## UDP网络编程



## URL编程

# 9.反射

## 反射概述

反射：通过对象得知其类的信息

为什么需要反射:有些类无法在编译时确定,需要在运行时动态确定,比如Spring管理各种Bean,不可能预知.

反射相关的主要API

- `java.lang.Class`:代表一个类
- `java.lang.reflect.Method`:代表类的方法
- `java.lang.reflect.Field`:代表类的成员变量
- `java.lang.reflect.Constructor`:代表类的构造器

## 获取Class的实例的四种方式

方式1:调用**运行时类**的属性:`.class`

```java
Class<Student> clazz=Student.class;	//泛型可加可不加
```

方式2:通过**运行时类**的对象

```java
Student stu=new Student();
Class clazz=stu.getClass();
```



> 运行时类: .java文件被编译成.class文件之后,加载进内存中,生成Class类的对象的类(任何类都是Class类的对象).
>
> 加载到内存中的运行时类,会缓存一定的时间.在此时间之内,我们可以通过不同的方式来获取此运行时类.

方式3:调用Class的静态方法(用的最多)

```java
Class clazz=Class.forName("com.njust.java.Student"); 	//有异常要处理ClassNotFoundException
```

方式4:使用类的加载器:ClassLoader(了解)

```java
ClassLoader classLoader=Studetn.class.getClassLoader();
Class clazz=classLoader.loadClass("com.njust.java.Student");
```



> 注:四种方式获取的运行时类,地址值相同.
>
> 即:Class的实例就对应着一个运行时类

## 哪些些类型可以有Class对象

1. class 外部类,成员(成员内部类,静态内部类),局部内部类,匿名内部类
2. interface:接口
3. [] 数组 (只要数组元素类型和维度相同,就是同一个Class)
4. enum:枚举
5. annotation:注解@interface
6. primitive type:基本数据类型
7. void

## 类的加载过程

![image-20220604164815175](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604164815175.png)

![image-20220604164848679](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604164848679.png)

 ![image-20220604165142642](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604165142642.png)

![image-20220604165528449](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604165528449.png)

## 使用ClassLoader加载配置文件

![image-20220604170541798](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604170541798.png)



## 创建运行时类的对象

```java
Class<Student> clazz=Student.class;
Student stu=clazz.newInstance();
```

还可以通过获取构造器来创建实例

![image-20220604171910160](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220604171910160.png)

# 10.Java8新特性

# 11.Java9 & 10 & 11 新特性 