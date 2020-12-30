# 实际问题

## 1.

![image-20200709194342806](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709194342806.png)

## 2.

![image-20200709194514178](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709194514178.png)

## 3.

![image-20200709194606336](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709194606336.png)

# 稀疏数组

## 实际需求

![image-20200709195943982](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709195943982.png)

## 基本介绍

<img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709200059948.png" alt="image-20200709200059948" style="zoom:50%;" />

<img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709200447859.png" alt="image-20200709200447859" style="zoom:50%;" />

## 稀疏数组的转换

![image-20200709201405893](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200709201405893.png)

## 代码实现

```
package com.cbx;

import java.io.*;

public class SparseArray {
    public static void main(String[] args) throws IOException {
//        创建一个原始的二维数组 11*11
        int checkArr1[][]=new int[11][11];
        checkArr1[1][2]=1;
        checkArr1[2][3]=2;
//        输出原始的数组
        System.out.println("原始的二维数组");
        for (int[] row : checkArr1) {
            for (int data : row) {
                System.out.printf("%d\t",data);
            }
            System.out.println("\n");
        }
        /**
         * 将二维数组 转换成稀疏数组
         * 1.先遍历二维数组得到非0的值
         */
        int sum=0;
        for (int i=0;i<checkArr1.length;i++){
            for (int j = 0; j < checkArr1.length; j++) {
                if(checkArr1[i][j]!=0){
                    sum++;
                }
            }
        }
        System.out.println(sum);
        /**
         * 创建对应的稀疏数组
         */
        int sparseArr[][]=new int[sum+1][3];
//        给稀疏数组赋值
        sparseArr[0][0]=11;
        sparseArr[0][1]=11;
        sparseArr[0][2]=sum;
//        遍历二维数组，将非0的值存放在sparseArr中
        int count=0;
        for (int i = 0; i < 11; i++) {
            for (int j = 0; j < 11; j++) {
                if(checkArr1[i][j]!=0){
                    count++;
                    sparseArr[count][0]=i;
                    sparseArr[count][1]=j;
                    sparseArr[count][2]=checkArr1[i][j];
                }
            }
        }
        /**
         * 输出稀疏数组
         */
        System.out.println("输出稀疏数组");
        for (int i = 0; i < sparseArr.length; i++) {
            for (int j = 0; j < 3; j++) {
                System.out.printf("%d\t",sparseArr[i][j]);
            }
            System.out.println("\n");
        }
        /**
         * 稀疏数组恢复成二维数组
         * 1、先读取稀疏数组的第一行，根据第一行数据，创建原始的二维数组
         */
        int checkArr2[][]=new int[sparseArr[0][0]][sparseArr[0][1]];
//        恢复后的二维数组
        for (int i = 1; i < sparseArr.length; i++) {
            checkArr2[sparseArr[i][0]][sparseArr[i][1]]=sparseArr[i][2];
        }
        System.out.println("恢复后的二维数组");
        for (int[] ints : checkArr2) {
            for (int data:ints){
                System.out.printf("%d\t",data);
            }
            System.out.println();
        }

        /**
         * 存储到磁盘
         */
        File file=new File("d:\\data.txt");
        OutputStream outputStream=new FileOutputStream(file,true);
        for (int[] ints : sparseArr) {
            for (int data:ints){
//                System.out.printf("%d\t",data);
                outputStream.write(String.valueOf(data).getBytes());
                outputStream.write("\t".getBytes());
            }
            String huanhang = System.getProperty("line.separator");
            outputStream.write(huanhang.getBytes());
//            System.out.println();
        }
        outputStream.flush();
        outputStream.close();
/**
 * 读出数组
 */
        System.out.println("开始读出数组++++++++++");
int[][] sparseArr2=new int[sparseArr[0][0]][sparseArr[0][1]];
BufferedReader reader=new BufferedReader(new FileReader(file));
String line;
int row=0;
while ((line=reader.readLine())!=null){
    String[] temp=line.split("\t");
    for (int i = 0; i < temp.length; i++) {
         sparseArr2[row][i]=Integer.parseInt(temp[i]);
    }
    row++;

}
reader.close();

    }




}


```

# 队列

## 数组模拟队列代码(存在假溢出问题)

```
package com.cbx.queue;

import java.util.Scanner;

/**
 * 用数组模拟队列
 */
public class ArrayQueueDemo {
    public static void main(String[] args) {
     ArrayQueue queue=new ArrayQueue(10);
     char key=' ';//接收用户输入
        Scanner scanner=new Scanner(System.in);
        boolean loop=true;
        while(loop){
            System.out.println("s(show):显示队列");
            System.out.println("e(exit):退出程序");
            System.out.println("a(add):添加数据");
            System.out.println("g(get):取出数据");
            System.out.println("h(head):查看队列头元素");
            key=scanner.next().charAt(0); //接收一个字符
            switch (key){
                case 's':
                    queue.showQueue();
                    break;
                case 'a':
                    System.out.println("输入一个数");
                    int value=scanner.nextInt();
                    queue.addQueue(value);
                    break;
                case 'g':
                    try{
                        System.out.println("取出的数据是"+queue.getQueue());
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try{
                        int res = queue.headQueue();
                        System.out.println("队列头数据"+res);
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'e':
                    scanner.close();
                    loop=false;
                    break;
                    default:
                        break;

            }
        }
        System.out.println("程序退出了");

    }
}

class ArrayQueue{
    private int maxSize; //数组的最大容量
    private int front; //队列头
    private int rear; //队列尾
    private int[] arr; //数据用于存放数据，模拟队列

//    初始化队列
    public ArrayQueue(int arrMaxSize){
        maxSize=arrMaxSize;
        arr=new int[maxSize];
        //front 指向队头的前一个位置，rear只向队列的尾部(要么就是front指向对头，rear指向队尾的后一个位置)
        front=rear=-1;

    }
//    判断队列是否满
    public boolean isFull(){
        return rear==maxSize-1;
    }
//    判断队列是否为空
    public boolean isEmpty(){
        return front==rear;
    }
//    添加数据到队列
    public void addQueue(int n ){
//        判断队列是否满
        if (isFull()){
            System.out.println("队列已经满了");
            return;
        }
        rear++;  //rear后移
        arr[rear]=n;
    }
//    获取队列数据，出队列
    public int getQueue(){
        if (isEmpty()){
            throw new RuntimeException("队列为空，不能取数据");
        }
       front++; //front指向元素前一个位置，所以先移动指针
        return arr[front];
    }
//    显示队列的所有数据
    public void showQueue(){
        if (isEmpty()){
            System.out.println("队列为空");
            return;
        }
        for (int i = 0; i < arr.length; i++) {
            System.out.printf("arr[%d]=%d\n",i,arr[i]);
        }
    }
//    显示队列的头数据，不是取出数据
    public int headQueue(){
        if (isEmpty()){
            throw new RuntimeException("队列空的，没有数据");
        }
        return arr[front+1]; //只是显示，不需要移动指针
    }
}
```

