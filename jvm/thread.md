# Thread

thread.join()

在父线程创建并调用子线程的时候，如果调用join()方法，那么就会让父线程等待子线程结束然后在继续运行

thread.run()

**先说用thread.run()来启动线程，会顺序执行，run启动一条线程之后将这条线程走完之后，才执行下面的run，想想火车站排票？要是用run就等于一条长蛇队了。**

thread.start()

**再说用thread.start来启动线程，这个才是真正实现了多线程运行，thread.start()不用等待被覆盖的run执行完也可以直接继续执行下面的代码，线程处于就绪状态，交替执行直到结束**

代码如下：

```java
public class Station extends  Thread {

    static int ticket = 10;

    static Object lock = "locked";

    public Station (String name) {
        super(name);
    }
    @Override
    public void run() {
        super.run();
        while(ticket > 0) {
            synchronized (lock) {
                if(ticket >0) {
                    System.out.println(getName() + " 卖出去第 " + ticket + " 张票了");
                    --ticket;
                } else {
                    System.out.println("票买完了!");
                }
            }
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}

public class App {

    public  App() {
        Station s1 = new Station("1");
        Station s2 = new Station("2");
        Station s3 = new Station("3");

        s1.run();
        s2.run();
        System.out.println(111);
        s3.run();
        System.out.println(222);
    }

    public static void main(String[] args) {
        new App();
    }
}

```

thread.run() 的运行结果

![image-20200530005220654](upload/image-20200530005220654.png)

将上面代码的thread.run() 修改为 thread.start(),结果如下

![image-20200530005031418](upload/image-20200530005031418.png)



## 线程的6中状态

New ：初始状态，线程被构建，但是还没有调用start方法

RUNNABLE： 运行状态

BLOCKED： 阻塞状态，表示线程没有获得锁资源

WAITTING: 等待状态，thread.wait() ,进入该状态的线程需要其他线程唤醒。lock.notifyAll()

notifyAll()  和 notify() 的区别

- 调用 notify() 方法只会随机选取一个处于等待池中的线程进入锁池中去竞争获取锁的机会
- 调用 notifyAll() 方法会让所有处于等待池中的线程进入锁池中去竞争获取锁的机会

TIME_WAITTING: 超时等待状态，lock.wait(long mills)，可以在指定时间返回，不需要被其他线程唤醒

TERMINATED: 终止，表示当前线程运行完毕

线程创建之后它将处于 **NEW（新建）** 状态，调用 `start()` 方法后开始运行，线程这时候处于 **READY（可运行）** 状态。可运行状态的线程获得了 cpu 时间片（timeslice）后就处于 **RUNNING（运行）** 状态。

> 操作系统隐藏 Java 虚拟机（JVM）中的 READY 和 RUNNING 状态，它只能看到 RUNNABLE 状态（图源：[HowToDoInJava](https://howtodoinjava.com/)：[Java Thread Life Cycle and Thread States](https://howtodoinjava.com/java/multi-threading/java-thread-life-cycle-and-thread-states/)），所以 Java 系统一般将这两个状态统称为 **RUNNABLE（运行中）** 状态 。

![RUNNABLE-VS-RUNNING](upload/RUNNABLE-VS-RUNNING.png)

当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）**状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）`方法或 `wait（long millis）`方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。



守护线程

当一个jvm中不存在 Daemon 线程时，jvm就退出了，通过 Thread.setDaemon(true)设置某一个线程为守护线程

线程中断

Thread.isINterrupted() 判断是否中断 