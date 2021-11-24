[W3Cschool java 帮助文档](https://www.w3cschool.cn/java/dict)

[阿里云`MaTools Java8`](https://www.matools.com/api/java8)

# java基础API

# java集合

![java.util](C:/Users/lenovo/Desktop/java.util.svg)

![image-2020031351363](image/image-2020031351363.jpg)



# JVM

[JAVA 常见内存泄露例子及详解](https://blog.csdn.net/m0_38110132/article/details/81986334)

# 注解和反射

# 多线程

## 线程简介

- 单线程`Process`
  - <img src="image/image-20211117173931758.png" alt="image-20211117173931758" style="zoom: 50%;" />
- 多线程`Thread` 
  - <img src="image/image-20211117173945517.png" alt="image-20211117173945517" style="zoom:50%;" />

- 线程就是独立的执行路径。
- 在程序运行时，即使没有自己创建线程，后台也会有多个线程，如主线程，gc线程。
- main()称之为主线程，为系统的入口，用于执行整个程序。
- 在一个进程中，如果开辟了多个线程，线程的运行由调度器安排调度，调度器是与操作系统紧密相关的，先后顺序是不能认为的干预的。
- 对同一份资源操作时，会存在资源抢夺的问题，需要加入并发控制。
- 线程会带来额外的开销，如cpu调度时间，并发控制开销。
- 每个线程在自己的工作内存交互，内存控制不当会造成数据不一致。

## ==线程实现 for Java==

### Thread class 继承类

- 自定义线程类继承Thread类
- 重写run()方法，编写线程执行体
- 创建线程对象，调用start()方法启动线程

```java
// 创建线程方式一:继承Thread类，重写run()方法，调用start开启线程
public class MyThread extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("我是线程MyThread"+i);
        }
    }

    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();
        for (int i = 0; i < 10; i++) {
            System.out.println("我是主线程"+i);
        }
    }
}
```

### Runnable interface 实现接口

- 定义MyRunnable类实现Runnable接口
- 实现run()方法，编写线程执行体
- 创建线程对象,调用start()方法启动线程

```java
// 方式二：实现runnable接口,重写run方法，执行线程需要丢入runnable接口实现类.调用start方法
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("我是线程MyRunnable"+i);
        }
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        // 通过线程对象来代理自己的线程
        Thread thread = new Thread(myRunnable);
        thread.start();

        for (int i = 0; i < 10; i++) {
            System.out.println("我是主线程"+i);
        }
    }
}
```

### ==Callable== interface 实现接口

1. 实现Callable接口，需要返回值类型

2. 重写call方法，需要抛出异常

3. 创建目标对象

4. 创建执行服务:ExecutorService ser = Executors.newFixedThreadPool(1);

5. 提交执行:Future<Boolean> result1 = ser.submit(t1);

6. 获取结果:boolean r1 = result1.get()

7. 关闭服务:ser.shutdownNow();

```java
public class MyCallable implements Callable {
    @Override
    public Object call() throws Exception {
        for (int i = 0; i < 10; i++) {
            System.out.println("我是线程MyRunnable"+i);
        }
        return (int)(Math.random()*100);
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable myCallable = new MyCallable();
        // 创建执行服务
        ExecutorService service = Executors.newFixedThreadPool(1);
        // 提交执行
        Future<Integer> future = service.submit(myCallable);
        // 获取结果
        int result = future1.get();
        // 关闭服务
        service.shutdown();
        System.out.println(result);

        for (int i = 0; i < 10; i++) {
            System.out.println("我是主线程"+i);
        }

    }
}
```

### Lamda表达式

```java
public class TestLambda {
    static class LikeInner implements ILike{
        @Override
        public void lambda() {
            System.out.println("I like Lambda 内部类");
        }
    }

    public static void main(String[] args) {
        // 外部类
        ILike like1 = new LikeOUT();
        like1.lambda();
        // 内部类
        ILike like2 = new LikeInner();
        like2.lambda();
        // 局部内部类
        class LikeLocalInner implements ILike{
            @Override
            public void lambda() {
                System.out.println("I like Lambda 局部内部类");
            }
        }
        ILike like3 = new LikeLocalInner();
        like3.lambda();
        // 匿名内部类
        ILike like4 = new ILike() {
            @Override
            public void lambda() {
                System.out.println("I like Lambda 匿名内部类");
            }
        };
        like4.lambda();
        // lambda简化(只能简化单个方法的接口)
        ILike like5 = ()->{
            System.out.println("I like Lambda lambda简化");
        };
        like5.lambda();
    }
}

interface ILike{
    void lambda();
}

class LikeOUT implements ILike{
    @Override
    public void lambda() {
        System.out.println("I like Lambda 外部类");
    }
}
```

带参数的lambda表达式。

```java
field = parameter-> {
    System.out.println("实现"+parameter);
};
field.method(parameter);
```

## 线程状态

![image-20211117204357034](image/image-20211117204357034.png)

![image-20211117204518250](image/image-20211117204518250.png)

线程的方法


| 方法                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| setPriority(int newPriority)   | 更改线程的优先级                                             |
| static void sleep(long millis) | 在指定的毫秒数内让当前正在执行的线程休眠                     |
| void join()                    | 等待该线程终止                                               |
| static void yield()            | 暂停当前正在执行的线程对象，从运行状态回到就绪状态，重新和就绪线程抢夺时间片 |
| void interrupt()               | 中断线程，别用这个方式                                       |
| boolean isAlive()              | 测试线程是否处于活动状态                                     |

### 线程停止——stop

- 不推荐使用JDK提供的stop()、destroy()方法【已废弃】
- 推荐线程自己停止下来
- 建议使用一个标志位进行终止变量当flag=false，则终止线程运行。

```java
public class TestThread implements Runnable {
    // 1.设置标识位
    private boolean flag = true;
    @Override
    public void run() {
        int i = 0;
        while (flag){
            System.out.println("run"+Thread.currentThread().getName()+i++);
        }
    }
    // 2.设置一个公开的方法停止线程，转换标识位
    public void stop() {
        this.flag = false;
    }
}
```

### 线程休眠——sleep

- sleep(时间)指定当前线程阻塞的毫秒数
- sleep存在异常InterruptedException
- sleep时间达到后线程进入就绪状态
- sleep可以模拟网络延时，倒计时等
- 每一个对象都有一个锁,sleep不会释放锁

[模拟延时](https://blog.csdn.net/ZREO_0/article/details/108626675)、[模拟倒计时](https://blog.csdn.net/qq_43619461/article/details/120920100)

### 线程礼让——yield

- 礼让线程，让当前正在执行的线程暂停，但不阻塞
- 将线程从运行状态转为就绪状态
- 让cpu重新调度，礼让不一定成功!看CPU心情

[线程礼让](https://blog.csdn.net/hello_word2/article/details/52885398)

### 线程合并——join

- Join合并线程，待此线程执行完成后，再执行其他线程，其他线程阻塞

[JAVA多线程中join()方法的详细分析](https://blog.csdn.net/u013425438/article/details/80205693)

### 线程状态观测——state 

```java
Thread.State state = thread.getState();
public static enum Thread.State
extends Enum<Thread.State>
    getState
```

线程状态。 线程可以处于以下状态之一：

- [`NEW`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#NEW)
  尚未启动的线程处于此状态。
- [`RUNNABLE`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#RUNNABLE)
  在Java虚拟机中执行的线程处于此状态。
- [`BLOCKED`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#BLOCKED)
  被阻塞等待监视器锁定的线程处于此状态。
- [`WAITING`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#WAITING)
  正在等待另一个线程执行特定动作的线程处于此状态。
- [`TIMED_WAITING`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#TIMED_WAITING)
  正在等待另一个线程执行动作达到指定等待时间的线程处于此状态。
- [`TERMINATED`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/lang/Thread.State.html#TERMINATED)
  已退出的线程处于此状态。

一个线程可以在给定时间点处于一个状态。 这些状态是不反映任何操作系统线程状态的虚拟机状态。

### [线程优先级](https://blog.csdn.net/qq_35400008/article/details/80219947)

## 守护线程

- 线程分为用户线程和守护线程
- 虚拟机必须确保用户线程执行完毕
- 虚拟机不用等待守护线程执行完毕
- 如,后台记录操作日志,监控内存,垃圾回收等待.

`thread.setDaemon(true);`

```java
public class Main {
    public static void main(string[] args) {
        God god = new God();
        You you = new You();
        Thread thread = new Thread(god);
        thread.setDaemon(true);//默认是false表示是用户线程，正常的线程都是用户线程...
        thread.start();//上帝引护线程启动
        new Thread(you).start(); //你用户线程启动...
    }
}
//上帝
class God implements Runnable{
    @Override
    public void run() {
        while (true){
        system.out.println("上帝保佑着你");
        }
    }
}
//你
class You implements Runnable{
    @Override
    public void run() {
        for (int i =0; i < 36500; i++) {
            system.out.println("你一生都开心的活着");
        }
    	system.out.println("====goodbye! world!======");
    }
}
```

## ==线程同步==

处理多线程问题时，多个线程访问同一个对象﹐并且某些线程还想修改这个对象﹒这时候我们就需要线程同步.线程同步其实就是一种等待机制,多个需要同时访问此对象的线程进入这个**对象的等待池**形成队列,等待前面线程使用完毕，下一个线程再使用

> [什么是线程不安全？](https://zhuanlan.zhihu.com/p/108880731?from_voters_page=true)

### 队列和锁

### 锁机制

#### synchronized

```java
public class lockThread {
    public static void main(String[] args) {
        AtomicReference<Integer> ticketNums = new AtomicReference<>(10);
        lockThread ticket = new lockThread();
        for (int i = 0; i < 3; i++) {
            new Thread(() -> {
                while (true) {
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (ticketNums){
                        if (ticketNums.get() > 0)
                            System.out.println(Thread.currentThread().getName() +
                                    " get ticket for" +
                                    ticketNums.getAndSet(ticketNums.get() - 1)
                            );
                    }
                }
            }).start();
        }
    }
}
```

#### Lock ReentrantLock可重入锁

```java
public class ReentrantLockThread {
    public static void main(String[] args) {
        TicketLockThread ticketLockThread = new TicketLockThread();
        new Thread(ticketLockThread,"1").start();
        new Thread(ticketLockThread,"2").start();
        new Thread(ticketLockThread,"3").start();
    }
}

class TicketLockThread implements Runnable{
    private final ReentrantLock lock = new ReentrantLock();
    private int ticketNums = 10;
    @Override
    public void run() {
        while (true){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            lock.lock();
            if(ticketNums>0){
                System.out.println(Thread.currentThread().getName()+" get "+ticketNums--);
                lock.unlock();
            }else {
                lock.unlock();
                break;
            }

        }
    }
}
```

#### 死锁

- 互斥条件:一个资源每次只能被一个进程使用。
- 请求与保持条件:一个进程因请求资源而阻塞时，对已获得的资源保持不放。
- 不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺。
- 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。

```java
public class DeadLock {
    public static void main(String[] args) {
        Makeup makeup1 = new Makeup(0,"1");
        Makeup makeup2 = new Makeup(1,"2");
        new Thread(makeup1).start();
        new Thread(makeup2).start();
    }
}
class Lipstick {}
class Mirror {}
class Makeup implements Runnable {
    static Lipstick lipstick = new Lipstick();
    static Mirror mirror = new Mirror();
    int choice;
    String name;
    public Makeup(int choice, String name) {
        this.choice = choice;
        this.name = name;
    }
    @Override
    public void run() {
        makeup();
    }
    private void makeup() {
        if (choice == 0) {
            synchronized (lipstick) {
                System.out.println("获得口红的锁");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (mirror) {
                    System.out.println("获得镜子的锁");
                }
            }
        } else {
            synchronized (mirror) {
                System.out.println("获得镜子的锁");
                synchronized (lipstick) {
                    System.out.println("获得口红的锁");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

## 线程通信问题

### 管程法——生产者和消费者

java线程通信方法

| 方法               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| wait()             | 表示线程一直等待，直到其他线程通知，与sleep不官，会释放锁，**阻塞** |
| wait(long timeout) | 指定等待的毫秒数                                             |
| notify()           | **唤醒**一个处于等待状态的线程                               |
| notifyAll()        | **唤醒**同一个对象上所有调用wait()方法的线程﹐优先级别高的线程优先调度 |

并发协作模型“生产者/消费者模式”==>管程法

- 生产者：负责生产数据的模块(可能是方法，对象，线程，进程)
- 消费者：负责处理数据的模块(可能是方法，对象，线程，进程)
- 缓冲区：消费者不能直接使用生产者的数据，他们之间有个“缓冲区“

生产者将生产好的数据放入缓冲区,消费者从缓冲区拿出数据

```java
// 管程法——生产者和消费者
public class TestPC {
    public static void main(String[] args) {
        SynContainer container = new SynContainer();

        new Thread(new Producer(container)).start();
        new Thread(new Consumer(container)).start();

    }
}
// 生产者
class Producer implements Runnable {
    SynContainer container;

    public Producer(SynContainer container) {
        this.container = container;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            container.push(new Chicken(i+1));
        }
    }
}
// 消费者
class Consumer implements Runnable {
    SynContainer container;

    public Consumer(SynContainer container) {
        this.container = container;
    }
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            container.pop();
        }
    }
}
// 产品
class Chicken {
    private int id;
    public Chicken(int id) {
        this.id = id;
    }
    public int getId() {
        return id;
    }
}
// 缓冲区
class SynContainer {
    // 容器
    Chicken[] chickens = new Chicken[10];
    // 容器计数器
    int count = 0;

    // 生产者放入产品
    public synchronized void push(Chicken chicken) {
        // 如果容器满了，需要等待消费者消费
        if (count>0) {
            // 通知消费者消费，生产者等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        // 生产需要的时间
        // try {
        //     this.wait(300);
        // } catch (InterruptedException e) {
        //     e.printStackTrace();
        // }
        // 容器没有满，我们就丢入产品
        System.out.println("生产了第"+chicken.getId()+"鸡");
        chickens[count] = chicken;
        count++;
        // 可以通知消费者消费
        this.notify();
    }

    // 消费者消费产品
    public synchronized Chicken pop() {
        // 如果容器空了，需要等待消费者消费
        if (count == 0) {
            // 通知生产者生产，消费者等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        // 消费需要的时间
        // try {
        //     this.wait(300);
        // } catch (InterruptedException e) {
        //     e.printStackTrace();
        // }
        // 容器有东西，我们就消费产品
        count--;
        System.out.println("消费了第"+chickens[count].getId()+"鸡");
        // 可以通知生产者生成
        this.notify();
        return chickens[count];
    }
}
```

### 信号灯法

```java
// 信号灯法——标志位
public class TestPC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Thread(new Performer(tv)).start();
        new Thread(new Viewer(tv)).start();
    }
}
// 生产者
class Performer implements Runnable {
    TV tv;
    public Performer(TV tv) {
        this.tv = tv;
    }
    @Override
    public void run() {
        for (int i = 1; i <= 20; i++) {
            if(i%2==0){
                tv.play("电影"+i);
            }else{
                tv.play("广告"+i);
            }
        }
    }
}
// 消费者
class Viewer implements Runnable {
    TV tv;
    public Viewer(TV tv) {
        this.tv = tv;
    }
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            tv.watch();
        }
    }
}
// 产品
class TV {
    private int id;
    private String voice;
    boolean flag = true;
    public synchronized void play(String voice){
        if(!flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("演员表演了："+voice+"节目");
        // 通知观众观看
        this.voice = voice;
        this.flag = !this.flag;
        this.notify();
    }
    public synchronized void watch(){
        if(flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观众观看了："+voice+"节目");
        // 通知演员表演
        this.flag = !this.flag;
        this.notify();
    }
}
```

## 线程池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class TestPool {
    public static void main(String[] args) {
        // 1.创建服务，关键线程池
        // newFixedThreadPool 参数为线程池大小
        ExecutorService service = Executors.newFixedThreadPool(10);
        // 执行
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        // 2.关闭连接
        service.shutdown();
    }
}

class MyThread implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + i);
        }
    }
}
```

# JUC并发多线程

## 1、JUC是什么

- [java.util.concurrent](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/package-summary.html)
- [java.util.concurrent.atomic](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/atomic/package-summary.html)
- [java.util.concurrent.locks](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/package-summary.html)

|          |                                  |
| -------- | -------------------------------- |
| Thread   | 普通的线程对象                   |
| Runable  | 没有返回值，效率相比于Callable低 |
| Callable | 重点！                           |

## 2、进程和线程

> 一个进程可能会有多个线程

Java默认线程：main、GC守护线程

Java不能开启线程，调用的本地方法(底层的C++)

```java
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
private native void start0();
```

> 并发、并行

并发(多线程操作同一个资源)

- CPU一核，模拟出来多条线程，天下武功，唯快不破，快速交替

并行(多个人一起行走)

- CPU多核，多个线程可以同时执行

```
// 打印逻辑处理器数量
System.out.println(Runtime.getRuntime().availableProcessors());
```

> [线程状态](#线程状态观测——state )

```java
public enum State {
    // 新建
    NEW,
	// 运行
    RUNNABLE,
	// 阻塞
    BLOCKED,
	// 等待
    WAITING,
	// 超市等待
    TIMED_WAITING,
	// 终止
    TERMINATED;
}
```

> wait/sleep区别

- **来自不同的类**
  - wait是java.lang.Object的

  - sleep是java.lang.Thread的
- **关于锁的释放**
  - wait会释放锁

  - sleep不会释放锁
- **使用的范围是不同的**
  - wait必须在同步代码块中
  - sleep可以在任何地方睡

## 3、==Lock 锁==

### 3.1、8种锁的现象

1. 一个`对象`，两个线程先执行`加锁方法`还是`加锁方法2`
   - 相当于一把锁，谁先获得锁谁先执行
2. `加锁方法1`延迟4秒，一个`对象`，两个线程先执行`加锁方法1`还是`加锁方法2`
   - 相当于一把锁，谁先获得锁谁先执行
3. 一个`对象`，两个线程先执行`加锁方法`还是`普通方法`
   - 相当于一把锁，但有个线程不用锁，主线程先执行到谁，谁先执行
4. 两个`对象`，两个线程分别执行两个`对象`的两个不同`加锁方法`
   - 相当于两把锁，主线程先执行到谁，谁先执行
5. 一个`对象`，两个线程先执行`加锁静态方法1`还是`加锁静态方法2`
   - 相当于一把锁，锁的是唯一的Class，主线程先执行到谁，谁先执行
6. 两个`对象`，两个线程先执行`加锁静态方法`1还是`加锁静态方法2`
   - 相当于一把锁，锁的是唯一的Class，主线程先执行到谁，谁先执行
7. 一个`对象`，两个线程先执行`加锁方法`还是`加锁静态方法`
   - 相当于两把锁，主线程先执行到谁，谁先执行
8. 两个`对象`，两个线程分别执行两个`对象`的`加锁方法`和`加锁静态方法`
   - 相当于两把锁，主线程先执行到谁，谁先执行

> [对象锁和类锁的区别](https://www.cnblogs.com/fengzheng/p/12066239.html)

### 3.2、java中的锁

> 传统Synchronized

[synchronized](#synchronized)

[Java6及以上版本对synchronized的优化](https://blog.csdn.net/weixin_38405770/article/details/115027132)

> Lock 接口

随着这种增加的灵活性，额外的责任。 没有块结构化锁定会删除使用`synchronized`方法和语句发生的锁的自动释放。 在大多数情况下，应使用以下惯用语：

```java
Lock l = ...; 
l.lock(); 
try { // access the resource protected by this lock 
} finally { 
    l.unlock(); 
} 
```

当在不同范围内发生锁定和解锁时，必须注意确保在锁定时执行的所有代码由try-finally或try-catch保护，以确保在必要时释放锁定。

> ReentantLocK

```java
public ReentrantLock() {
    sync = new NonfairSync();
}
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

> Synchronized 和 ReentantLocK的区别

- Synchronized 是内置的java关键字，Lock 是一个java类
- Synchronized 无法判断获取锁的状态，Lock 可以判断是否获取到了锁
- Synchronized 可以自动释放，Lock 必须要手动释放，如何不释放锁，会导致**死锁**
- Synchronized 线程1(获得锁，阻塞)、线程2(等待，一直等)，Lock 不一定会等待下去
- Synchronized 可重入锁，不可以中断，Lock 可重入锁，可以自己设置公平非公平锁
- Synchronized 适合锁少量的代码同步问题，Lock 适合锁大量的同步代码
- Synchronized ，Lock 

[可重入锁详解（什么是可重入）](https://blog.csdn.net/w8y56f/article/details/89554060)

> ReadLock

> WriteLock

## 4、生产者和消费者问题

[java最早的生产者和消费者问题解决方案](#管程法——生产者和消费者)

- wait

|      锁      | 等待 |  通知  |
| :----------: | :--: | :----: |
| Synchronized | wait | notify |

**线程也可以唤醒，而不会被通知，中断或超时**，即所谓的***虚假唤醒*** 。 虽然这在实践中很少会发生，但应用程序必须通过测试应该使线程被唤醒的条件来防范，并且如果条件不满足则继续等待。 换句话说，**等待应该总是出现在循环中**，就像这样：

```java
synchronized (obj) {
    while (<condition does not hold>)
        obj.wait(timeout);
    ... // Perform action appropriate to condition
}
```

- JUC

|  锁  | 等待  |  通知  |
| :--: | :---: | :----: |
| Lock | await | signal |

[Interface Condition](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/Condition.html)

例如，假设我们有一个有限的缓冲区，它支持`put`和`take`方法。 如果在一个空的缓冲区尝试一个`take` ，则线程将阻塞直到一个项目可用; 如果`put`试图在一个完整的缓冲区，那么线程将阻塞，直到空间变得可用。 我们希望在单独的等待集中等待`put`线程和`take`线程，以便我们可以在缓冲区中的项目或空间可用的时候使用仅通知单个线程的优化。 这可以使用两个[`Condition`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/locks/Condition.html)实例来实现。

```java
class BoundedBuffer {
    final Lock lock = new ReentrantLock();
    final Condition notFull  = lock.newCondition(); 
    final Condition notEmpty = lock.newCondition(); 

    final Object[] items = new Object[100];
    int putptr, takeptr, count;

    public void put(Object x) throws InterruptedException {
        lock.lock(); try {
            while (count == items.length)
                notFull.await();
            items[putptr] = x;
            if (++putptr == items.length) putptr = 0;
            ++count;
            notEmpty.signal();
        } finally { lock.unlock(); }
    }

    public Object take() throws InterruptedException {
        lock.lock(); try {
            while (count == 0)
                notEmpty.await();
            Object x = items[takeptr];
            if (++takeptr == items.length) takeptr = 0;
            --count;
            notFull.signal();
            return x;
        } finally { lock.unlock(); }
    }
}
```

> Condition 与 传统 Object 中的等待与通知的区别

Condition可以指定唤醒的线程。

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
public class Test2 {
    public static void main(String[] args) {
        Ticket1 ticket = new Ticket1();
        new Thread(() -> {
            for (int i = 0; i < 20; i++) ticket.sale0();
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 20; i++) ticket.sale1();
        }, "B").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) ticket.add0();
        }, "C").start();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) ticket.add1();
        }, "D").start();
    }
}
class Ticket1 {
    private int num = 0;
    private int id = 0;
    Lock lock = new ReentrantLock();
    Condition buyCondition0 = lock.newCondition();
    Condition buyCondition1 = lock.newCondition();
    Condition addCondition = lock.newCondition();
    public void sale0() {
        // 加锁
        lock.lock();
        try {
            // 等待
            while (num == 0) {
                buyCondition0.await();
            }
            System.out.println(Thread.currentThread().getName() + "卖出了第" + (++id) + "票，剩余:" + (--num));
            addCondition.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    public void sale1() {
        // 加锁
        lock.lock();
        try {
            // 等待
            while (num == 0) {
                buyCondition1.await();
            }
            System.out.println(Thread.currentThread().getName() + "卖出了第" + (++id) + "票，剩余:" + (--num));
            addCondition.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    public void add0() {
        // 加锁
        lock.lock();
        try {
            // 等待
            while (num > 0) {
                addCondition.await();
            }
            System.out.println(Thread.currentThread().getName() + "添加了一张票，剩余:" + ++num);
            buyCondition0.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    public void add1() {
        // 加锁
        lock.lock();
        try {
            // 等待
            while (num > 0) {
                addCondition.await();
            }
            System.out.println(Thread.currentThread().getName() + "添加了一张票，剩余:" + ++num);
            buyCondition1.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

## 5、集合类不安全

java.util.[ConcurrentModificationException](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/ConcurrentModificationException.html)异常

解决方案：

- 使用官方提供的线程安全的类

- `List<String> list = Collections.synchronizedList(new ArrayList<>());`

  ![image-20211121204031112](image/image-20211121204031112.png)

- CopyOnWrite 写入时复制，COW 计算机程序设计领域的一种优化策略：

  多个线程调用的时候，`list`，读取的时候，固定的，写入（覆盖）

  在写入的时候避免覆盖，造成数据问题

- 扩展：读写分离

参考：[java集合](#java集合)

|  接口  | 并发下不安全 | 并发下安全 |      JUC包下集合       |
| :----: | :----------: | :--------: | :--------------------: |
| `List` | `ArrayList`  |  `vector`  | `CopyOnWriteArrayList` |
| `Set`  |  `HashSet`   |            | `CopyOnWriteArratySet` |
| `Map`  |  `HashMap`   |            |  `CopyOnWriteHashMap`  |

- [ConcurrentHashMap](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/ConcurrentHashMap.html)

## 6、[Callable](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/Callable.html)

返回结果并可能引发异常的任务。 实现者定义一个没有参数的单一方法，称为`call` 。

`Callable`接口类似于`Runnable`，因为它们都是为其实例可能由另一个线程执行的类设计的。 然而，一个 `Runnable`不返回结果，也不能抛出被检查的异常。

该`Executors`类包含的实用方法，从其他普通形式转换为`Callable`类。

与`Runable`相比

- 有返回值
- 可以抛出异常
- 调用方法不同，run() call()

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> futureTask = new FutureTask<>(new MyThread());
        new Thread(futureTask,"A").start();
        System.out.println(futureTask.get());
    }
}

class MyThread implements Callable<String>{
    @Override
    public String call() throws Exception {
        System.out.println(Thread.currentThread().getName());
        return "StringJAVA";
    }
}
```

**FutureTask对象只能执行一次**：JVM第二次再调用FutrueTask对象所持有的线程，此时FutrueTask的state此时已非NEW状态（各个状态，这边不做详细解释），则此时会直接结束对应线程，就会导致任务也不执行，但是在第一次调用时返回结果保存

## 7、辅助类

### 7.1、[CountDownLatch](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/CountDownLatch.html)

减法计数器控制的线程，开始直接等待，当有n个线程执行完之后，本线程停止等待。

```java
import java.util.concurrent.CountDownLatch;

public static void main(String[] args) throws InterruptedException {
    CountDownLatch countDownLatch = new CountDownLatch(6);
    for (int i = 0; i < 6; i++) {
        int finalI = i;
        new Thread(()->{
            System.out.println(Thread.currentThread().getName());
            countDownLatch.countDown();
        },String.valueOf(i)).start();
    }
    countDownLatch.await();// 等待计数器归零，然后再向下执行
    System.out.println("我可以动了");
}
```

### 7.2、[CyclicBarrier](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/CyclicBarrier.html)

加法计数器(本质还是减法)控制的线程，开始直接等待，当有n个线程再等待时，停止等待，并通知他们不用等待

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierTest {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("我可以动了");
        });
        for (int i = 0; i < 7; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName());
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

### 7.3、[Semaphore](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/Semaphore.html)

限制线程个数，限制了同时能运行的线程个数n

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreTest {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"in");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"out");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

## 8、读写锁

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;
/*
* ReadWriteLock
* 读-读   可以共存
* 读-写   不能共存
* 写-写   不能共存
* 独占锁（写锁）
* 共享锁（读锁）
* */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCacheLock myCache = new MyCacheLock();
        for (int i = 1; i <= 5; i++) {
            final int finalI = i;
            new Thread(() -> {
                myCache.put(String.valueOf(finalI), String.valueOf(finalI));
            }, String.valueOf(i)).start();
        }
        for (int i = 1; i <= 5; i++) {
            final int finalI = i;
            new Thread(() -> {
                myCache.get(String.valueOf(finalI));
            }, String.valueOf(i)).start();
        }
    }
}
// 加锁
class MyCacheLock {
    private volatile Map<String, Object> map = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock();
    // 存，写，只希望同时只有一个线程写
    public void put(String key, Object value) {
        try {
            lock.writeLock().lock();
            System.out.println(Thread.currentThread().getName() + "写入" + key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "写入ok");
        } catch (Exception e) {

        } finally {
            lock.writeLock().unlock();
        }
    }
    // 取，读，所有人都可以读
    public void get(String key) {
        try {
            lock.readLock().lock();
            System.out.println(Thread.currentThread().getName() + "读取" + key);
            map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取ok");
        } catch (Exception e) {

        } finally {
            lock.readLock().unlock();
        }
    }
}
// 未加锁
class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();
    // 存，写
    public void put(String key, Object value) {
        System.out.println(Thread.currentThread().getName() + "写入" + key);
        map.put(key, value);
        System.out.println(Thread.currentThread().getName() + "写入ok");

    }
    // 取，读
    public void get(String key) {
        System.out.println(Thread.currentThread().getName() + "读取" + key);
        map.get(key);
        System.out.println(Thread.currentThread().getName() + "读取ok");
    }
}
```

## 9、阻塞队列

- [*BlockingDeque*](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/BlockingDeque.html)

- [*BlockingQueue*](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/concurrent/BlockingQueue.html)