## 数组模拟环形队列

**分析**

![image-20200713211051696](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200713211051696.png)

```
package com.cbx.queue;

import java.util.Scanner;

public class CircleArrayDemoQueue {
    public static void main(String[] args) {
        CircleArray queue=new CircleArray(3);
        char key=' ';//接收用户输入
        Scanner scanner=new Scanner(System.in);
        boolean loop=true;
        while(loop){
            System.out.println("s(show):显示队列");
            System.out.println("e(exit):退出程序");
            System.out.println("a(add):添加数据");
            System.out.println("g(get):取出数据");
            System.out.println("h(head):查看队列头元素");
            key=scanner.next().charAt(0); //接收一个字符
            switch (key){
                case 's':
                    queue.showQueue();
                    break;
                case 'a':
                    System.out.println("输入一个数");
                    int value=scanner.nextInt();
                    queue.addQueue(value);
                    break;
                case 'g':
                    try{
                        System.out.println("取出的数据是"+queue.getQueue());
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h':
                    try{
                        int res = queue.headQueue();
                        System.out.println("队列头数据"+res);
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'e':
                    scanner.close();
                    loop=false;
                    break;
                default:
                    break;

            }
        }
        System.out.println("程序退出了");
    }

}
class CircleArray{
    private int maxSize; //数组的最大容量
    private int front; //队列头
    private int rear; //队列尾
    private int[] arr; //数据用于存放数据，模拟队列
    public CircleArray(int arrMaxSize){
//        front 和rear默认就是0
        maxSize=arrMaxSize;
        arr=new int[maxSize];
    }
    //    判断队列是否满
    public boolean isFull(){
        return (rear+1)%maxSize==front;
    }
    //    判断队列是否为空
    public boolean isEmpty(){
        return front==rear;
    }
    //    添加数据到队列
    public void addQueue(int n ){
//        判断队列是否满
        if (isFull()){
            System.out.println("队列已经满了");
            return;
        }
//      直接将数据加入
        arr[rear]=n;
//        将rear后移
        rear=(rear+1)%maxSize;
    }

    //    获取队列数据，出队列
    public int getQueue(){
        if (isEmpty()){
            throw new RuntimeException("队列为空，不能取数据");
        }
       int temp=arr[front];
        front=(front+1)%maxSize;
        return temp;
    }
    //    显示队列的所有数据
    public void showQueue(){
        if (isEmpty()){
            System.out.println("队列为空");
            return;
        }
//        从front开始遍历 ，遍历(rear-front+maxSize)%maxSize个
       for(int i=front;i<front+(rear-front+maxSize)%maxSize;i++){
//            会越界，自己带个图分析
           System.out.printf("arr[%d]=%d\n",i%maxSize,arr[i%maxSize]);
       }
    }
    //    显示队列的头数据，不是取出数据
    public int headQueue(){
        if (isEmpty()){
            throw new RuntimeException("队列空的，没有数据");
        }
        return arr[front]; //只是显示，不需要移动指针
    }
}
```

# 单链表

## 创建和遍历实现(默认插到链表尾部)

```
package com.cbx.linkedList;

public class SingleLinkedListDemo {
    public static void main(String[] args) {
       SingleLinkedList list=new SingleLinkedList();
       list.add(new HeroNode(1,"宋江","及时雨"));
       list.add(new HeroNode(2,"卢俊义","玉麒麟"));
       list.add(new HeroNode(3,"吴用","智多星"));
       list.list();
    }
}
/**
 * 定义链表
 */
class SingleLinkedList{
//    初始化一个头结点 ，不允许操作
    private HeroNode head=new HeroNode(0,"","");
/**
 *   不考虑编号的操作
 *   插入节点
  */

     public void add(HeroNode heroNode){
//         头结点不能动，需要一个辅助节点
         HeroNode temp=head;
          while(true){
              if (temp.next==null){
                  break;
              }
              temp=temp.next;
          }
//          退出while循环，保证temp指向了链表的最后
         temp.next=heroNode;

     }
    /**
     * 显示链表的操作
     */
    public void list(){
        if (head.next==null){
            System.out.println("链表为空");
            return;
        }
//        需要一个辅助节点来遍历
        HeroNode temp=head.next;
        while (true){
            if (temp==null){
//                遍历到了最后
                break;
            }
//            输出节点信息
            System.out.println(temp);
            temp=temp.next;
        }
    }
}

/**
 * 定义节点
 */
class HeroNode{
    public int no;
    public String name;
    public String nickname;
    public HeroNode next;
    public HeroNode(int no,String name,String nickname){
        this.no=no;
        this.name=name;
        this.nickname=nickname;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                ", nickname='" + nickname + '\'' +
                '}';
    }
}

```

