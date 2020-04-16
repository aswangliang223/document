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

​	HostPost虚拟机对象头主要包括两部分：Mark Word(标记字段) ,Klass Pointer（类型指针）

​	Mark Word 主要用来存储对象自身的运行时数据，如 哈希码、GC分代年龄段，锁状态标志、线程持有的锁，偏向线程ID、偏向时间戳等等。java 对象头一般占用 两个机器码 （在32位的虚拟机中）

![](http://q8sats5bw.bkt.clouddn.com/image-20200416155414919.png)

什么是monitor？

我们可以把它理解为一个同步工具，也可以描述为一种同步机制，它通常被描述为一个对象。
与一切皆对象一样，所有的Java对象是天生的Monitor，每一个Java对象都有成为Monitor的潜质，因为在Java的设计中 ，每一个Java对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者Monitor锁。
Monitor 是线程私有的数据结构，每一个线程都有一个可用monitor record列表，同时还有一个全局的可用列表。每一个被锁住的对象都会和一个monitor关联（对象头的MarkWord中的LockWord指向monitor的起始地址），同时monitor中有一个Owner字段存放拥有该锁的线程的唯一标识，表示该锁被这个线程占用

##### 锁优化

jdk1.6对锁的实现引入了大量的优化，引入 了“ 偏向 锁” 和“ 轻量级 锁”， 锁 一 共有 4 种 状态， 级别 从低 到 高 依次 是： 无 锁 状态、 偏向 锁 状态、 轻量级 锁 状态 和 重量级 锁 状态， 这 几个 状态 会 随着 竞争 情况 逐渐 升级。 锁 可以 升级 但不能 降级， 意味着 偏向 锁 升级 成 轻量级 锁 后 不能 降级 成 偏向 锁。 这种 锁 升级 却不 能 降级 的 策略， 目的 是 为了 提高 获得 锁 和 释放 锁 的 效率。

synchronized 锁重入

当一个线程得到一个对象锁，再次请求此对象锁时时可以得到该对象锁。这也说明在一个synchronized 方法或者代码块内部调用当前对象的其他synchronized 方法或者代码块的时候，始终可以得到锁



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