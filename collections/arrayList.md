# Queue

队列是一种特殊的线性表，它只允许在表的前端进行删除操作，而在表的后端进行插入操作。

LinkedList类实现了Queue接口，因此我们可以把LinkedList当成Queue来用。

```java
package com.company.list;

import java.util.LinkedList;
import java.util.Queue;

public class TestQueue {
    public static void main(String[] args) {
        //add()和remove()方法在失败的时候会抛出异常(不推荐)
        Queue<String> queue = new LinkedList<String>();
        //添加元素
        queue.offer("a");
        queue.offer("b");
        queue.offer("c");
        queue.offer("d");
        queue.offer("e");
        for(String q : queue){
            System.out.println(q);
        }
        System.out.println("===");
        System.out.println("poll="+queue.poll()); //返回第一个元素，并在队列中删除
        for(String q : queue){
            System.out.println(q);
        }
        System.out.println("===");
        System.out.println("element="+queue.element()); //返回第一个元素
        for(String q : queue){
            System.out.println(q);
        }
        System.out.println("===");
        System.out.println("peek="+queue.peek()); //返回第一个元素
        for(String q : queue){
            System.out.println(q);
        }
    }
}
```

# Deque

```java
package com.company.list;

import java.util.Deque;
import java.util.LinkedList;

public class TestDeque {

    public static void main(String[] args) {
        Deque<String> deque = new LinkedList<>();
        deque.offerLast("end"); // "end"
        deque.offerFirst("C"); // "C", "end"
        deque.offerFirst("B"); // "B", "C", "end"
        deque.offerFirst("A"); // "A", "B", "C", "end"
        System.out.println(deque.pollLast()); //获取并且删除队尾元素，失败返回null
        System.out.println(deque.pollFirst()); // 获取并且删除队首元素，失败返回null
        System.out.println(deque.pollFirst());
        System.out.println(deque.pollFirst());
        System.out.println(deque.pollFirst());
    }

}
```

# Stack：线程安全的java栈