## 根据排名插入节点的链表

```
  /**
     * 根据编号来插入节点
     */
    public void addByOrder(HeroNode heroNode){
         HeroNode temp=head;
//         如果节点编号已经存在，不能添加
        boolean flag=false; //表示不存在
        while (true){
            if (temp.next==null){
//                已经到达尾部
                break;
            }
            if(temp.next.no>heroNode.no){
                heroNode.next=temp.next;
                temp.next=heroNode;
                break;
            }else if(temp.next.no==heroNode.no){
//                说明添加的编号已经存在
                flag=true;
                break;
            }
            temp=temp.next;
        }
        if(flag){
            System.out.println("插入的英雄编号"+heroNode.no+"已经存在");

        }else {
            temp.next=heroNode;
        }
    }
}
```

## 修改节点

```
 /**
     * 修改节点
     */
    public void update(HeroNode newHeroNode){
       if (head.next==null){
           System.out.println("链表为空");
           return;
       }
       HeroNode temp=head.next;
       boolean flag=false; //表示是否找到该节点
        while (true){
            if (temp==null){
                break;//链表已经遍历结束
            }
            if (temp.no==newHeroNode.no){
                flag=true;
                break;
            }
            temp=temp.next;
        }
        if (flag){
            temp.name=newHeroNode.name;
            temp.nickname=newHeroNode.nickname;
        }else {
            System.out.println("没有找到该节点");
        }

    }
```

## 删除节点

```
 /**
     * 删除节点
     */
    public void delete(int no){
        HeroNode temp=head;
        while(true){
             if(temp.next==null){
                 System.out.println("没有该节点");
                 return;
             }
             if(temp.next.no==no){
                 temp.next=temp.next.next;
                 return;
             }
             temp=temp.next;
        }
    }
```

## 单链表面试题

### 1.求单链表有效节点的个数

```
/**
     * 获取单链表节点的个数
     */
    public static int getLength(HeroNode head){
//        头结点不算
        if (head.next==null){
            return 0;
        }
        int length=0;
        HeroNode current=head.next;
        while (current!=null){
           length++;
           current=current.next;
        }
        return length;
    }
}
```

### 2.查询单链表中的倒数第k个元素(新浪)

```
/**
     * 新浪面试题
     * 查询单链表的倒数第k个节点
     * 遍历(size-k)后，就可以得到
     */
    public static HeroNode findLastIndexNode(HeroNode head,int index){
        if (head.next==null){
            return null;
        }
        int size=getLength(head);
        if(index<=0||index>size){
            return null;
        }
        HeroNode temp=head.next;
        for (int i=0;i<size-index;i++){
             temp=temp.next;
        }
        return temp;

    }
```

### 3.单链表的反转(腾讯)

#### 方式一(以前数据结构用的方法)

```
    /**
     * 单链表的反转(Tecent)
     * 腾讯面试题
     */
    public static void reverse(HeroNode head){
            if(head.next==null){
                System.out.println("链表为空");
                return ;
            }
             if (head.next.next==null){
                System.out.println("只有一个节点无需反转");
                return;
            }
            HeroNode temp=head.next;
            while(temp.next!=null){
               temp=temp.next;
            }
//            找到尾节点
           HeroNode last=temp;
            while(head.next!=last){
                HeroNode p=head.next;
                head.next=p.next;
                p.next=last.next;
                last.next=p;
            }
    }
```

#### 方式二(借助新的链表)

![image-20200717215334975](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200717215334975.png)

```
  /**
     * 反转 方式2 借助一个新的链表
     */
    public static void reverse2(HeroNode head){
        if(head.next==null){
            System.out.println("链表为空");
            return ;
        }
        if (head.next.next==null){
            System.out.println("只有一个节点无需反转");
            return;
        }
        HeroNode newNode=new HeroNode(0,"","");
        HeroNode temp=head.next;
        HeroNode p=null;
        while(temp!=null){
//            头插法插入
            p=temp.next;
                 temp.next=newNode.next;
                 newNode.next=temp;
                 temp=p;
        }
        head.next=newNode.next;
    }
```

### 4.逆序打印单链表(百度)

**要求不改变表本身的结构**

```
 /**
     * 逆序打印栈
     * 利用栈
     */
    public static void reversePrint(HeroNode head){
         if(head.next==null){
             //空链表
             return;
         }
        Stack<HeroNode> stack=new Stack<>();
         HeroNode cur=head.next;
         while (cur!=null){
             stack.push(cur);
             cur=cur.next;
         }
         while (!stack.isEmpty()){
             System.out.println(stack.pop());
         }
    }

```

# 双链表

