# 重写java finalize的方法的坑(OOM内存溢出)

```
如果发现重写了finalize()方法，就会触发调用回收，如果逻辑过长，处理时间满，新的对象不断进来，老的对象回收慢，就造成了OOM
```

![image-20200922193915180](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922193915180.png)

# java中的四种引用方式

1.强引用

![image-20200922194125707](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922194125707.png)

2.软引用

![image-20200922194146797](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922194146797.png)

```
调用垃圾回收器不会回收，只有当heap装不下时，才会把软引用干掉
```

3.弱引用

**ThreadLocal为什么要使用弱引用？**

![image-20200922214437197](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922214437197.png)

![image-20200922215320607](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922215320607.png)

4.虚引用

![image-20200922195501348](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922195501348.png)

![image-20200922195430007](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200922195430007.png)

```
被回收时，先被扔到引用队列中，垃圾回收线程会不断检查该队列，当发现队列里有了该对象，就会真正的回收它
```

# 红黑树

**红黑树，对于每个节点，从该节点到它子孙节点的所有路径上包含相同的黑节点**

**所有插入的点默认为红色**

![image-20201008151457287](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201008151457287.png)

![image-20201008153048927](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201008153048927.png)

```
变色
变色指的是结点的颜色由红变黑或由黑变红

变换规则
        将左旋、右旋和变色结合起来，得到一套变换规则：
        变色：如果当前结点的父结点和叔父结点是红色，那么：
            把父结点和叔父结点变为黑色
            把祖父结点变为红色
            把指针定义到祖父结点
        
左旋：当前结点是右子树，且父结点是红色，叔父结点是黑色，对它的父结点左旋
右旋：当前结点是左子树，且父结点是红色，叔父结点是黑色，那么：
            把父结点变为黑色
            把祖父结点变为红色
            对祖父结点右旋
```

**左旋是以父节点进行左旋的**

**右旋是以祖父节点进行右旋的**

![ ](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201008153140678.png)

![image-20201008153217923](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201008153217923.png)