![](http://q8sats5bw.bkt.clouddn.com/image-20200416135025713.png)

java Stack 类，是一个标准的后进先出的栈，继承自 vector，底层使用过数组实现的，是线程安全的

* java中stack的使用方法,堆栈是一种"后进先出"(LIFO)的数据结构,只能在一端进行插入(称为"压栈")或删除(称为"出栈")数据的操作.
  * Java中,使用java.util.Stack类的构造方法创建对象
   * public class Stack extends vector
   * 构造方法:public Stack()创建一个空Stack
   * 1.public push(item)把item压入栈顶.其作用与addElement(item)相同
   * 2.public pop移除栈顶对象,并作为函数的值返回该对象
   * 3.public peek()查看栈顶对象而不移除它
   * 4.public boolean empty()测试堆栈是否为空
   * 5.public int search(Object object)返回对象在堆栈中的位置



# transient 关键字 

变量修饰符，如果用transient申明一个实例变量，当对象存储时，它的值不需要维持，换句话说，用transient关键字标记的成员变量不参与序列化的过程

 Java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。当一个对象被序列化的时候，transient型变量的值不包括在序列化的表示中，然而非transient型的变量是被包括进去的。

# ArrayList

```java
new ArrayList() //无参构造器
new ArrayList(int initialCapacity) // 有参构造器
```

ArrayList 如果new的时候如果没有初始化大小，则默认大小为10，扩容1.5倍

扩容源码

list.add() 底层源码

````java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
````

list.set(int index,E element) 源码实现

```java
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
```

list.get(int index)

```java
    public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }
```

remove(int index)

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

通过源码我们可以看到首先获取了待删除的元素，并最终返回了。其次计算了数组中需要移动的位数 size - index - 1，那么很明显我们可以得出待删除的是最后一个元素的话，移到位数为0，否则移动位数大于0，那么通过数组元素的拷贝来实现往前移动相应位数。

如remove(10)，找到的元素为“美猴王”，那么移动位数 = 12-10-1 = 1；此时将原本在第12个位置上（数组下标为11）的“白骨精”往前移动一位，同时设置elementData[11] = null；这里通过设置null值让GC起作用。

## arrayList优缺点

分析得出下面结论：

（1）ArrayList 本质上是一个可改变大小的数组.当元素加入时,其大小将会动态地增长.内部的元素可以直接通过get与set方法进行访问.元素顺序存储 ,随机访问很快，删除非头尾元素慢，新增元素慢而且费资源 ,较适用于无频繁增删的情况 ,比数组效率低，如果不是需要可变数组，可考虑使用数组 ,非线程安全.

（2）LinkedList 是一个双链表,在添加和删除元素时具有比ArrayList更好的性能.但在get与set方面弱于ArrayList. 适用于 ：没有大规模的随机读取，有大量的增加/删除操作.随机访问很慢，增删操作很快，不耗费多余资源 ,允许null元素,非线程安全.

（3）Vector （类似于ArrayList）但其是同步的，开销就比ArrayList要大。如果你的程序本身是线程安全的，那么使用ArrayList是更好的选择。 Vector和ArrayList在更多元素添加进来时会请求更大的空间。Vector每次请求其大小的双倍空间，而ArrayList每次对size增长50%.

- ArrayList自己实现了序列化和反序列化的方法，因为它自己实现了private void writeObject(java.io.ObjectOutputStream s)、private void readObject(java.io.ObjectInputStream s) 方法ArrayList基于数组方式实现，无容量的限制（会扩容）

- 添加元素时可能要扩容（所以最好预判一下），删除元素时不会减少容量（若希望减少容量，trimToSize()），删除元素时，将删除掉的位置元素置为null，下次gc就会回收这些元素所占的内存空间。

- 线程不安全，会出现fall-fail。下一篇文章会详细讲，add(int index, E element)：添加元素到数组中指定位置的时候，需要将该位置及其后边所有的元素都整块向后复制一位

- get(int index)：获取指定位置上的元素时，可以通过索引直接获取（O(1)）

- remove(Object o)需要遍历数组

- remove(int index)不需要遍历数组，只需判断index是否符合条件即可，效率比remove(Object o)高

- contains(E)需要遍历数组使用

- iterator遍历可能会引发多线程异常

# 线程安全的list

# vector

vector 和 arrayList一样，内部都是通过一个容量能够动态增长的数组来实现的。不同之处在于 vector 是线程安全的。因为内部有很多同步代码块来保证线程安全。



![](http://q8sats5bw.bkt.clouddn.com/image-20200416133536845.png)



vector 初始化容量为10，vector 扩容是有增长因子，如果指定了增长因子，就会在原来数组基础上加上增长因子，如果不指定增长因子，那么就给原数组大小 * 2。



# LinkedList

![](http://q8sats5bw.bkt.clouddn.com/image-20200416135921814.png)

 # java.util.Collections.SynchronizedList

定义个 Obj 类，内部使用一个 Collections.synchronizedList(List) 构造一个线程安全的 List 对象。

内部加了 mutex  对象锁 来实现同步

# java.util.concurrent.CopyOnWriteArrayList

![](http://q8sats5bw.bkt.clouddn.com/image-20200416144240164.png)

`CopyOnWriteArrayList` 的读操作（比如 `get()`）也不会阻塞其他操作；写操作则是通过复制一份，对复制版本进行操作，不会影响原来的数据。

如果需要在遍历时操作列表，其中一种解决办法就是使用CopyOnWriteArrayList，它的迭代器永远不会抛出 ConcurrentModificationException异常。原因在于：**在创建一个迭代器时，它会拷贝一份列表数据，这样即使操作列表也不会影响迭代器，缺点和前面一样，可能无法反映数据的最新状态。**

CopyrightOnWriteArrayList 底层也是一个数据，不同之处在于它被volatile修饰，对他的读取，修改具有可见性、有序性，但是不保证原子性，因此可以不用加同步块，直接读取。

## ReentrantLock

ReentrantLock是并发包中提供的一种AQS（AbstractQueuedSynchronizer）实现的一个可重入锁，提供公平锁和非公平锁两种锁

ReentrantLock和Syncronized的异同？

相同：都是同步锁，可重入锁。

不同：
1、Syncronized时JVM实现的，ReentrantLock是JDK（java类）实现的。
2、ReentrantLock需要配合try/finally方法进行使用。
3、ReentrantLock减少操作系统内核间的切换，提高了效率，不过Syncronized在jdk1.6之后进行了优化，效率也不低。
4、ReentrantLock是可以等待中断的，Syncronized不可以中断。
5、ReentrantLock提供公平锁和非公平锁，Syncronized只是非公平锁。

​	公平锁 和 非公平锁： 线程按照发出请求的顺序来获取锁就是公平锁。非公平锁就是线程 发出请求的时候可以 ‘插队’来获取锁。

6、ReentrantLock的API更丰富，可扩展性更好。

### AQS(AbstractQueuedSynchronizer)

是java.util.concurrent包下的抽象类

	- AQS其实就是一个可以给我们实现锁的框架
	- 内部实现的关键是：先进先出的队列、state状态
	- 定义了内部类ConditionObject
 - 拥有两种线程模型
   - 独占模式
   - 共享模式

- Lock 包中的相关锁（ReentrantLock、ReadWriteLock）都是基于AQS来构建的

- 一般我们视AQS为同步器

  

#### AQS 一些概念

AQS: AbstractQueuedSynchronizer

我们Lock之类的两个常见锁都是基于它来实现的 ReentrantLock ReentrantReadWriteLock 

AQS里边最重要的是状态和队列,依赖于先进先出的等待队列，依靠单个原子int 值来表示状态，该状态用户表示锁是在获取中还是已释放，通过给定的方法改变状态值。子类一般使用内部类来实现同步的，提供了两种模式：独占模式和共享模式，一般只会有一种模式，但ReadWriteLock两种模式并存，子类不需要再定义别的方法来实现这两种模式，这个类定义了ConditionObject内部类，提供方法进行一些列的操作，为内置的队列提供了监视等功能，某些AbstractOwnableSynchronizer 的方法可以实现监听该线程的锁是否被持有。

- AQ其实就是一个可以给我们实现锁的框架
- 内部实现的关键是：先进先出队列，state状态
- 定义了内部类ConditionObject
- 拥有两种模式

  - 独占模式
  - 共享模式


+ 使用volitile 修饰实现线程可见性

  修改state状态值时采用CAS算法来实现

  ![](http://q8sats5bw.bkt.clouddn.com/image-20200420111451156.png)

- 先进先出队列
  - CLH队列，是一个双向队列

	##### CAS算法（Compare And Swap）

- 获取独占锁的过程就是在 acquire 中定义的，该方法用到了模板设计模式，由子类实现的

  ![](http://q8sats5bw.bkt.clouddn.com/20200420135042.png)

  - release 方法，释放独占锁的过程就是在release方法中

    ![](http://q8sats5bw.bkt.clouddn.com/image-20200420140646500.png)

- 应用

  原子操作类，例如AtomicInteger，AtomicBoolean …

  适用于并发量较小，多cpu的情况下

  Java中有许多线程安全类，比如线程安全的集合类。从Java5开始，在`java.util.concurrent`包下提供了大量支持高效并发访问的集合接口和实现类。如：ConcurrentMap、ConcurrentLinkedQueue等线程安全集合

  - 乐观锁：总是认为是线程安全的，不怕别的线程修改变量，如果修改了我就再重新尝试
  - 悲观锁：总是认为线程不安全，不管什么情况都进行加锁，要是获取锁失败，就阻塞

- 优点

  - 相对于synchoroized是比较"乐观"的，它不会像synchronized一样，当一个线程访问共享数据的时候，别的线程都在阻塞。synchronized不管是否有线程冲突都会进行加锁。由于CAS是非阻塞的，它死锁问题天生免疫，并且线程间的相互影响也非常小，更重要的是，使用无锁的方式完全没有锁竞争带来的系统开销，也没有线程间频繁调度带来的开销，所以它要比锁的方式拥有更优越的性能

- 实现过程

  - 线程开启时候，会从主存中给每一个线程拷贝一个变量副本到各自的运行环境中，CAS包含三个参数（V,E,N）,V 表示要更新的变量，E表示预期值，N表示新值

    ![](http://q8sats5bw.bkt.clouddn.com/image-20200417153646955.png)

  - 假如现在有两个线程t1,t2,，他们各自的运行环境中都有共享变量的副本V1、V2，预期值E1、E2，预期主存中的值还没有被改变，假设现在在并发环境，并且t1先拿到了执行权限，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次发起尝试，然后t1比较预期值E1和主存中的V，发现E1=V，说明预期值是正确的，执行N1=V1+1，并将N1的值传入主存。这时候贮存中的V=21，然后t2又紧接着拿到了执行权，比较E2和主存V的值，由于V已经被t1改为21，所以E2！=V，t2线程将主存中已经改变的值更新到自己的副本中，再发起重试；直到预期值等于主存中的值，说明没有别的线程对旧值进行修改，继续执行代码，退出；

- 底层原理

  - 总线锁
  - 缓存锁

- CAS源码剖析

  - Unsafe 是 CAS的核心类，java 无法直接操作底层操作系统，而是通过底层方法来实现，尽管如此，jvm 还是提供了最后一个门，Unsafe 提供了硬件级别的原子操作

### JAVA 锁机制

#### Synchronized 锁

synchronized是java 的一个关键字，能够将代码块（方法) 锁起来

使用非常简单，只需要在代码块（方法）添加关键字synchronized，即可实现同步功能，是一种互斥锁，一次只允许一个线程进入被锁定的代码块，是一种内置锁/监视器锁。java 中每一个对象都有一个内置锁（监视器，也可以理解成锁标记），而synchronized 就是使用对象的内置锁（监视器）来将代码块（方法）锁定的

普通方法： 锁的是当前实例对象 （this）

静态同步方法： 锁的是当前class对象 

同步代码块：锁的是Synchronized括号里面的代码块

类锁和对象锁区别

```java
public class SynchoronizedDemo {

    //synchronized修饰非静态方法
    public synchronized void function() throws InterruptedException {
        for (int i = 0; i <3; i++) {
            Thread.sleep(1000);
            System.out.println("function running...");
        }
    }
    //synchronized修饰静态方法
    public static synchronized void staticFunction()
            throws InterruptedException {
        for (int i = 0; i < 3; i++) {
            Thread.sleep(1000);
            System.out.println("Static function running...");
        }
    }

    public static void main(String[] args) {
        final SynchoronizedDemo demo = new SynchoronizedDemo();

        // 创建线程执行静态方法
        Thread t1 = new Thread(() -> {
            try {
                staticFunction();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        // 创建线程执行实例方法
        Thread t2 = new Thread(() -> {
            try {
                demo.function();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        // 启动
        t1.start();
        t2.start();
    }
}
```



synchronized 用处是什么？

- synchronized保证了线程的**原子性**。(被保护的代码块是一次被执行的，没有任何线程会同时访问)
- synchronized还保证了**可见性**。(当执行完synchronized之后，修改后的变量对其他的线程是可见的)

Java中的synchronized，通过使用内置锁，来实现对变量的同步操作，进而实现了**对变量操作的原子性和其他线程对变量的可见性**，从而确保了并发情况下的线程安全。

synchronized 底层是通过 monitor 对象，对象有自己的头对象，存储了很多信息，其中一个信息是标识是被哪一个线程持有的。

代码 块 同步 是 使用 monitorenter 和 monitorexit 指令 实现 的

Java对象头和monitor是实现synchronized的基础

synchronized 用的锁是存在于 java 对象头，那么什么是java 对象头？

​	HostPost虚拟机(它是Sun JDK和OpenJDK中所带的虚拟机)

​	对象在内存中的存储分为3块区域： 对象头，实例数据 和 对齐填充

​	对象头主要包括两部分：Mark Word(标记字段) ,Klass Pointer（类型指针）

​	Mark Word 主要用来存储对象自身的运行时数据，如 哈希码、GC分代年龄段，锁状态标志、线程持有的锁，偏向线程ID、偏向时间戳等等。java 对象头一般占用 两个机器码 （在32位的虚拟机中）

![](http://q8sats5bw.bkt.clouddn.com/image-20200416155414919.png)

什么是monitor？

我们可以把它理解为一个同步工具，也可以描述为一种同步机制，它通常被描述为一个对象。
与一切皆对象一样，所有的Java对象是天生的Monitor，每一个Java对象都有成为Monitor的潜质，因为在Java的设计中 ，每一个Java对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者Monitor锁。
Monitor 是线程私有的数据结构，每一个线程都有一个可用monitor record列表，同时还有一个全局的可用列表。每一个被锁住的对象都会和一个monitor关联（对象头的MarkWord中的LockWord指向monitor的起始地址），同时monitor中有一个Owner字段存放拥有该锁的线程的唯一标识，表示该锁被这个线程占用

##### 锁优化

jdk1.6对锁的实现引入了大量的优化，引入 了“ 偏向 锁” 和“ 轻量级 锁”， 锁 一 共有 4 种 状态， 级别 从低 到 高 依次 是： 无 锁 状态、 偏向 锁 状态、 轻量级 锁 状态 和 重量级 锁 状态， 这 几个 状态 会 随着 竞争 情况 逐渐 升级。 锁 可以 升级 但不能 降级， 意味着 偏向 锁 升级 成 轻量级 锁 后 不能 降级 成 偏向 锁。 这种 锁 升级 却不 能 降级 的 策略， 目的 是 为了 提高 获得 锁 和 释放 锁 的 效率。

- 偏向锁： 当一个线程访问同步块并获得锁时，会在对象头和栈帧中的锁记录里存储锁偏向的线程ID，当该线程再次进入和退出同步块时，不需要再进行CAS操作加锁和解锁，只需要判断一下对象头中（Mark Word）是否存储有指向当前线程的偏向锁

- 如果存在指向当前线程的偏向锁，说明该线程已经获得了锁。

  如果不存在，则需要判断一下Mark Word中的偏向锁标识是否为1，如果不为1，就使用CAS竞争锁；如果标识为1，则尝试使用CAS将对象头中的偏向锁指向当前线程。

  JDK 1.6 1.7 中默认开启偏向锁

synchronized 锁重入

当一个线程得到一个对象锁，再次请求此对象锁时时可以得到该对象锁。这也说明在一个synchronized 方法或者代码块内部调用当前对象的其他synchronized 方法或者代码块的时候，始终可以得到锁

- synchronized 具备可重入性，对同一个线程在获得锁之后在调用其他需要同样锁的代码时可以直接调用，其可重入性是通过记录锁的持有线程和持有数量来实现的，调用 synchronized 代码时检查对象是否已经被锁，是则检查是否被当前线程锁定，是则计数加一，不是则加入等待队列，释放时计数减一直到为零释放锁。
- synchronized 还具备内存可见性，除了实现原子操作避免竞态以外对于明显是原子操作的方法（譬如一个 boolean 状态变量 state 的 get 和 set 方法）也可以通过 synchronized 来保证并发的可见性，在释放锁时所有写入都会写回内存，而获得锁后都会从内存读取最新数据；不过对于已经是原子性的操作为了保证内存可见性而使用 synchronized 的成本会比较高，轻量级的选择应该是使用 volatile 修饰，一旦修饰 java 就会在操作对应变量时插入特殊指令保证可见性
- synchronized 在发生异常时会自动释放线程占用的锁资源，Lock 需要在异常时主动释放，synchronized 在锁等待状态下无法响应中断而 Lock 可以
- synchronized 是重量级锁，其语义底层是通过一个 monitor 监视器对象来完成，其实 wait、notify 等方法也依赖于 monitor 对象，所以这就是为什么只有在同步的块或者方法中才能调用 wait、notify 等方法，否则会抛出 IllegalMonitorStateException 异常的原因，监视器锁（monitor）的本质依赖于底层操作系统的互斥锁（Mutex Lock）实现，而操作系统实现线程之间的切换需要从用户态转换到核心态，这个成本非常高，状态之间的转换需要相对比较长的时间，所以这就是为什么 synchronized 效率低且重量级的原因（Java 1.6 进行了优化，但是相比其他锁机制还是略显偏重）

synchronized 锁同步不具备继承特性

```java
class Parent {
   public synchronized void method() { }
}
class Child extends Parent {
   public void method() { }
}
```

synchronized 在发生异常时会自动释放线程占用的锁资源，Lock 需要在异常时主动释放，synchronized 在锁等待状态下无法响应中断而 Lock 可以。