```
package com.cbx.linkedList;

public class DoubleLinkedListDemo {
    public static void main(String[] args) {
        DoubleLinkedList list=new DoubleLinkedList();
        HeroNode2 hero1=new HeroNode2(1,"aa","aaa");
        HeroNode2 hero2=new HeroNode2(2,"bb","bbb");
        HeroNode2 hero3=new HeroNode2(3,"cc","ccc");
        HeroNode2 hero4=new HeroNode2(4,"dd","ddd");
//        list.add(hero1);
//        list.add(hero2);
//        list.add(hero3);
//        list.add(hero4);
        list.addByOrder(hero4);
        list.addByOrder(hero3);
        list.addByOrder(hero1);
        list.addByOrder(hero2);
        list.list();
//        list.delete(4);
//        list.list();
    }

}
class DoubleLinkedList{
//   初始化头结点
    private HeroNode2 head=new HeroNode2(0,"","");

    public HeroNode2 getHead() {
        return head;
    }
    /**
     * 显示链表的操作
     */
    public void list(){
        if (head.next==null){
            System.out.println("链表为空");
            return;
        }
//        需要一个辅助节点来遍历
        HeroNode2 temp=head.next;
        while (true){
            if (temp==null){
//                遍历到了最后
                break;
            }
//            输出节点信息
            System.out.println(temp);
            temp=temp.next;
        }
    }

    /**
     * 插入节点
     * @param heroNode
     */
    public void add(HeroNode2 heroNode){
//         头结点不能动，需要一个辅助节点
        HeroNode2 temp=head;
        while(true){
            if (temp.next==null){
                break;
            }
            temp=temp.next;
        }
//          退出while循环，保证temp指向了链表的最后
        temp.next=heroNode;
        heroNode.pre=temp;

    }
    /**
     * 修改节点
     */
    public void update(HeroNode2 newHeroNode){
        if (head.next==null){
            System.out.println("链表为空");
            return;
        }
        HeroNode2 temp=head.next;
        boolean flag=false; //表示是否找到该节点
        while (true){
            if (temp==null){
                break;//链表已经遍历结束
            }
            if (temp.no==newHeroNode.no){
                flag=true;
                break;
            }
            temp=temp.next;
        }
        if (flag){
            temp.name=newHeroNode.name;
            temp.nickname=newHeroNode.nickname;
        }else {
            System.out.println("没有找到该节点");
        }

    }
    /**
     * 删除节点
     */

    public void delete(int no){
        HeroNode2 temp=head;
        while(true){
            if(temp==null){
                System.out.println("没有该节点");
                return;
            }
            if(temp.no==no){
//                如果是最后一个节点会有问题了
                temp.pre.next=temp.next;
                if(temp.next!=null){
                    temp.next.pre=temp.pre;
                }
                return;
            }
            temp=temp.next;
        }
    }
    /**
     * 根据编号来插入节点
     */
    public void addByOrder(HeroNode2 heroNode) {
        HeroNode2 temp=head;
       while(true){
           if (temp.next==null){
               temp.next=heroNode;
               heroNode.pre=temp;
               return;
           }
           if(temp.next.no>heroNode.no){
//               插入节点
               heroNode.next=temp.next;
               temp.next.pre=heroNode;
               temp.next=heroNode;
               heroNode.pre=temp;
               return;
           }
           temp=temp.next;
       }
    }

}
/**
 * 定义节点
 */
class HeroNode2{
    public int no;
    public String name;
    public String nickname;
    public HeroNode2 next;
    public HeroNode2 pre;
    public HeroNode2(int no,String name,String nickname){
        this.no=no;
        this.name=name;
        this.nickname=nickname;
    }

    @Override
    public String toString() {
        return "HeroNode{" +
                "no=" + no +
                ", name='" + name + '\'' +
                ", nickname='" + nickname + '\'' +
                '}';
    }
}

```

# 单向环形链表(约瑟夫环)

![image-20200722162229614](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200722162229614.png)

![image-20200722215456986](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200722215456986.png)

```
package com.cbx.linkedList;

/**
 * 模拟小孩围圈报数的约瑟夫问题
 */
public class JosePh {
    public static void main(String[] args) {
        CirecleSingleLinkedList list = new CirecleSingleLinkedList();
        list.addBoy(5);
        list.showBoy();
        list.countBoy(1,2,5);


    }
}
//创建环形链表
class CirecleSingleLinkedList{
//    创建一个first节点
    private Boy first=null;
//添加节点,形成一个环形链表
    public void  addBoy(int num){
        if (num<1){
            System.out.println("传入编号不正确");
        }
        Boy curBoy=null;//辅助指针
        for (int i=1;i<=num;i++){
           Boy boy=new Boy(i);
//           第一个节点特殊
           if (i==1){
               first=boy;
               first.setNext(first);
               curBoy=first; //让curBoy指向第一个小孩
           }else {
               curBoy.setNext(boy);
               boy.setNext(first);
               curBoy=boy;//让curBoy指向该节点
           }


        }
    }
    /**
     * 遍历当前的环形链表
     */
    public void showBoy(){
        if (first==null){
            System.out.println("链表为空");
            return;
        }
        Boy curBoy=first;
        while (true){
            System.out.println(curBoy);
            if (curBoy.getNext()==first){
                 break;
            }
            curBoy=curBoy.getNext(); //后移
        }

    }
//    根据用户的输入，计算出小孩的出圈顺序
    /**
     *
     * @param startNo  表示从第几个小孩开始数数
     * @param countNum 表示数几下
     * @param nums  表示有多少小孩在圈中
     */
    public void countBoy(int startNo,int countNum,int nums){
       if (first==null||startNo<1||startNo>nums){
           System.out.println("参数有误");
           return;
       }
//       创建辅助指针helper,事先指向环形链表的最后(helper的之前一个节点)
        Boy helper=first;
       while (true){
           if (helper.getNext()==first){
               break;
           }
           helper=helper.getNext();
       }
//       报数前，先移动firstNo-1次，因为不是非要从第一个开始报数
        for (int i = 0; i < startNo - 1; i++) {
            first=first.getNext();
            helper=helper.getNext();
        }
        while (true){
            if (helper==first){
                //说明圈中只有一个人
                break;
            }
            for (int j=0;j<countNum-1;j++){
                //自身也要报数一次
                first=first.getNext();
                helper=helper.getNext();
            }
            //此时first指向的节点就是，要出圈的节点
            System.out.println("小孩"+first.getNo()+"出圈");
            first=first.getNext();
            helper.setNext(first);
        }
        System.out.println("最后留在圈中的小孩是"+first.getNo());


    }

}
//表示节点
class Boy{
    private int no;
    private Boy next;
    public Boy(int no){
        this.no=no;
    }

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    public Boy getNext() {
        return next;
    }

    public void setNext(Boy next) {
        this.next = next;
    }

    @Override
    public String toString() {
        return "Boy{" +
                "no=" + no +
                '}';
    }
}
```