```
package com.cbx;

import sun.security.krb5.internal.PAData;

/**
 * @Author:cbx
 * @Date:2020/11/02/13:19
 */
public class RBtree<K extends Comparable<K>,V> {
      private static final boolean RED=true;
      private static final boolean BLACK=false;
      //树根的引用
      private RBNode root;

    public RBNode getRoot() {
        return root;     
    }

    /**
     * 获取父节店点
     * @param <>
     * @param <>
     */
    private RBNode parentOf(RBNode node){
         if (node!=null){
             return node.parent;
         }
         return null;
    }

    /**
     * 判断节点是否为红色
     * @param <>
     * @param <>
     */

    private Boolean isRed(RBNode node){
        if (node!=null){
            return node.color==RBtree.RED;
        }
        return null;
    }

    /**
     * 判断节点是否为黑色
     * @param <>
     * @param <>
     */

    private Boolean isBlack(RBNode node){
        if (node!=null){
            return node.color==RBtree.BLACK;
        }
        return null;
    }

    /**
     * 设置当前节点为红色
     * @param <>
     * @param <>
     */
    private void setRed(RBNode node){
        if (node!=null){
            node.color=RED;
        }
    }

    /**
     * 设置当前节点为黑色
     * @param <>
     * @param <>
     */
    private void setBlack(RBNode node){
        if (node!=null){
            node.color=BLACK;
        }
    }


    public void inorderPrint(){
        inorderPrint(this.root);
    }
    /**
     * inorder
     * @param <>
     * @param <>
     */
    private void inorderPrint(RBNode node){
         if (node!=null){
             inorderPrint(node.left);
             System.out.println("key:"+node.key+"value:"+node.value);
             inorderPrint(node.right);
         }
    }

    /**
     * 左旋方法
     * 当前节点为x , 右孩子 y ，左孩子 z
     * 1.将y的左孩子放在x的右孩子位置
     * 2.当x的父节点不为空时，更新y的父节点为x的父节点，并将x的父节点指定子树(x可能在x父节点的左/右子树)为y
     * 3.将x的父节点更新为y,将y的左子节点更新为x
     * @param <>
     * @param <>
     */

    private void leftRotate(RBNode x){
         RBNode y=x.right;
         //将x的右子节点指向y的左子节点，并y的左子节点的父节点更新为x
         x.right=y.left;
         if (y.left!=null){
             y.left.parent=x;
         }

        //     * 2.当x的父节点不为空时，更新y的父节点为x的父节点，并将x的父节点指定子树(x可能在x父节点的左/右子树)为y
         if (x.parent!=null){
             y.parent=x.parent;
             if (x==x.parent.left){
                 x.parent.left=y;
             }else {
                 x.parent.right=y;
             }
         }else {
             //x为根节点 ，则更新y为根节点
             this.root=y;
         }
//      3.将x的父节点更新为y,将y的左子节点更新为x
         x.parent=y;
         y.left=x;

    }

    /**
     * 右旋
     * 1.更新y的左子节点指向x的右子节点，并且更新x的右子节点的父节点为y
     * 2.当y的父节点不为空时，更新x的父节点为y的父节点，更新y指定位置的子节点为y(可能左/右)
     * 3.设置x的右子节点为y，y的父节点为x
     * @param y
     */
    private void rightRotate(RBNode y){
       RBNode x=y.left;
       y.left=x.right;
       if (x.right!=null){
           x.right.parent=y;
       }

       if (y.parent!=null){
           x.parent=y.parent;
           if (y==y.parent.left){
               y.parent.left=x;
           }else {
               y.parent.right=x;
           }
       }else {
           //y为根节点的情况
           this.root=x;
       }

       y.parent=x;
       x.right=y;
    }

    /**
     * 定义插入方法
     * @param 
     * @param 
     */
    public void insert(K key,V value){
        RBNode node=new RBNode();
        node.setKey(key);
        node.setValue(value);
        //插入的节点一定是红色的
        node.setColor(RED);
        insert(node);
    }

    private void insert(RBNode node) {
          //定位到当前node的父节点
        RBNode parent=null;
        RBNode x=this.root;
        while (x!=null){
               parent=x;
               int cmp=node.key.compareTo(x.key);
               if (cmp>0){
                   x=x.right;
               }else if(cmp==0){
                   //相等的情况就去覆盖该节点的值
                   x.setValue(node.getValue());
                   return;
               }else {
                   x=x.left;
               }
        }
        node.parent=parent;
        if (parent!=null){
            int cmp=node.key.compareTo(parent.key);
            if (cmp>0){
                parent.right=node;
            }else {
                parent.left=node;
            }
        }else {
            this.root=node;
        }
        
        //修复红黑树的平衡
        insertFixUp(node);

    }

    /**
     * 1.如果红黑树为空树，就将当前节点染为黑色插入
     * 2.插入节点的key已经存在 ，(上面插入方法中已经进行return)
     * 3.插入节点的父节点为黑色 ,不需要变化
     * 4.插入节点的父节点为红色
     *    4.1叔叔节点存在并且为红色，将叔叔节点染为黑色，父节点染为黑色，将祖父节点染为红色，祖父节点为当前节点，下一轮处理
     *    4.2叔叔节点不存在或者为黑色，父节点为祖父节点的左子树
     *      4.2.1 插入节点为父节点的左子树，(LL情况)，先将父节点为黑色，爷爷染红色，以爷爷节点右旋
     *      4.2.2 插入节点为父节点的右子树，(LR情况)，以父节点进行左旋，父节点为当前节点进行下一轮
     *    4.3叔叔节点不存在或者为黑色，父节点为祖父节点的右子树
     *       4.3.1 插入节点为父节点的右子树，(RR情况)，先将父节点为黑色，爷爷染红色，以爷爷节点左旋
     *       4.3.2 插入节点为父节点的左子树，(RL情况)，以父节点进行右旋，父节点为当前节点进行下一轮
     */
    private void insertFixUp(RBNode node) {
        //情况1 2 3
           this.root.setColor(BLACK);
           RBNode parent=parentOf(node);
           RBNode gparent=parentOf(parent);
           if (parent!=null&&isRed(parent)){
               //父节点是红色,并且是祖父节点的左子树
               RBNode uncle=null;

               if (parent==gparent.left){
                   uncle=gparent.right;
                   //叔叔节点存在，并且是红色
                   if (uncle!=null&&isRed(uncle)){
                       //uncle 也是红色
                       setBlack(parent);
                       setBlack(uncle);
                       setRed(gparent);
                       insertFixUp(gparent);
                       return;
                   }
                   //叔叔节点不存在，或者是黑色
                   if (uncle==null||isBlack(uncle)){
                      //LL情况
                       if (node==parent.left){
                           setBlack(parent);
                           setRed(gparent);
                           //以祖父节点进行右旋
                           rightRotate(gparent);
                           return;
                       }
                       //LR情况
                       if (node==parent.right){
                           leftRotate(parent);
                           insertFixUp(parent);
                           return;
                       }
                   }
               }else {
                   uncle=gparent.left;
                   //父节点是红色,并且是祖父节点的右子树
                   //叔叔节点存在，并且是红色
                   if (uncle!=null&&isRed(uncle)){
                       //uncle 也是红色
                       setBlack(parent);
                       setBlack(uncle);
                       setRed(gparent);
                       insertFixUp(gparent);
                       return;
                   }
                   //叔叔节点不存在，或者是黑色
                   if (uncle==null||isBlack(uncle)){
                       //RL情况
                       if (node==parent.left){
                           leftRotate(parent);
                           insertFixUp(parent);
                           return;
                       }
                       //RR情况
                       if (node==parent.right){
                           setBlack(parent);
                           setRed(gparent);
                           leftRotate(gparent);
                           return;
                       }
                   }

               }

           }

    }

    //定义红黑树节点
    static class RBNode<K extends Comparable<K>,V>{
        private RBNode parent;
        private RBNode left;
        private RBNode right;
        private boolean color;
        private K key;
        private V value;

          public RBNode() {

          }

          public RBNode(RBNode parent, RBNode left, RBNode right, boolean color, K key, V value) {
              this.parent = parent;
              this.left = left;
              this.right = right;
              this.color = color;
              this.key = key;
              this.value = value;
          }

          public RBNode getParent() {
              return parent;
          }

          public void setParent(RBNode parent) {
              this.parent = parent;
          }

          public RBNode getLeft() {
              return left;
          }

          public void setLeft(RBNode left) {
              this.left = left;
          }

          public RBNode getRight() {
              return right;
          }

          public void setRight(RBNode right) {
              this.right = right;
          }

          public boolean isColor() {
              return color;
          }

          public void setColor(boolean color) {
              this.color = color;
          }

          public K getKey() {
              return key;
          }

          public void setKey(K key) {
              this.key = key;
          }

          public V getValue() {
              return value;
          }

          public void setValue(V value) {
              this.value = value;
          }
      }
}

```



