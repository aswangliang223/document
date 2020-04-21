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