# 栈

## 应用场景

![image-20200723212230151](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200723212230151.png)

## 栈计算表达式的结果(中缀)

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200723225742218.png)

```
package com.cbx.Stack;

import java.util.OptionalInt;

/**
 * 实现式的计算
 */
public class Calculator {
    public static void main(String[] args) {
        String expression="1000+3/6";
        //创建两个栈，一个数栈，一个符号栈
        ArrayStack2 numStack = new ArrayStack2(10);
        ArrayStack2 operStack = new ArrayStack2(10);
        int index=0; //用于扫描
        int num1=0;
        int num2=0;
        int oper=0;
        int res=0;
        char ch=' ';
        String keepNum="";
         while (true){
             //遍历每一个字符
             ch=expression.substring(index,index+1).charAt(0);
             if (operStack.isOper(ch)){
                //是运算符 ,判断当前的符号栈是否为空
                    if(!operStack.isEmpty()){
                        //符号栈不为空  (与栈顶元素比较一下)
                        if (operStack.priority(ch)<=operStack.priority(operStack.peek())){
                            num1=numStack.pop();
                            num2=numStack.pop();
                            oper=operStack.pop();
                            res=numStack.cal(num1,num2,oper);
                            numStack.push(res);
                            //把当前操作符入符号栈
                            operStack.push(ch);
                        }else {
                            //当前操作符的优先级大于栈顶的操作符优先级，直接入栈
                            operStack.push(ch);
                        }
                      }else {
                        //符号栈为空 ，直接入符号栈
                        operStack.push(ch);
                    }
                  } else {
                    //如果是数字  //'1'==>49 减去48就是原本的
//                          numStack.push(ch-48);
                         //处理多位数的问题
                         keepNum+=ch;
                    if(index==expression.length()-1){
                        //到达最后一位直接入栈
                        numStack.push(Integer.parseInt(keepNum));
                    }else {
                        //是操作符就不扫描，直接入栈
                        if (operStack.isOper(expression.substring(index + 1, index + 2).charAt(0))) {
                            numStack.push(Integer.parseInt(keepNum));
                            //一定要注意清空
                            keepNum="";
                        }
                        }
                    }
                         //index后移一位
                         index++;
                         if (index>=expression.length()){
                             break;
                         }
                  }
                while (true){
                    //如果符号栈为空，则得到最后的结果，不然就计算
                    if (operStack.isEmpty()){
                        break;
                    }
                    num1=numStack.pop();
                    num2=numStack.pop();
                    oper=operStack.pop();
                    res=numStack.cal(num1,num2,oper);
                    numStack.push(res);
                }
                System.out.printf("表达式%s=%d",expression,numStack.pop());

         }

    }

/**
 * 对之前的栈进一步加强
 */
class ArrayStack2{
    private int maxSize;
    private int[] stack;
    private int top=-1;
    public ArrayStack2(int maxSize){
        this.maxSize=maxSize;
        stack=new int[maxSize];
    }
    /**
     * 栈满
     */
    public boolean isFull(){
        return top==maxSize-1;
    }
    /**
     * 栈空
     */
    public boolean isEmpty(){
        return top==-1;
    }
    /**
     * 入栈
     */
    public void push(int value){
        if (isFull()){
            System.out.println("栈满");
            return;
        }
        top++;
        stack[top]=value;
    }
    /**
     * 出栈
     * 将栈顶的数据返回
     */
    public int pop(){
        if (isEmpty()){
            throw new RuntimeException("栈空");
        }
        int value=stack[top];
        top--;
        return value;
    }
    /**
     * 遍历栈，从栈顶往下遍历
     */
    public void list(){
        if (isEmpty()){
            System.out.println("没有数据");
            return;
        }
        for (int i=top;i>=0;i--){
            System.out.println("数据"+stack[i]);
        }

    }
    /**
     * 返回运算符的优先级
     */
    public int priority(int oper){
        if (oper=='*'||oper=='/'){
            return 1;
        }else if(oper=='+'||oper=='-'){
            return 0;
        }else {
            return -1;//暂定只有=-*/
        }
    }
    /**
     * 判断是不是一个运算符
     */
    public boolean isOper(char val){
        return val=='+'||val=='-'||val=='*'||val=='/';
    }
    /**
     * 计算方法
     */
    public int cal(int num1,int num2,int oper){
           int res=0;
           switch (oper){
               case '+':
                   res=num1+num2;
                   break;
               case '-':
                   //注意顺序
                   res=num2-num1;
                   break;
               case '*':
                   res=num1*num2;
                   break;
               case '/':
                   res=num2/num1;
                   break;
               default:
                   break;
           }
           return res;
    }
    /**
     * 增加一个方法，可以返回栈顶的值，但不是真正的pop
     */
    public int peek(){
        return stack[top];
    }

}

```

## 逆波兰计算器实现(后缀)