# Spring源码

![image-20201022162731772](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201022162731772.png)

# AQS

```
1.自旋
2.park unpark
3.CAS
4.如果是交替执行的线程(就是没有竞争的情况下)，不需要用到队列，可以在jdk级别处理问题，而sync块是要在os层面上解决问题的。
```

```
Node类的设计{
   最重要的三个元素
   volatile Node prev;
   volatile Node next;
   volatile Thread thread;
   volatile int waitStatus； #当前线程的状态
}
```

```
队列里面的队头结点Node中Thread为空

入队的时候会把上一个的waitstatus改为-1 代表休眠 （对于解锁非常重要）
为什么自己不能改自己的状态为休眠？
  就好比，你睡着的时候你不知道你已经睡着了，只有别人才能看到你其实已经睡着了
  
```

```
 //判断自己是否需要排队的方法
   1.队列已经没有初始化 (h==t代表队列没有初始化，返回h!=t为false，后面代码不需要执行)，后面直接CAS加锁
   2.队列已经被初始化
      2.1队列中元素的个数大于1(因为有一个虚拟节点)
      2.2队列中元素的个数等于1
  
  public final boolean hasQueuedPredecessors() {
        // The correctness of this depends on head being initialized
        // before tail and on head.next being accurate if the current
        // thread is first in queue.
        Node t = tail; // Read fields in reverse initialization order
        Node h = head;
        Node s;
        return h != t &&
            ((s = h.next) == null || s.thread != Thread.currentThread());
    }
```



# 1.8 concurrentHashMap

1.构造方法

**使用不带参的构造函数，默认初始容量是16**

```
public ConcurrentHashMap(int initialCapacity) {
//跟1.7不同，这里如果是传入的32,那么容量就会扩大到64 ，1.7就是返回的32
        if (initialCapacity < 0)
            throw new IllegalArgumentException();
        int cap = ((initialCapacity >= (MAXIMUM_CAPACITY >>> 1)) ?
                   MAXIMUM_CAPACITY :
                   tableSizeFor(initialCapacity + (initialCapacity >>> 1) + 1));
        this.sizeCtl = cap;
    }
```

2.sizeCtl的含义

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201105202840672.png)

```
 private final Node<K,V>[] initTable() {
        Node<K,V>[] tab; int sc;
        while ((tab = table) == null || tab.length == 0) {
            if ((sc = sizeCtl) < 0)
                Thread.yield(); // lost initialization race; just spin
            else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
                try {
                    if ((tab = table) == null || tab.length == 0) {
                        int n = (sc > 0) ? sc : DEFAULT_CAPACITY; 
                        @SuppressWarnings("unchecked")
                        Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                        table = tab = nt;
                        sc = n - (n >>> 2);  //初始化好记录的是数组容量*0.75
                    }
                } finally {
                    sizeCtl = sc;
                }
                break;
            }
        }
        return tab;
    }
```

