# 堆和栈

## 数据结构中的堆和栈

**堆**：是一种经过排序的树形的数据结构，每一个节点都有一个值，通常指的是二叉堆。堆的特点是根节点最大或者最小，且根节点的两个子树也是一个堆，常用来实现优先级队列

### 堆的特性

- *最大堆*和*最小堆*，两者的差别在于节点的排序方式。

- 在最大堆中，父节点的值比每一个子节点的值都要大。在最小堆中，父节点的值比每一个子节点的值都要小。这就是所谓的“堆属性”，并且这个属性对堆中的每一个节点都成立。

**栈**：是一种运算受限制的线性表，只允许在栈顶插入或者删除元素。栈顶是低位，栈底是高位。栈中没有元素时称作空栈，栈符合先进后出原则（LIFO,last in first out）

- 实例:线性表
- 操作：push pop

![](http://q8sats5bw.bkt.clouddn.com/20200421101239.png)

**队列**：也是一种运算受限的线性表，特殊之处在于只允许在队列的前端进行删除操作，而在队列的后端进行插入操作，当队列中没有元素的时候，称为空队列，在队列中插入一个队列元素称为入队，从队列中删除一个队列元素称为出队。队列符合先进先出(FIFO—first in first out)原则。

![](http://q8sats5bw.bkt.clouddn.com/20200421101501.png)

## 内存申请中的堆和栈

堆：堆是在程序运行时，而不是在程序编译时，申请的某个大小的内存空间。即动态分配的内存，对其访问和对一般内存的访问没有区别。堆是应用程序在运行的时候请求操作系统分配给自己的内存，一般是申请/给予的过程，一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收

栈：由操作系统自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈



# 链表LinkedList





# 树Tree

## 二分搜索树

每个节点的值都大于其左子树的所有节点的值

每个节点的值都小于其右子树的所有节点的值

![](http://q8sats5bw.bkt.clouddn.com/20200421111946.png)

存储的元素必须具有比较性，java 中要求二分搜索树保存的数据类型要实现comparable接口，或者使用额外的比较器实现

可以认为在遍历的时候每个节点要访问三次, 对当前节点进行遍历操作时一次, 访问当前节点左子树时一次, 访问当前节点右子树时一次, 可以认为前序遍历就是在第一次访问当前节点时进行操作, 以方便我们理解遍历结果, 下面几张图演示前中后序遍历的访问顺序, 蓝色的点表示在这次访问时对当前节点进行遍历操作

遍历操作分为两类：

- 深度优先遍历 :
  - 前序遍历 : 对当前节点的遍历在对左右孩子节点的遍历之前, 遍历顺序 : 当前节点->左孩子->右孩子
  - 中序遍历 : 对当前节点的遍历在对左右孩子节点的遍历中间, 遍历顺序 : 左孩子->当前节点->右孩子
  - 后序遍历 : 对当前节点的遍历在对左右孩子节点的遍历之后, 遍历顺序 : 左孩子->右孩子->当前节点
- 广度优先遍历 :
  - 层序遍历 : 按层从左到右进行遍历
    

![](http://q8sats5bw.bkt.clouddn.com/20200421115446.png)

![](httP://q8sats5bw.bkt.clouddn.com/20200421115549.png)

![](http://q8sats5bw.bkt.clouddn.com/20200421115714.png)

- 层序遍历

  - 广度优先遍历

    - 遍历过程：遍历过程:
      		  1. 首先根节点入队		 
      		  2. 每次出队时, 都将当前节点的左右孩子先后入队
      		  3. 如果队列为空的话, 则表示层序遍历结束
      		       5      
                           /   \    
                        3    6   
                     / \    \   
                2  4     8
      		    针对上面的二分搜索树, 详细描述一下层序遍历步骤
      		  1. 5入队, 队列元素 : head->[5]<-tail
      		  2. 5出队, 5的左子树3, 6入队, 由于队列是先入先出(FIFO), 所以先左后右, 队列元素 : head->[3, 6]<-tail
      		  3. 3出队, 2, 4入队, 队列元素  : head->[6, 2, 4]<-tail
      		  4. 6出队, 左孩子为空,所以8入队, 队列元素  : head->[2, 4, 8]<-tail
      		  5. 2,4,8依次出队, 由于这三个节点都是叶子节点, 无子节点, 所以这三个节点出队后队列为空, 层序遍历完成
      		  6. 按照出队的顺序演示的遍历结果为 : 5 3 6 2 4 8

    

    ```java
    package com.company.sort;
    
    import java.util.LinkedList ;
    import java.util.Queue ;
    import java.util.Stack ;
    
    /**
     * 递归实现二分搜索树
     * 这里设计的树是不存储重复元素的, 重复添加元素只保存一个
     * @author 七夜雪
     *
     */
    public class BinarySearchTree<E extends Comparable<E>> {
    
        // 根节点
        private Node root ;
        // 树容量
        private int size ;
    
        public BinarySearchTree() {
            this.root = null ;
            this.size = 0 ;
        }
    
        public boolean isEmpty() {
            return size == 0 ;
        }
    
        public int getSize(){
            return size;
        }
    
        /**
         * 向二分搜索树上添加节点
         * @param e
         */
        public void add(E e) {
            root = add(root, e) ;
        }
    
        /**
         * 向以node节点为根节点的树上添加节点e
         * 递归方法
         * @param node
         * @param e
         */
        private Node add(Node node, E e) {
            // 递归终止条件
            if (node == null) {
                size++ ;
                return new Node(e) ;
            }
    
            // 添加的元素小于当前元素, 向左递归
            if (node.e.compareTo(e) > 0) {
                node.left = add(node.left, e) ;
                // 添加的元素小于当前元素, 向右递归
            } else if (node.e.compareTo(e) < 0) {
                node.right = add(node.right, e) ;
            }
    
            return node ;
        }
    
        /**
         * 判断树中是否包含元素e
         * @param e
         * @return
         */
        public boolean contains(E e) {
            return contains(root, e) ;
        }
    
        /**
         * 判断树中是否包含元素e
         * 递归方法
         * @param node
         * @param e
         * @return
         */
        private boolean contains(Node node, E e) {
            // 递归终结条件
            if (node == null) {
                return false ;
            }
    
            if (node.e.compareTo(e) == 0) {
                return true;
            } else if (node.e.compareTo(e) > 0) {
                return contains(node.left, e) ;
            } else { // node.e < e
                return contains(node.right, e) ;
            }
    
        }
    
        /**
         * 前序遍历树
         */
        public void preOrder() {
            preOrder(root) ;
        }
    
        /**
         *
         * 前序遍历的递归方法, 深度优先
         * 前序遍历是指,先访问当前节点, 然后再访问左右子节点
         * @param node
         */
        private void preOrder(Node node) {
            // 递归终止条件
            if (node == null) {
                return ;
            }
    
            // 1. 前序遍历先访问当前节点
            System.out.println(node.e) ;
            // 2. 前序遍历访问左孩子
            preOrder(node.left) ;
            // 3. 前序遍历访问右孩子
            preOrder(node.right) ;
        }
    
        /**
         * 前序遍历的非递归方法, 深度优先
         * 这里使用栈进行辅助实现
         * 前序遍历是指,先访问当前节点, 然后再访问左右子节点
         */
        public void preOrderNR() {
            // 使用栈辅助实现前序遍历
            Stack<Node> stack = new Stack<>();
            /*
             * 前序遍历的过程就是先访问当前节点, 然后再访问左子树, 然后右子树
             * 所以使用栈实现时, 可以先将当前节点入栈, 当前节点出栈时,
             * 分别将当前节点的右孩子, 左孩子压入栈
             */
            // 首先将根节点压入栈
            stack.push(root);
            while (!stack.isEmpty()) {
                Node cur = stack.pop();
                // 前序遍历当前节点
                System.out.println(cur.e) ;
                // 由于栈是后入先出, 前序遍历是先左孩子, 再右孩子, 所以这里需要先将右孩子压入栈
                if (cur.right != null) {
                    stack.push(cur.right);
                }
    
                if (cur.left != null) {
                    stack.push(cur.left);
                }
            }
        }
    
        /**
         * 中序遍历树, 深度优先
         */
        public void inOrder() {
            inOrder(root) ;
        }
    
        /**
         *
         * 中序遍历的递归方法, 深度优先
         * 中序遍历指的是访问当前元素的顺序放在访问左右子节点之间
         * 中序遍历的结果是有序的
         * @param node
         */
        private void inOrder(Node node) {
            // 递归终止条件
            if (node == null) {
                return ;
            }
    
            // 1. 中序遍历访问左孩子
            inOrder(node.left) ;
            // 2. 中序遍历先访问当前节点
            System.out.println(node.e) ;
            // 3. 中序遍历访问右孩子
            inOrder(node.right) ;
    
        }
    
        /**
         * 后序遍历树, 深度优先
         */
        public void postOrder() {
            postOrder(root) ;
        }
    
        /**
         *
         * 后序遍历的递归方法, 深度优先
         * 后序遍历指的是访问当前元素的顺序放在访问左右子节点之后
         * @param node
         */
        private void postOrder(Node node) {
            // 递归终止条件
            if (node == null) {
                return ;
            }
    
            // 1. 后序遍历访问左孩子
            postOrder(node.left) ;
            // 2. 后序遍历访问右孩子
            postOrder(node.right) ;
            // 3. 后序遍历先访问当前节点
            System.out.println(node.e) ;
        }
    
        /**
         * 层序遍历, 从左到右一层一层遍历
         * 借助队列实现
         */
        public void levelOrder(){
            // LinkedList实现了Queue接口
            Queue<Node> queue = new LinkedList<>();
            /*
             * 遍历过程:
             * 1. 首先根节点入队
             * 2. 每次出队时, 都将当前节点的左右孩子先后入队
             * 3. 如果队列为空的话, 则表示层序遍历结束
             *      5
             *    /   \
             *   3    6
             *  / \    \
             * 2  4     8
             * 针对上面的二分搜索树, 详细描述一下层序遍历步骤
             * 1. 5入队, 队列元素 : head->[5]<-tail
             * 2. 5出队, 5的左子树3, 6入队, 由于队列是先入先出(FIFO), 所以先左后右, 队列元素 : head->[3, 6]<-tail
             * 3. 3出队, 2, 4入队, 队列元素  : head->[6, 2, 4]<-tail
             * 4. 6出队, 左孩子为空,所以8入队, 队列元素  : head->[2, 4, 8]<-tail
             * 5. 2,4,8依次出队, 由于这三个节点都是叶子节点, 无子节点, 所以这三个节点出队后队列为空, 层序遍历完成
             * 6. 按照出队的顺序演示的遍历结果为 : 5 3 6 2 4 8
             */
            queue.add(root);
    
            while(!queue.isEmpty()){
                Node cur = queue.poll();
                if (cur.left != null) {
                    queue.add(cur.left);
                }
                if (cur.right != null) {
                    queue.add(cur.right);
                }
            }
        }
    
        /**
         * 查找树中最小元素
         * @return
         */
        public E minimum(){
            if (size == 0) {
                throw new IllegalArgumentException("BSTree is empty");
            }
            return minimum(root).e;
        }
    
        /**
         * 查找以node为根节点的最小元素, 递归方法
         * @param node
         * @return
         */
        private Node minimum(Node node){
            if (node.left == null) {
                return node;
            }
            return minimum(node.left);
        }
    
        /**
         * 查找树中最大元素
         * @return
         */
        public E maximum(){
            if (size == 0) {
                throw new IllegalArgumentException("BSTree is empty");
            }
            return maximum(root).e;
        }
    
        /**
         * 查找以node为根节点的最大元素, 递归方法
         * @param node
         * @return
         */
        private Node maximum(Node node){
            if (node.right == null) {
                return node;
            }
            return maximum(node.right);
        }
    
        /**
         * 删除二分搜索树中的最小值
         * @return
         */
        public E removeMin(){
            E ret = minimum();
            root = removeMin(root);
            return ret;
        }
    
        /**
         * 删除以node为根节点的树的最小值
         * @param node
         * @return 返回删除后的新的二分搜索树的根
         */
        private Node removeMin(Node node){
            // 递归终止条件
            if(node.left == null){
                Node rightNode = node.right;
                node.right = null;
                size --;
                return rightNode;
            }
    
            node.left = removeMin(node.left);
            return node;
        }
    
        /**
         * 删除二分搜索树中的最大值
         * @return
         */
        public E removeMax(){
            E ret = maximum();
            root = removeMax(root);
            return ret;
        }
    
        /**
         * 删除以node为根节点的树的最大值
         * @param node
         * @return 返回删除后的新的二分搜索树的根
         */
        private Node removeMax(Node node){
            // 递归终止条件
            if(node.right == null){
                Node leftNode = node.left;
                node.left = null;
                size --;
                return leftNode;
            }
    
            node.right = removeMin(node.right);
            return node;
        }
    
        /**
         * 删除指定元素e所在的节点
         * @param e
         */
        public void remove(E e){
            root = remove(root, e);
        }
    
        /**
         * 删除以node为根节点中的二分搜索树中
         * @param node
         * @param e
         * @return 返回删除后的新二分搜索树的根节点
         */
        private Node remove(Node node, E e){
            if (node == null) {
                return null;
            }
    
            // node.e > e
            if (node.e.compareTo(e) > 0) {
                node.left =remove(node.left, e);
                return node;
                // node.e < e
            } else if (node.e.compareTo(e) < 0) {
                node.right = remove(node.right, e);
                return node;
            } else { // e == node.e
                // 待删除节点左子树为空的情况
                if (node.left == null) {
                    Node rightNode = node.right;
                    node.right = null;
                    size --;
                    return rightNode;
                }
    
                // 待删除节点右子树为空的情况
                if (node.right == null) {
                    Node leftNode = node.left;
                    node.left = null;
                    size --;
                    return leftNode;
                }
    
                // 待删除节点左右子树均不为空的情况
    
                // 查找待删除节点的后继节点
                // 用后继节点替换当前待删除节点
    
                // 查找后继节点, 从待删除节点的右子树,查找最小值
                Node successor = minimum(node.right);
                /*
                 *  需要注意的是, 这里removeMin中进行了size--操作,
                 *  但是实际上最小的元素变成了successor, 并没有删除
                 *  所以按照逻辑的话, 这里应该有一个size++
                 *  但是后面删除了元素之后,需要再进行一次size--, 所以这里就不对size进行操作了
                 */
                successor.right = removeMin(node.right);
                successor.left = node.left;
                // 后继节点完成替换, 删除当前节点
                node.left = node.right = null;
                return successor;
            }
        }
    
    
        @Override
        public String toString() {
            StringBuilder res = new StringBuilder();
            generateString(root, 0, res);
            return res.toString();
        }
    
        /**
         * 生成以node为根节点，深度为depth的描述二叉树的字符串
         * @param node 根节点
         * @param depth 深度
         * @param res 用于拼接字符串的StringBuilder
         * @return
         */
        private void generateString(Node node, int depth, StringBuilder res){
            if (node == null) {
                res.append(generateDepthString(depth) + "null\n");
                return;
            }
            res.append(generateDepthString(depth)).append(node.e + "\n");
            generateString(node.left, depth + 1, res);
            generateString(node.right, depth + 1, res);
        }
    
        private StringBuilder generateDepthString(int depth){
            StringBuilder res = new StringBuilder();
            for (int i = 0; i < depth; i++) {
                res.append("--");
            }
            return res;
        }
    
        /**
         * 二分搜索树节点类
         * @author 七夜雪
         *
         */
        private class Node {
            public E	e ;
            // 左右子树
            public Node	left , right ;
    
            public Node(E e) {
                this.e = e ;
                this.left = null;
                this.right = null;
            }
        }
    }
    ```


## 红黑树

特点：

- 每个节点或是红色，或是黑色。
- 根节点是黑色的
- 每个叶子节点是黑色的
- 如果一个节点是红色，他的两个儿子节点都是黑色（从每个叶子到根的所有路径上不能有两个连续的红色节点）。
- 对于每一个节点，从该节点到其孙子节点所有路径上包含相同的黑节点

旋转+变色

红黑树节点添加：

1. 父亲节点是黑色的不用进行调整
2. 父亲节点是红色的：
   1. 叔叔节点是空的/黑色的，旋转+变色
   2. 叔叔节点是红色，父亲节点+叔叔节点变成黑色，祖父节点变成红色。

### HashMap-JDK1.7实现: 数组  + 链表

```java
HashMap map = new HashMap<String，Integer>();
map.put("123",456)
```

```java
    static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next;
        int hash;
    }

// new HashMap<>() 调用无参构造器，无参构造器并没有初始化这个tab，而是在第一次put操作时去初始化
// DEFAULT_INITIAL_CAPACITY 默认数组的初始容量 =16
// DEFAULT_LOAD_FACTOR 默认的扩容因子 = 0.75
   public HashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
    }
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity;
        init();
    }

// map.put(); 方法实现如下：
    public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            // 初始化一个容量为16大小的tab
            inflateTable(threshold);
        }
        if (key == null)
            // key为null将value放在数组的0号位置
            // 注意： k不为null的情况下，算出来的(k.hashcode) & (length -1) 也有可能返回的值为 0 ，那么这种其情况下的数据也会存在 0号位置
            return putForNullKey(value);
        // key去做hash操作
        int hash = hash(key);
        // hash 值  和 数组容量-1 去做与运算 得到 index（entry在数组中存储的索引位置）
        int i = indexFor(hash, table.length);
        // 循环的作用主要是判断是否有一个相同的key，如果当前index 的链表上没有相同的key，则头插法和尾插法效果相同。都需要遍历走完这个循环
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        // 修改次数
        modCount++;
        // 插入元素
        addEntry(hash, key, value, i);
        return null;
    }
// inflateTable() table初始化
    private void inflateTable(int toSize) {
        // Find a power of 2 >= toSize
        // 找到大于等于toSize的2的幂次方数据
        int capacity = roundUpToPowerOf2(toSize);

        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        initHashSeedAsNeeded(capacity);
    }
// key.hashcode() 方法：
    final int hash(Object k) {
        // hashSeed 表示hash种子，用户自定义hash散列参数
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        // hash种子和key的hashcode()去异或
        // a⊕b = (¬a ∧ b) ∨ (a ∧¬b)
        // 如果a、b两个值不相同，则异或结果为1。如果a、b两个值相同，异或结果为0
        h ^= k.hashCode();

        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).

        // 32位的hashcode的高28位参与到index的计算过程中来
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }

// indexFor() :计算插入元素的索引位置
    /**
     * Returns index for hash code h.
     * 返回index（数组索引） ： h.hashcode() & length -1
     */
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        // 数组容量 -1  和 key.hashcode() 去做 与 操作
        return h & (length-1);
    }


// 添加元素
    void addEntry(int hash, K key, V value, int bucketIndex) {
        // 扩容因子默认：0.75
        // threshold 阈值： map中存储的元素的个数 * 扩容因子*table.length & 要插入的位置的链表不为空 ，这两个条件同时满足才会进行扩容
        if ((size >= threshold) && (null != table[bucketIndex])) {
            // 扩容大小 为 2*oldLength
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        // 采用头插法将value插入到链表头节点
        createEntry(hash, key, value, bucketIndex);
    }

// key为null 的情况 的put操作
    private V putForNullKey(V value) {

        // 数组0号位置不仅存key 为 null的entry 还存了key的hashcode & length -1 后的 index 为0的entry，所以这里put 的时候还是需要去遍历
        // 遍历的目的主要是为了相同key的value替换
        for (Entry<K,V> e = table[0]; e != null; e = e.next) {
            if (e.key == null) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        addEntry(0, null, value, 0);
        return null;
    }

// 数组扩容
    void resize(int newCapacity) {
        // 数组扩容 new 一个新的扩容后的数组
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        // 转移后新的链表和原链表数据顺序正好相反
        // initHashSeedAsNeeded(newCapacity) 这个结果返回true，才会发生rehash，是否重新初始化hash种子
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        // 将新数组赋值给table
        table = newTable;
        // 重新计算阈值
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }

// 数组元素转移到新的tab中
 /**
     * Transfers all entries from current table to newTable.
     * @params rehash 默认为false
     */
    void transfer(Entry[] newTable, boolean rehash) {
        // 将老的数组的每一个位置上的链表上的每一个元素转移到新的数组上来
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                // 扩容后的新的entry在newTable上存放的index，扩容后的index 可能不变，可能为 oldIndex + oldTable.length
                // JDK1.7table扩容会出现循环链表的问题
                // 扩容的目的不仅仅是将table扩大两倍，还会将value 分散到不同的 index 的链表中去
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }

// remove

        /**
         * iterator中的remove方法，不会出现fast-fail异常 （ConcurrentModificationException）
         * 原因在于删除之后会对exceptedModCount重新赋值为修改后的modeCount
         */
        public void remove() {
            if (current == null)
                throw new IllegalStateException();
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
            Object k = current.key;
            current = null;
            HashMap.this.removeEntryForKey(k);
            //
            expectedModCount = modCount;
        }
```



### HashMap-JDK1.8实现 : 数组 + 链表 + 红黑树

```java
HashMap map = new HashMap<String，Integer>();
map.put("123",456)
```

```java
// entry 数据结构    
static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next; // 单链表
        int hash;
    }
// TreeNode 数据结构
static final class TreeNode<K, V> extends LinkedHashMap.Entry<K, V> {
        TreeNode<K, V> parent;  //节点的父亲
        TreeNode<K, V> left;    //节点的左孩子
        TreeNode<K, V> right;   //节点的右孩子
        TreeNode<K, V> prev;    //节点的前一个节点
        boolean red;            //true表示红节点，false表示黑节点,红黑树中默认插入的是红节点
}

/**
     * 使用指定的初始化容量（16）和默认加载因子DEFAULT_LOAD_FACTOR（0.75）构造一个空HashMap
     */
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }

    /**
     * 将指定参数key和指定参数value插入map中，如果key已经存在，那就替换key对应的value
     * put(K key, V value)可以分为三个步骤：
     * 1.通过hash(Object key)方法计算key的哈希值。
     * 2.通过putVal(hash(key), key, value, false, true)方法实现功能。
     * 3.返回putVal方法返回的结果。
     *
     * @param key   指定key
     * @param value 指定value
     * @return 如果value被替换，则返回旧的value，否则返回null。当然，可能key对应的value就是null
     */
    public V put(K key, V value) {
        // 倒数第二个参数false：表示允许旧值替换
        // 最后一个参数true：表示HashMap不处于创建模式
        return putVal(hash(key), key, value, false, true);
    }

    /**
     * Map.put和其他相关方法的实现需要的方法
     * putVal方法可以分为下面的几个步骤:
     * 1.如果哈希表为空，调用resize()创建一个哈希表。
     * 2.如果指定参数hash在表中没有对应的桶，即为没有碰撞，直接将键值对插入到哈希表中即可。
     * 3.如果有碰撞，遍历桶，找到key映射的节点
     * 3.1桶中的第一个节点就匹配了，将桶中的第一个节点记录起来。
     * 3.2如果桶中的第一个节点没有匹配，且桶中结构为红黑树，则调用红黑树对应的方法插入键值对。
     * 3.3如果不是红黑树，那么就肯定是链表。遍历链表，如果找到了key映射的节点，就记录这个节点，退出循环。如果没有找到，在链表尾部插入节点。插入后，如果链的长度大于等于TREEIFY_THRESHOLD这个临界值，则使用treeifyBin方法把链表转为红黑树。
     * 4.如果找到了key映射的节点，且节点不为null
     * 4.1记录节点的vlaue。
     * 4.2如果参数onlyIfAbsent为false，或者oldValue为null，替换value，否则不替换。
     * 4.3返回记录下来的节点的value。
     * 5.如果没有找到key映射的节点（2、3步中讲了，这种情况会插入到hashMap中），插入节点后size会加1，这时要检查size是否大于临界值threshold，如果大于会使用resize方法进行扩容。
     *
     * @param hash         指定参数key的哈希值
     * @param key          指定参数key
     * @param value        指定参数value
     * @param onlyIfAbsent 如果为true，即使指定参数key在map中已经存在，也不会替换value
     * @param evict        如果为false，数组table在创建模式中
     * @return 如果value被替换，则返回旧的value，否则返回null。当然，可能key对应的value就是null。
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K, V>[] tab;
        Node<K, V> p;
        int n, i;
        //如果哈希表为空，调用resize()创建一个哈希表，并用变量n记录哈希表长度
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        /**
         * 如果指定参数hash在表中没有对应的桶，即为没有碰撞
         * Hash函数，(n - 1) & hash 计算key将被放置的槽位
         * (n - 1) & hash 本质上是hash % n，位运算更快
         */
        if ((p = tab[i = (n - 1) & hash]) == null)
            //直接将键值对插入到map中即可
            tab[i] = newNode(hash, key, value, null);
        else {// 桶中已经存在元素
            Node<K, V> e;
            K k;
            // 比较桶中第一个元素(数组中的结点)的hash值相等，key相等
            if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                // 将第一个元素赋值给e，用e来记录
                e = p;
                // 当前桶中无该键值对，且桶是红黑树结构，按照红黑树结构插入
            else if (p instanceof TreeNode)
                e = ((TreeNode<K, V>) p).putTreeVal(this, tab, hash, key, value);
                // 当前桶中无该键值对，且桶是链表结构，按照链表结构插入到尾部
            else {
                for (int binCount = 0; ; ++binCount) {
                    // 遍历到链表尾部
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        // 检查链表长度是否达到阈值，达到将该槽位节点组织形式转为红黑树
                        // TREEIFY_THRESHOLD 为 8
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    // 链表节点的<key, value>与put操作<key, value>相同时，不做重复操作，跳出循环
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            // 找到或新建一个key和hashCode与插入元素相等的键值对，进行put操作
            if (e != null) { // existing mapping for key
                // 记录e的value
                V oldValue = e.value;
                /**
                 * onlyIfAbsent为false或旧值为null时，允许替换旧值
                 * 否则无需替换
                 */
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                // 访问后回调
                afterNodeAccess(e);
                // 返回旧值
                return oldValue;
            }
        }
        // 更新结构化修改信息
        ++modCount;
        // 键值对数目超过阈值时，进行rehash
        // 判断加入新的元素后，长度是否大于扩容阈值（JDK1.8）
        // 判断加入新的元素后，长度是否大于扩容阈值 & 插入元素的位置上的链表不为空 （JDK1.7）
        if (++size > threshold)
            resize();
        // 插入后回调
        afterNodeInsertion(evict);
        return null;
    }


// table扩容
    /**
     * 对table进行初始化或者扩容。
     * 如果table为null，则对table进行初始化
     * 如果对table扩容，因为每次扩容都是翻倍，与原来计算（n-1）&hash的结果相比，节点要么就在原来的位置，要么就被分配到“原位置+旧容量”这个位置
     * resize的步骤总结为:
     * 1.计算扩容后的容量，临界值。
     * 2.将hashMap的临界值修改为扩容后的临界值
     * 3.根据扩容后的容量新建数组，然后将hashMap的table的引用指向新数组。
     * 4.将旧数组的元素复制到table中。
     *
     * @return the table
     */
    final Node<K, V>[] resize() {
        //新建oldTab数组保存扩容前的数组table
        Node<K, V>[] oldTab = table;
        //获取原来数组的长度
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        //原来数组扩容的临界值
        int oldThr = threshold;
        int newCap, newThr = 0;
        //如果扩容前的容量 > 0
        if (oldCap > 0) {
            //如果原来的数组长度大于最大值(2^30)
            if (oldCap >= MAXIMUM_CAPACITY) {
                //扩容临界值提高到正无穷
                threshold = Integer.MAX_VALUE;
                //无法进行扩容，返回原来的数组
                return oldTab;
                //如果现在容量的两倍小于MAXIMUM_CAPACITY且现在的容量大于DEFAULT_INITIAL_CAPACITY
            } else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                    oldCap >= DEFAULT_INITIAL_CAPACITY)
                //临界值变为原来的2倍
                newThr = oldThr << 1;
        } else if (oldThr > 0) //如果旧容量 <= 0，而且旧临界值 > 0
            //数组的新容量设置为老数组扩容的临界值
            newCap = oldThr;
        else { //如果旧容量 <= 0，且旧临界值 <= 0，新容量扩充为默认初始化容量，新临界值为DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY
            newCap = DEFAULT_INITIAL_CAPACITY;//新数组初始容量设置为默认值
            newThr = (int) (DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);//计算默认容量下的阈值
        }
        // 计算新的resize上限
        if (newThr == 0) {//在当上面的条件判断中，只有oldThr > 0成立时，newThr == 0
            //ft为临时临界值，下面会确定这个临界值是否合法，如果合法，那就是真正的临界值
            float ft = (float) newCap * loadFactor;
            //当新容量< MAXIMUM_CAPACITY且ft < (float)MAXIMUM_CAPACITY，新的临界值为ft，否则为Integer.MAX_VALUE
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float) MAXIMUM_CAPACITY ?
                    (int) ft : Integer.MAX_VALUE);
        }
        //将扩容后hashMap的临界值设置为newThr
        threshold = newThr;
        //创建新的table，初始化容量为newCap
        @SuppressWarnings({"rawtypes", "unchecked"})
        Node<K, V>[] newTab = (Node<K, V>[]) new Node[newCap];
        //修改hashMap的table为新建的newTab
        table = newTab;
        //如果旧table不为空，将旧table中的元素复制到新的table中
        if (oldTab != null) {
            //遍历旧哈希表的每个桶，将旧哈希表中的桶复制到新的哈希表中
            for (int j = 0; j < oldCap; ++j) {
                Node<K, V> e;
                //如果旧桶不为null，使用e记录旧桶
                if ((e = oldTab[j]) != null) {
                    //将旧桶置为null
                    oldTab[j] = null;
                    //如果旧桶中只有一个node
                    if (e.next == null)
                        //将e也就是oldTab[j]放入newTab中e.hash & (newCap - 1)的位置
                        newTab[e.hash & (newCap - 1)] = e;
                        //如果旧桶中的结构为红黑树
                    else if (e instanceof TreeNode)
                        //将树中的node分离
                        ((TreeNode<K, V>) e).split(this, newTab, j, oldCap);
                    else {  //如果旧桶中的结构为链表,链表重排，jdk1.8做的一系列优化
                        Node<K, V> loHead = null, loTail = null;
                        Node<K, V> hiHead = null, hiTail = null;
                        Node<K, V> next;
                        // 链表扩容
                        //遍历整个链表中的节点
                        do {
                            next = e.next;
                            // 原索引
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            } else {// 原索引+oldCap
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        // 原索引放到bucket里
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        // 原索引+oldCap放到bucket里
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }

// 链表转成红黑树
 
    /**
     * 将链表转化为红黑树
     */
    final void treeifyBin(Node<K, V>[] tab, int hash) {
        int n, index;
        Node<K, V> e;
        //如果桶数组table为空，或者桶数组table的长度小于MIN_TREEIFY_CAPACITY = 16，不符合转化为红黑树的条件
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            //扩容
            resize();
            //如果符合转化为红黑树的条件，而且hash对应的桶不为null
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            // 红黑树的头、尾节点
            TreeNode<K, V> hd = null, tl = null;
            //遍历链表
            do {
                //替换链表node为树node，建立双向链表
                TreeNode<K, V> p = replacementTreeNode(e, null);
                // 确定树头节点
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            //遍历链表插入每个节点到红黑树
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }

// 删除操作
    /**
     * 删除hashMap中key映射的node
     * remove方法的实现可以分为三个步骤：
     * 1.通过 hash(Object key)方法计算key的哈希值。
     * 2.通过 removeNode 方法实现功能。
     * 3.返回被删除的node的value。
     *
     * @param key 参数key
     * @return 如果没有映射到node，返回null，否则返回对应的value
     */
    public V remove(Object key) {
        Node<K, V> e;
        //根据key来删除node。removeNode方法的具体实现在下面
        return (e = removeNode(hash(key), key, null, false, true)) == null ?
                null : e.value;
    }

    /**
     * Map.remove和相关方法的实现需要的方法
     * removeNode方法的步骤总结为:
     * 1.如果数组table为空或key映射到的桶为空，返回null。
     * 2.如果key映射到的桶上第一个node的就是要删除的node，记录下来。
     * 3.如果桶内不止一个node，且桶内的结构为红黑树，记录key映射到的node。
     * 4.桶内的结构不为红黑树，那么桶内的结构就肯定为链表，遍历链表，找到key映射到的node，记录下来。
     * 5.如果被记录下来的node不为null，删除node，size-1被删除。
     * 6.返回被删除的node。
     *
     * @param hash       key的哈希值
     * @param key        key的哈希值
     * @param value      如果 matchValue 为true，则value也作为确定被删除的node的条件之一，否则忽略
     * @param matchValue 如果为true，则value也作为确定被删除的node的条件之一
     * @param movable    如果为false，删除node时不会删除其他node
     * @return 返回被删除的node，如果没有node被删除，则返回null（针对红黑树的删除方法）
     */
    final Node<K, V> removeNode(int hash, Object key, Object value,
                                boolean matchValue, boolean movable) {
        Node<K, V>[] tab;
        Node<K, V> p;
        int n, index;
        //如果数组table不为空且key映射到的桶不为空
        if ((tab = table) != null && (n = tab.length) > 0 &&
                (p = tab[index = (n - 1) & hash]) != null) {
            Node<K, V> node = null, e;
            K k;
            V v;
            //如果桶上第一个node的就是要删除的node
            if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                //记录桶上第一个node
                node = p;
            else if ((e = p.next) != null) {//如果桶内不止一个node
                //如果桶内的结构为红黑树
                if (p instanceof TreeNode)
                    //记录key映射到的node
                    node = ((TreeNode<K, V>) p).getTreeNode(hash, key);
                else {//如果桶内的结构为链表
                    do {//遍历链表，找到key映射到的node
                        if (e.hash == hash &&
                                ((k = e.key) == key ||
                                        (key != null && key.equals(k)))) {
                            //记录key映射到的node
                            node = e;
                            break;
                        }
                        p = e;
                    } while ((e = e.next) != null);
                }
            }
            //如果得到的node不为null且(matchValue为false||node.value和参数value匹配)
            if (node != null && (!matchValue || (v = node.value) == value ||
                    (value != null && value.equals(v)))) {
                //如果桶内的结构为红黑树
                if (node instanceof TreeNode)
                    //使用红黑树的删除方法删除node
                    ((TreeNode<K, V>) node).removeTreeNode(this, tab, movable);
                else if (node == p)//如果桶的第一个node的就是要删除的node
                    //删除node
                    tab[index] = node.next;
                else//如果桶内的结构为链表，使用链表删除元素的方式删除node
                    p.next = node.next;
                ++modCount;//结构性修改次数+1
                --size;//哈希表大小-1
                afterNodeRemoval(node);
                return node;//返回被删除的node
            }
        }
        return null;//如果数组table为空或key映射到的桶为空，返回null。
    }


//get(Object key)

 /**
     * 返回指定的key映射的value，如果value为null，则返回null
     * get可以分为三个步骤：
     * 1.通过hash(Object key)方法计算key的哈希值hash。
     * 2.通过getNode( int hash, Object key)方法获取node。
     * 3.如果node为null，返回null，否则返回node.value。
     *
     * @see #put(Object, Object)
     */
    public V get(Object key) {
        Node<K, V> e;
        //根据key及其hash值查询node节点，如果存在，则返回该节点的value值
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    /**
     * 根据key的哈希值和key获取对应的节点
     * getNode可分为以下几个步骤：
     * 1.如果哈希表为空，或key对应的桶为空，返回null
     * 2.如果桶中的第一个节点就和指定参数hash和key匹配上了，返回这个节点。
     * 3.如果桶中的第一个节点没有匹配上，而且有后续节点
     * 3.1如果当前的桶采用红黑树，则调用红黑树的get方法去获取节点
     * 3.2如果当前的桶不采用红黑树，即桶中节点结构为链式结构，遍历链表，直到key匹配
     * 4.找到节点返回null，否则返回null。
     *
     * @param hash 指定参数key的哈希值
     * @param key  指定参数key
     * @return 返回node，如果没有则返回null
     */
    final Node<K, V> getNode(int hash, Object key) {
        Node<K, V>[] tab;
        Node<K, V> first, e;
        int n;
        K k;
        //如果哈希表不为空，而且key对应的桶上不为空
        if ((tab = table) != null && (n = tab.length) > 0 &&
                (first = tab[(n - 1) & hash]) != null) {
            //如果桶中的第一个节点就和指定参数hash和key匹配上了
            if (first.hash == hash && // always check first node
                    ((k = first.key) == key || (key != null && key.equals(k))))
                //返回桶中的第一个节点
                return first;
            //如果桶中的第一个节点没有匹配上，而且有后续节点
            if ((e = first.next) != null) {
                //如果当前的桶采用红黑树，则调用红黑树的get方法去获取节点
                if (first instanceof TreeNode)
                    return ((TreeNode<K, V>) first).getTreeNode(hash, key);
                //如果当前的桶不采用红黑树，即桶中节点结构为链式结构
                do {
                    //遍历链表，直到key匹配
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        //如果哈希表为空，或者没有找到节点，返回null
        return null;
    }


        /**
         * 将链表转为二叉树
         *
         * @return root of tree
         */
        final void treeify(Node<K, V>[] tab) {
            TreeNode<K, V> root = null;
            for (TreeNode<K, V> x = this, next; x != null; x = next) {
                next = (TreeNode<K, V>) x.next;
                x.left = x.right = null;
                if (root == null) {
                    x.parent = null;
                    x.red = false;
                    root = x;
                } else {
                    K k = x.key;
                    int h = x.hash;
                    Class<?> kc = null;
                    for (TreeNode<K, V> p = root; ; ) {
                        int dir, ph;
                        K pk = p.key;
                        if ((ph = p.hash) > h)
                            dir = -1;
                        else if (ph < h)
                            dir = 1;
                        else if ((kc == null &&
                                (kc = comparableClassFor(k)) == null) ||
                                (dir = compareComparables(kc, k, pk)) == 0)
                            dir = tieBreakOrder(k, pk);

                        TreeNode<K, V> xp = p;
                        // 判断插入元素在当前节点的左侧还是右侧
                        if ((p = (dir <= 0) ? p.left : p.right) == null) {
                            x.parent = xp;
                            if (dir <= 0)
                                // 小于插入到左侧
                                xp.left = x;
                            else
                                // 大于插入右侧
                                xp.right = x;
                            // 红黑树中插入 x 节点
                            root = balanceInsertion(root, x);
                            break;
                        }
                    }
                }
            }
            // 将红黑树移动到数组的某一个位置上去
            moveRootToFront(tab, root);
        }

        /**
         * 将二叉树转为链表
         */
        final Node<K, V> untreeify(HashMap<K, V> map) {
            Node<K, V> hd = null, tl = null;
            for (Node<K, V> q = this; q != null; q = q.next) {
                Node<K, V> p = map.replacementNode(q, null);
                if (tl == null)
                    hd = p;
                else
                    tl.next = p;
                tl = p;
            }
            return hd;
        }

```

### ConcurrentHashMap-JDK1.7实现：segment分段锁+链表

segment 类实现了 ReentrantLock

```java
ConcurrentHashMap map = new ConcurrentHashMap<>();
map.put("123",456);
```

```java
// 数据结构 每一个 ConcurrentHashMap包含一个segment<K,V>数组，每一个segment对象中包含一个HashEntry数组
public class ConcurrentHashMap<K, V> extends AbstractMap<K, V>
        implements ConcurrentMap<K, V>, Serializable {
    final Segment<K,V>[] segments;
        static final class HashEntry<K,V> {
        final int hash;
        final K key;
        volatile V value;
        volatile HashEntry<K,V> next;
}

// 无参构造
    public ConcurrentHashMap() {
        // 初始容量 DEFAULT_INITIAL_CAPACITY = 16
        // 默认扩容因子 DEFAULT_LOAD_FACTOR  = 0.75
        // 默认并发级别，即segment的个数 DEFAULT_CONCURRENCY_LEVEL = 16
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
    }
    
        /**
     * Creates a new, empty map with the specified initial
     * capacity, load factor and concurrency level.
     *
     * @param initialCapacity the initial capacity. The implementation
     * performs internal sizing to accommodate this many elements.
     * @param loadFactor  the load factor threshold, used to control resizing.
     * Resizing may be performed when the average number of elements per
     * bin exceeds this threshold.
     * @param concurrencyLevel the estimated number of concurrently
     * updating threads. The implementation performs internal sizing
     * to try to accommodate this many threads.
     * @throws IllegalArgumentException if the initial capacity is
     * negative or the load factor or concurrencyLevel are
     * nonpositive.
     */
    @SuppressWarnings("unchecked")
    public ConcurrentHashMap(int initialCapacity,
                             float loadFactor, int concurrencyLevel) {
        if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
            throw new IllegalArgumentException();
        // segment 个数最多为 MAX_SEGMENTS
        if (concurrencyLevel > MAX_SEGMENTS)
            concurrencyLevel = MAX_SEGMENTS;
        // Find power-of-two sizes best matching arguments
        int sshift = 0;
        // ssize 表示 segment 数组的大小
        int ssize = 1;
        // ssize 大小和并发级别有关系，找到大于等于该并发级别的2的幂次方的数
        // 假设 concurrencyLevel为16，while循环
        // 1. 1<16  返回true ，sshift  = 1，ssize 左移一位 =2
        // 2. 2<16  返回true ，sshift  = 2，ssize 左移一位 =4
        // 3. 4<16  返回true ，sshift  = 3，ssize 左移一位 =8
        // 4. 8<16  返回true ，sshift  = 4，ssize 左移一位 =16
        // 5. 16<16 返回false

        // 所以 ssize  = 16 sshift 为 4
        while (ssize < concurrencyLevel) {
            ++sshift;
            ssize <<= 1;
        }
        this.segmentShift = 32 - sshift; // segmentShift = 28
        // 这个segmentMask 作用是为了 key.hashcode() & ssize -1 去计算当前key 应该存放在segment数组的哪一个位置
        this.segmentMask = ssize - 1; // segmentMask = 15
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        // c代表每一个segment对象中包含的HashEntry数组的长度
        int c = initialCapacity / ssize;
        if (c * ssize < initialCapacity)
            // c向上取整
            ++c;
        // 每一个segment中最少包含2个hashEntry ,MIN_SEGMENT_TABLE_CAPACITY 默认为2
        int cap = MIN_SEGMENT_TABLE_CAPACITY;
        while (cap < c)
            // 每一个 segment 下面的 hashEntry 的个数只能为 2的幂次方
            cap <<= 1;
        // create segments and segments[0]
        // segment 对象创建 segment[0]赋值

        // 扩容指的是segment下面的HashEntry个数的扩容，是分段扩容，而不是整个segment数组的扩容，只和当前segment对象有关系

        // segment[0]的创建，segment[0]的作用就是相当于一个原型，loadFactor, (int)(cap * loadFactor 属性的值的计算，不需要每次new segment对象的时候去计算这些值
        Segment<K,V> s0 =
            new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                             (HashEntry<K,V>[])new HashEntry[cap]);
        // segment数组的创建
        Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
        UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
        this.segments = ss;
    }
    
 // put操作
    
        public V put(K key, V value) {
        Segment<K,V> s;
        if (value == null)
            throw new NullPointerException();
        // key.hashcode()
        int hash = hash(key);
        // 计算 segment 数组index 的算法流程
        // key.hashcode() 右移 segmentShift位 （segmentShift 为 28）
        // 0100 0011 1101 0001 0101 0000 1101 0010  (hashcode)
        // 右移 28位
        // 0000 0000 0000 0000 0000 0000 0000 0100
        // &
        // 0000 0000 0000 0000 0000 0000 0001 1111
        // 0000 0000 0000 0000 0000 0000 0000 0100 (所要插入的元素在segment数组中的index)
        int j = (hash >>> segmentShift) & segmentMask;
        if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
             (segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
            s = ensureSegment(j);
        return s.put(key, hash, value, false);
    }
    // 多线程并发执行插入操作，使用 DCL & CAS操作保证只有一个线程能够或得资源
      private Segment<K,V> ensureSegment(int k) {
        //  DCL & CAS  保证只有一个线程可以对segment对象赋值
        final Segment<K,V>[] ss = this.segments;
        long u = (k << SSHIFT) + SBASE; // raw offset
        Segment<K,V> seg;
        if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
            Segment<K,V> proto = ss[0]; // use segment 0 as prototype
            // 用segment[0] 进行 put操作
            int cap = proto.table.length;
            float lf = proto.loadFactor;
            int threshold = (int)(cap * lf);
            HashEntry<K,V>[] tab = (HashEntry<K,V>[])new HashEntry[cap];
            if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u))
                == null) { // recheck
                Segment<K,V> s = new Segment<K,V>(lf, threshold, tab);
                while ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u))
                       == null) {
                    if (UNSAFE.compareAndSwapObject(ss, u, null, seg = s))
                        break;
                }
            }
        }
        return seg;
    }
```



```java
 


static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root,
                                                    TreeNode<K,V> x) {
     		// 新插入的节点为红色
            x.red = true;
     		// xp表示x的父节点，xpp表示x的祖父节点，xppl表示xpp的左孩子节点，xppr表示xpp的右孩子节点
            for (TreeNode<K,V> xp, xpp, xppl, xppr;;) {
                // 插入的节点是红黑树的第一个节点
                if ((xp = x.parent) == null) {
                    x.red = false;
                    // 返回红黑树的根节点
                    return x;
                }
                else if (!xp.red || (xpp = xp.parent) == null)
                    return root;
                if (xp == (xppl = xpp.left)) {
                    if ((xppr = xpp.right) != null && xppr.red) {
                        xppr.red = false;
                        xp.red = false;
                        xpp.red = true;
                        x = xpp;
                    }
                    else {
                        if (x == xp.right) {
                            root = rotateLeft(root, x = xp);
                            xpp = (xp = x.parent) == null ? null : xp.parent;
                        }
                        if (xp != null) {
                            xp.red = false;
                            if (xpp != null) {
                                xpp.red = true;
                                root = rotateRight(root, xpp);
                            }
                        }
                    }
                }
                else {
                    if (xppl != null && xppl.red) {
                        xppl.red = false;
                        xp.red = false;
                        xpp.red = true;
                        x = xpp;
                    }
                    else {
                        if (x == xp.left) {
                            root = rotateRight(root, x = xp);
                            xpp = (xp = x.parent) == null ? null : xp.parent;
                        }
                        if (xp != null) {
                            xp.red = false;
                            if (xpp != null) {
                                xpp.red = true;
                                root = rotateLeft(root, xpp);
                            }
                        }
                    }
                }
            }
        }
```



hashMap  某一个节点上的链表的长度 > 8 ,则将链表变成红黑树（数组长度大于64才会成立），jdk1.8 链表的插入采用的尾插法，jdk1.7采用头插法。



# 哈希表Hash





# 递归阶乘

```java
static int factorial(int n) { // 基本条件1 
    if (n <= 1){ //2 
        return 1; 
    } else { // 递归步，执行n-1次3 
        return n * factorial(n - 1); 
    } 
}

factorial(6) = 720
```