```
package com.cbx.Stack;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
    public static void main(String[] args) {
        //定义一个逆波兰表达式
        //(3+4)*5-6 =>34+5*6-
        String suffixExpression="3 4 + 5 * 6 -";
        List<String> string = getListString(suffixExpression);
        System.out.println(string);
        int calculate = calculate(string);
        System.out.println(calculate);
    }
    /**
     * 将逆波兰表达式，数据和运算符 放入ArrayList中
     */
    public static List<String> getListString(String suffixExperssion){
        String[] split = suffixExperssion.split(" ");
        List<String> list=new ArrayList<>();
        for (String s : split) {
            list.add(s);
        }
        return list;
    }
    /**
     * 完成后缀表达式的运算
     */
    public static int calculate(List<String> ls){
          //创建一个栈
        Stack<String> stack=new Stack<>();
        //遍历List
        for (String item : ls) {
            if (item.matches("\\d+")){
                //匹配多位数
                stack.push(item);
            }else {
                int num2=Integer.parseInt(stack.pop());
                int num1=Integer.parseInt(stack.pop());
                int res=0;
                if (item.equals("+")){
                    res=num1+num2;
                }else if(item.equals("-")){
                    res=num1-num2;
                }else if(item.equals("*")){
                     res=num1*num2;
                }else if(item.equals("/")){
                    res=num1/num2;
                }else {
                    throw new RuntimeException("运算符有误");
                }
                stack.push(res+"");
            }
        }
        return Integer.parseInt(stack.pop());
    }
}

```

## 中缀转后缀

![image-20200729125754579](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200729125754579.png)

```
package com.cbx.Stack;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PolandNotation {
    public static void main(String[] args) {
        //定义一个逆波兰表达式
        //(3+4)*5-6 =>34+5*6-
//        String suffixExpression="3 4 + 5 * 6 -";
//        List<String> string = getListString(suffixExpression);
//        System.out.println(string);
//        int calculate = calculate(string);
//        System.out.println(calculate);
        String expression="1+(20*3)-5";
        List<String> list = toInfixExpressionList(expression);
        List<String> list1 = parseSuffixExperssion(list);
        int calculate = calculate(list1);
        System.out.println(list);
        System.out.println(list1);
        System.out.println(calculate);
    }
    /**
     * 将逆波兰表达式，数据和运算符 放入ArrayList中
     */
    public static List<String> getListString(String suffixExperssion){
        String[] split = suffixExperssion.split(" ");
        List<String> list=new ArrayList<>();
        for (String s : split) {
            list.add(s);
        }
        return list;
    }
    /**
     * 完成后缀表达式的运算
     */
    public static int calculate(List<String> ls){
          //创建一个栈
        Stack<String> stack=new Stack<>();
        //遍历List
        for (String item : ls) {
            if (item.matches("\\d+")){
                //匹配多位数
                stack.push(item);
            }else {
                int num2=Integer.parseInt(stack.pop());
                int num1=Integer.parseInt(stack.pop());
                int res=0;
                if (item.equals("+")){
                    res=num1+num2;
                }else if(item.equals("-")){
                    res=num1-num2;
                }else if(item.equals("*")){
                     res=num1*num2;
                }else if(item.equals("/")){
                    res=num1/num2;
                }else {
                    throw new RuntimeException("运算符有误");
                }
                stack.push(res+"");
            }
        }
        return Integer.parseInt(stack.pop());
    }
    /**
     * 将中缀表达式转换成对应的list
     */
    public static List<String> toInfixExpressionList(String s){
          List<String> ls=new ArrayList<>();
          int i=0;//用来遍历中缀表达式字符串
          char c;
          while(i<s.length()){
              if ((c=s.charAt(i))<48||(c=s.charAt(i))>57){
                  //如果是非数字，直接加入
                  ls.add(""+c);
                  i++; //后移
              }else {
                  //如果是数，考虑多位数的情况
                  String str=""; //用来拼接多位数
                  while (i<s.length()&&(c=s.charAt(i))>=48&&(c=s.charAt(i))<=57){
                      str+=s.charAt(i);
                      i++;
                  }
                  ls.add(str);
              }
          }
          return ls;
    }
    /**
     * 将中缀表达式对应的List转为后缀
     */
    public static List<String> parseSuffixExperssion(List<String> ls){
         //定义两个栈
        Stack<String> s1=new Stack<>();
        //s2只需要保存结果，还要逆序输出，这里就不用栈了
        List<String> s2=new ArrayList<>();
        for (String item : ls) {
             if(item.matches("\\d+")){
                 s2.add(item);
             }else if(item.equals("(")){
                 s1.push(item);
             }else if(item.equals(")")){
                 while (!s1.peek().equals("(")){
                     //一直弹出，直到出现左括号为止
                     s2.add(s1.pop());
                 }
                 //把左括号弹出，消除一对括号
                 s1.pop();
             }else {
               while (s1.size()!=0&&Operation.getValue(s1.peek())>=Operation.getValue(item)){
                   //如果当前操作符的优先级小于等于 栈顶操作符的优先级
                   s2.add(s1.pop());
               }
               s1.push(item); //将该操作符要入栈

             }
        }
        //将s1中剩余的运算符依次弹出并加入s2
        while (s1.size()!=0){
            s2.add(s1.pop());
        }
        return s2;
    }
}
//返回运算符优先级的类
class Operation{
    private static int ADD=1;
    private static int SUB=1;
    private static int MUL=2;
    private static int DIV=1;
    public static int getValue(String operation){
        int result=0;
        switch (operation){
            case "+":
                result=ADD;
                break;
            case "-":
                result=SUB;
                break;
            case "*":
                result=MUL;
                break;
            case "/":
                result=DIV;
                break;
            default:
                System.out.println("不存在该运算符");
                break;
        }
        return result;
    }
}
```

# 递归的应用场景和调用机制

## 迷宫问题

![image-20200730115722234](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730115722234.png)

```
package com.cbx.recursion;

public class MIGong {
    public static void main(String[] args) {
        //创建一个迷宫 二维数组
        int[][] map=new int[8][7];
        //使用1表示墙
        //上下全置为1
        for (int i = 0; i <7; i++) {
            map[0][i]=1;
            map[7][i]=1;
        }
        //左右全置为1 
        for (int i = 0; i <8; i++) {
            map[i][0]=1;
            map[i][6]=1;
        }
        //设置挡板 也是1表示(不能走的地方)
        map[3][1]=1;
        map[3][2]=1;
        System.out.println("地图的情况");
        for (int i = 0; i < 8; i++) {
            for (int j = 0; j < 7; j++) {
                System.out.print(map[i][j]+" ");
            }
            System.out.println();
        }
        //找路后
        System.out.println("找路后的情况");
        boolean b = setWay(map, 1, 1);
        for (int i = 0; i < 8; i++) {
            for (int j = 0; j < 7; j++) {
                System.out.print(map[i][j]+" ");
            }
            System.out.println();
        }
    }
    //使用递归回溯来给小球找路
    /**
     *
     * @param map 表示地图
     * @param i 从哪个位置开始找
     * @param j(i,j)表示从哪个地方出发
     * 小球能到map[6][5]说明，通路能找到
     * 0没有走过  1表示墙 不能走 ，2表示通路可以走 3表示已经走过（但是走不通）
     * 定义策略下==》右==》上==》左 走不通再回溯
     * @return
     */
    public static boolean setWay(int[][] map,int i,int j){
          if (map[6][5]==2){
              //表示通路已经找到 ok
              return true;
          }else{
               if (map[i][j]==0){
                   //这个点还没有走过
                   map[i][j]=2;//假定该点可以走
                   if (setWay(map,i+1,j)){
                       //向下走
                       return true;
                   }else if(setWay(map,i,j+1)){
                       //向右
                       return true;
                   }else if (setWay(map,i-1,j)){
                       //向上
                       return true;
                   }else if (setWay(map,i,j-1)){
                       //向左走
                      return true;
                   }else {
                       //说明走不通
                       map[i][j]=3;
                       return false;
                   }
               }else {
                   //如果map[i][j]!=0 ,可能是1,2,3(2，3都可以认为之前走过,就不走了)
                   return false;
               }
          }
    }
}

```

## 八皇后问题(92种解法)

![image-20200730132027692](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730132027692.png)

![image-20200730132041505](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730132041505.png)

![image-20200730133655169](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730133655169.png)

```
package com.cbx.recursion;


public class Queue8 {
    int max=8; //皇后个数
    //可以使用一维数组，保存皇后放置位置的结果
    int[] array=new int[max];
    static int count=0;
    public static void main(String[] args) {
        Queue8 queue8 = new Queue8();
         queue8.check(0);
        System.out.println("一共有"+count+"种解法");
    }
    /**
     * 放置第n个皇后
     */
    private void check(int n){
        if (n==max){
//            n=8 其实是放第9个了，前面已经放好
            print();
            return;
        }
        //依次放入皇后，并判读是否冲突
        for (int i = 0; i < max; i++) {
            //先把这个皇后n，放在该行的第一列
            array[n]=i;
            //判断是否冲突
            boolean judge = judge(n);
            if (judge){
              //如果不冲突
                check(n+1);
            }
            //如果冲突会回溯到该行的下一个位置
        }
    }

    /**
     * 放置第n个皇后时，就去检测该皇后是否和前面已经摆放的皇后冲突
     * @param n n=1表示第二个皇后
     * @return
     */
    private boolean judge(int n){
        for (int i = 0; i < n; i++) {
            //不需要判断是否放在同一行，因为一直在递增着放
            if (array[i]==array[n]||Math.abs(n-i)==Math.abs(array[n]-array[i])){
                //在同一列 同一个斜线(同一直线斜率相等永远45度 y2-y1=k(x2-x1) k=1)
                return false;
            }
        }
        return true;
    }
    //写一个方法，可以将皇后摆放的位置输出
    private void print(){
        count++;
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i]+" ");
        }
        System.out.println();
    }
}

```

# 排序算法

![image-20200730151808487](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730151808487.png)

## 排序算法时间复杂度表

![image-20200730154936329](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200730154936329.png)

## 冒泡排序

```
package com.cbx.sort;

import java.util.Arrays;

public class BubbleSort {
    public static void main(String[] args) {
//        int arr[]={3,9,-1,-10,20};
        int arr[]={1,2,3,4,5,6};
        bubbleSort(arr);

    }
    public static void bubbleSort(int[] arr){
        int temp=0; //临时变量
        boolean flag=false;//标记，表示是否进行交换
        for (int j = 0; j < arr.length - 1; j++) {
            for (int i = 0; i < arr.length-1-j; i++) {
                if (arr[i]>arr[i+1]){
                    flag=true;
                    temp=arr[i];
                    arr[i]=arr[i+1];
                    arr[i+1]=temp;
                }
            }
            System.out.println("第"+(j+1)+"排序后的数组"+ Arrays.toString(arr));
            if (!flag){
                //没交换过
                break;
            }

        }
    }
}

```

## 选择排序

![image-20200731124348418](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200731124348418.png)

```
package com.cbx.sort;

import java.util.Arrays;

public class selectSort {
    public static void main(String[] args) {
         selectSort(new int[]{3,5,1,2,4});

    }
    public static void selectSort(int[] arr){
        for (int i = 0; i < arr.length - 1; i++) {
            int minIndex=i; //最小值的索引
            int min=arr[i]; //最小值
            for (int j=i+1;j<arr.length;j++){
                if (min>arr[j]){
                    min=arr[j]; //重置Min
                    minIndex=j; //重置minIndex
                }
            }
            if (minIndex!=i){
                //交换
                arr[minIndex]=arr[i];
                arr[i]=min;
            }
            System.out.println("第"+(i+1)+"轮后~~");
            System.out.println(Arrays.toString(arr));
        }
    }
}

```

## 直接插入排序

```
package com.cbx.sort;

import java.util.Arrays;

public class InsertSort {

    public static void main(String[] args) {
            int[] arr={25,34,1,5,3};
            insertSort(arr);
    }

    public static void insertSort(int[] arr){
        for (int i = 1; i < arr.length; i++) {
            //定义待插入的数
            int insertVal=arr[i];
            int insertIndex=i-1;
           while (insertIndex>=0&&insertVal<arr[insertIndex]){
               arr[insertIndex+1]=arr[insertIndex];
               insertIndex--;
           }
           arr[insertIndex+1]=insertVal;
            System.out.println("第"+i+"次插入");
            System.out.println(Arrays.toString(arr));
        }
    }
}
```

## 希尔排序

<img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200808224908006.png" alt="image-20200808224908006" style="zoom:50%;" />

```
package com.cbx.sort;

import java.util.Arrays;

/**
 * 简单插入排序的高效版本
 * 交换法
 */
public class ShellSort {
    public static void main(String[] args) {
         int[] arr={8,9,1,7,2,3,5,4,6,0};
//         shellSort(arr);
         shellSort2(arr);
        System.out.println(Arrays.toString(arr));
    }


    /**
     * 交换法
     * @param arr
     */
    public static void shellSort(int[] arr){
        int temp=0;
        for(int gap=arr.length/2;gap>0;gap/=2){
            //共gap组，步长gap
            for(int i=gap;i<arr.length;i++){
                for (int j=i-gap;j>=0;j-=gap){
                    if (arr[j]>arr[j+gap]){
                        temp=arr[j];
                        arr[j]=arr[j+gap];
                        arr[j+gap]=temp;
                    }
                }
            }
            System.out.println("步长"+gap+ Arrays.toString(arr));
        }
    }

    /**
     * 移位法（是进一步的优化）
     * @param arr
     */
    public static void shellSort2(int[] arr){
        for(int gap=arr.length/2;gap>0;gap/=2) {
             for(int i=gap;i<arr.length;i++){
                 int j=i;
                 int temp=arr[j];
                 if (arr[j]<arr[j-gap]){
                     while(j-gap>=0&&temp<arr[j-gap]){
                        arr[j]=arr[j-gap];
                        j-=gap;
                     }
                     arr[j]=temp;
                 }
             }
        }
    }
}

```

## 快速排序(第一个数为基准)

```
package com.cbx.sort;

import java.util.Arrays;

public class QuickSort {
    public static void main(String[] args) {
        int[] arr={1,2,5,0,9,-1};
        quickSort(arr,0,arr.length-1);
        System.out.println(Arrays.toString(arr));
    }

    public static void quickSort(int[] arr,int low,int high){
            if (low<high){
                int pivotpos=Partition(arr,low,high);
                quickSort(arr,low,pivotpos-1);
                quickSort(arr,pivotpos+1,high);
            }
    }

    public  static int Partition(int[] arr,int low,int high){
          int pivot=arr[low];
          while(low<high){
              while (low<high&&arr[high]>=pivot) --high;
              arr[low]=arr[high];  //小的移动到左端

              while(low<high&&arr[low]<=pivot) ++low;
              arr[high]=arr[low]; //大的移动到右端
          }
          arr[low]=pivot;
          return low;

    }
}

```

## 归并排序

![image-20200809153724660](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200809153724660.png)

```
package com.cbx.sort;

import java.util.Arrays;

public class MergeSort {
    public static void main(String[] args) {
     int arr[]={8,4,5,7,1,3,6,2};
       int[] temp=new int[arr.length];
       mergeSort(arr,0,arr.length-1,temp);
        System.out.println(Arrays.toString(arr));
    }


    public static void mergeSort(int[] arr,int left,int right,int[] temp){
        if (left<right){
            int mid=(left+right)/2;
            //做左递归分解
            mergeSort(arr,left,mid,temp);
            //右递归分解
            mergeSort(arr,mid+1,right,temp);
            merge(arr,left,right,mid,temp);

        }
    }


    public static void merge(int arr[],int left,int right,int mid,int[] temp){
        System.out.println("xxx");
            int i=left;
            int j=mid+1;
            int t=0;
            while (i<=mid&&j<=right){
                if (arr[i]<=arr[j]){
                    temp[t]=arr[i];
                    t++;
                    i++;
                }else {
                    temp[t]=arr[j];
                    t++;
                    j++;
                }
            }
            while (i<=mid) {
                //左边的有剩余
                temp[t] = arr[i];
                t++;
                i++;
            }
            while (j<=right){
                temp[t]=arr[j];
                t++;
                j++;
            }

            //将temp数组的元素拷贝到arr
             t=0;
            int tempLeft=left;
        System.out.println("tempLeft="+tempLeft+"right="+right);
            while (tempLeft<=right){
                arr[tempLeft]=temp[t];
                t++;
                tempLeft++;
            }



    }
}


```

## 基数排序

![image-20200810225650158](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200810225650158.png)