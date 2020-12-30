# 1.进程调度，以及进程调度算法？

![image-20201221211416696](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201221211416696.png)

https://blog.csdn.net/wx960421/article/details/88697996

```
因为会有多个进程，并且会产生对cpu占有权的竞争，所以需要有合理的进程调度。

进程是资源分配的最小单位，线程是任务执行的最小单位。                                                            

1.先来先调度算法。
 对于首先达到的进程,cpu先进行执行，其他后面来的依次按照时间排在就绪队列中
 优点：比较简单，容易实现
 缺点：1.如果是执行时间长的进程，那么我们的平均周转时间就要增加
      2.如果有一个cpu密集型的进程和多个i/o密集型的进程，可能发生这种情况:
        cpu密集型程序得到cpu,并使用它，在这段时间内，其他所有的进程会处理完I/O，然后到就绪队列尾部等待cpu
        此时I/O设备就空闲，但是I/O密集型进程还在等待。
2.短任务优先调度算法
    避免平均周转时间较长，非抢占式的，选择下一次预估时间最短的任务
3.最短剩余时间调度算法
   是短任务优先调度算法的升级版， 是抢占式的，如果当前执行的进程剩余时间 比 新来的进程执行时间来的长，就切换为新的进程
    缺点：可能会导致长时间的进程无法得到调度，饥饿现象
4.最高响应比优先算法
    R=(w+s)/s 考虑了等待时间  ，缓解了长时间线程的饥饿现象 （w为等待时间，s为服务时间）、
    
5.时间片轮询算法
是抢占式的，由时钟装置发出时钟中断来通知cpu时间片已经到了
按照个进程到达就绪队列的顺序，轮流让各个进程执行时间片，执行完放入队尾。
    比较公平，合理设置时间片给每一个进程轮询执行
    产生大量的上下文切换开销，但是公平性比较好
  ## 时间片太大，每个任务都在时间片内完成任务，就退化为先来先服务
  ## 时间片太小，就会产生大量的上下文切换，产生很大的开销

6.基于优先级的调度算法(可以是抢占式的，可以是非抢占式的) 会导致饥饿的
    每个进程都关联一个优先级，内核将CPU分配给最高优先级的进程。具有相同优先级的进程，按照先来先服务的原则进行调度。
    要考虑进行饿死问题，因为高优先级的进程总会被优先调度，具有低优先级的进程可能永远都不会被调度执行。所以需要增加一个Aging，就是指逐渐提高系统中长时间等待的线程的优先级。

7.多级反馈队列算法
    1.进程在进入待调度的队列等待时，首先进入优先级最高的Q1等待。

    2.首先调度优先级最高的队列中的进程，若最高优先级队列中已经没有调度的进程，则去调度次优先级的队列中的进程。
 
    3.对于在同一个队列中的各个进程，按照时间片轮询调度。比如Q1队列的时间片为N，那么Q1中的作业在经历了N个时间片后若还没有完成，则进入Q2队列等待，若Q2的时间片用完后作业还不能完成，一直进入下一级队列，直至完成。

   4.在低优先级的队列中的进程在运行时，又有新到达的作业，那么在运行完这个时间片后，CPU马上分配给新到达的作业（抢占式）。
   
   5.高优先级的队列分配的时间片时间短，优先级越低分配的时间片时间越长。
```

![image-20201221211110234](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201221211110234.png)

![image-20201221211311798](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201221211311798.png)

# 三种调度对比(高级，终极，低级)

![image-20201220204353479](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220204353479.png)

![image-20201220204516634](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220204516634.png)



![image-20201220204554121](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220204554121.png)

![image-20201220204626453](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220204626453.png)

# 调度算法的评价指标

![image-20201221201354468](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201221201354468.png)



```
周转时间：作业完成时间-作业提交时间
平均周转时间：各作业周转时间之和/作业数
```

# 各种指标计算方法

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201221203712996.png)

# 进程互斥的软件实现方法

```
1.单标志法 违背空闲让进原则

2.双标志先检查法 违背忙则等待原则

3.双标志后检查法 违背空闲让进 和 有限等待原则 导致长期无法访问临界资源 饥饿现象

4.Peterson算法 解决了进程互斥问题，遵循了空闲让进，忙则等待，有限等待三个原则，但是依然未遵循让权等待原则。  相比之前三个是最好的，但是依然不够好,期间会有一个忙等状态，占用着cpu，等到时间片用完才行
```

![image-20201222200955380](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222200955380.png)

![image-20201222201502381](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222201502381.png)

![image-20201222201801662](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222201801662.png)

![image-20201222202716827](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222202716827.png)

# 进程互斥的硬件实现方法

```
1.中断屏蔽方法

2.TestAndSet指令(硬件层面实现)

3.Swap指令
```

![image-20201222203320030](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222203320030.png)

![image-20201222203708667](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222203708667.png)

![image-20201222203839656](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222203839656.png)



![image-20201222204911954](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222204911954.png)

## 信号量机制

![image-20201222205832629](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222205832629.png)

### 整型信号量

![image-20201222210221524](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222210221524.png)

### 记录性信号量

![image-20201222210459944](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222210459944.png)

![image-20201222210756011](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222210756011.png)

## 信号量机制实现进程互斥

```
信号量初值设为1
```

![image-20201223134250256](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223134250256.png)

## 信号量机制实现进程同步

```
在前面的操作之后执行V操作 signal()
在后面的操作之前执行P操作 wait()

信号量初值设为0
```

![image-20201223134742377](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223134742377.png)

# 生产者消费者问题

![image-20201223140902506](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223140902506.png)

![image-20201223141117051](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223141117051.png)

### 改变P V顺序会造成死锁现象

```
实现互斥的p操作，一定要放在实现同步的p操作之后       先同步 ==》后互斥
```

![image-20201223141420304](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223141420304.png)

# 管程

```
就是更方便的实现进程互斥和同步，之前都是使用信号量 P V操作

类似于java中synchronized关键字的使用方法
```

![image-20201223144823325](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223144823325.png)

# 死锁，饥饿，死循环

![image-20201223150809567](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223150809567.png)

## 死锁的四个必要条件

![image-20201223151644016](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223151644016.png)

## 避免死锁

https://www.cnblogs.com/bopo/p/9228834.html#_label3_0

# 2.TCP三次握手四次挥手？为什么四次挥手等待2MSL?

https://www.cnblogs.com/Andya/p/7272462.html

## 三次握手

```
三次握手的目的是建立可靠的通信通道，就是建立可靠的数据发送和接收

为什么要发送特定的数据包？
  1.第一次握手：Server猜测Cilent可能要建立TCP请求，但是不确定，因为可能是Client乱发的一个数据包
  
  2.第二次握手：通过ack=J+1,Client知道Server是支持TCP的
  
  3.第三次握手：通过ack= K+1，Server知道Client是支持TCP的，开始确定连接
```

![image-20201202214116679](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201202214116679.png)

```
1.第一次握手：客户端向服务器端发出请求报文段，(SYN(同步位=1)表示请求连接，seq=x(随机的初始序列号))，此时客户端进入SYN-SENT(同步已发送状态).

###大写的ACK代表TCP Flags 控制位中的  确认序号标志，ACK=1的时候表示确认序号标志有效，ACK=0表示报文中不含确认信息可以忽略。

小写的ack意思是 你第一次握手从client发送SYN包到server得时候携带的seq=0,当server接收到client发送的SYN包时会携带SYN,ACK包，seq=0，ack= 1(client中的seq+1), 表示消耗了（client第一次握手的seq）。ack就是确认序列号

2.第二次握手：服务器收到客户端请求报文后，如果同意连接，则会向A发出确认，(SYN=1,ACK=1，确认号ack=x+1,seq=y)，此时服务器进入SYN-RCVD(同步收到状态)

3.第三次握手:客户端接收到服务器的确认后，要给服务器发送确认报文段(ACK=1,seq=x+1,ack=y+1) 进入established状态 ，服务器接收到后也进入确认状态
```

## 四次挥手

![image-20201202220417384](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201202220417384.png)

```
1.客户端向服务器发出释放报文段(FIN=1,seq=u)，并停止发送数据，主动关闭TCP连接，进入FIN-WAIT-1（终止等待1）状态，等待B的确认。

2.服务器收到释放报文段后发出确认报文段(ACK=1,ack=u+1,seq=v)，服务器进入CLOSE-WAIT（关闭等待）状态，此时TCP处于半关闭状态。
##可能服务器还有数据没有发送完，所以得先发送完数据再真正的断开

3.客户端接收到B的确认后，进入FIN-WAIT2(终止等待状态2)，等待服务器发出的连接释放报文段

4.有数据传送就先传数据，没有数据传送，服务器发出连接释放报文段(FIN=1,ACK=1，seq=w,ack=u+1)，服务器就会进入LAST-ACK（最终确认阶段），等待客户端的确认。

5.客户端接收到服务器的连接释放报文段后，对此发出确认报文段(ACK=1,seq=u+1,ack=w+1),客户端进入TIME-WAIT（时间等待状态）,此时TCP连接未释放掉，等待计时器设施的时间2MS后释放。
```

### 为什么客户端在TIME-WAIT必须等待2MSL时间？

```
MSL最长报文段寿命Maximum Segment Lifetime，MSL=2

1.这个ACK报文段有可能丢失，使得处于LAST-ACK状态的B收不到对已发送的FIN+ACK报文段的确认，B超时重传FIN+ACK报文段，而A能在2MSL时间内收到这个重传的FIN+ACK报文段，接着A重传一次确认，重新启动2MSL计时器，最后A和B都进入到CLOSED状态。

2.A在发送完最后一个ACK报文段后，再经过2MSL，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失，使下一个新的连接中不会出现这种旧的连接请求报文段。
```

# 3.https跟http区别

https://www.sslzhengshu.com/article/post-547.html

# 4.HTTPS加密过程？对称加密非对称加密？

https://www.wosign.com/News/httpsjiami_20180817.htm



```
使用了对称加密（AES）和非对称加密（RSA）

对称加密算法加密数据+非对称加密算法交换密钥+数字证书验证身份=安全

对称加密: 加密和解密使用的是同一个秘钥

非对称加密：有一对秘钥：公钥和私钥 ，公钥加密的内容，只有私钥才能解开，私钥加密的的内容，对应的公钥都能解开
           私钥只保存在服务器端，公钥可以发送给所有的客户端
 1.首先客户端发起握手请求，此时是明文传输的，(一些版本信息，加密套件候选列表，压缩算法候选列表。。。)到443端口。
 
 2.采用https协议的服务器必须要有一套数字证书，这套证书相当于公钥和私钥。
 
 3.服务器返回证书公钥，客户端验证证书的合法性(可信性，过期时间，域名)，由客户端的SSL/TLS来完成，首先验证公钥是否有效，如果没有问题，就会生成一个随机值，然后用证书公钥对这个随机值进行加密。
 
 4.客户端使用公钥对称秘钥加密，发送给服务器(目的是让服务器得到这个随机值，以后客户端和服务端的通信就可以通过这个随机值来进行加密解密了)。
 
 5.服务器使用私钥解密，拿到对称加密的秘钥。（解密后拿到了客户端传过来的随机值，然后对随机值进行对称加密，）
 
 6.传输加密后的信息，可以在客户端用随机值解密还原。
 
```

![image-20201203171806354](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201203171806354.png)

# 5.cookie和session?

```
会话是由一组相关的请求与响应组成(从第一次请求开始，到关闭浏览器)，是围绕这一件相关事情所进行的请求与响应，这些请求与响应之间是需要数据传递的，即需要进行会话状态跟踪。而http请求是一种无状态协议，在不同的请求之间是无法进行数据传递的。cookie就是在请求间数据传递的会话跟踪技术。

只要资源路径相同，资源名称不一样。也会携带cookie,http://192.168.2.0/aa/bb   /bb 就是资源名称

也可以绑定路径 cookie.setPath()方法	指定的路径必须要添加项目名称 request.getcontextPath()+"指定的路径"

设置cookie的有效值：1.该值大于0，表示存放到客户端的硬盘
                  2.该值小于0，相当于没设置的效果，会将cookie存放到浏览器的缓存
                  3.该值等于0，表示cookie一生成，马上失效
                  
                  
区别：
 1.cookie数据存在客户的浏览器上，session数据存放在服务器上
 
 2.cookie不是很安全，别人可以分析存放在本地的cookie
 
 3.session会在一定时间内保存在服务器上，(默认失效时间为30分钟)，当访问增多时，服务器压力增大
 
 4.单个cookie保存的数据不能超过4K，很多浏览器限制是一个站点最多保存20个cookie
 
 5.生存周期： 关闭浏览器后session就失效了(jessionId存放在浏览器的缓存中)，但是cookie可以设置生存时间，关闭浏览器还可以操作
                 
```

## session的工作原理

1.写入session列表

```
服务器对当前应用的session是以Map的形式进行管理的，map的key是32位的随机字符串，jessionId,value是session对象的引用。
当用户第一次提交请求时，服务端servlet中执行到getsession会自动生成一个Map.Entry对象，key:jessionId,
value:新创建的httpsession对象
```

![image-20201204151124768](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201204151124768.png)

2.服务器生成并发送cookie

```
 将session信息写入session列表后，系统会自动将JSESSIONID作为name,这个32位长度的key作为value以cookie的形式响应给客户端。
```

![image-20201204151417042](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201204151417042.png)

3.客户端接收并发送cookie

```
客户端接收到这个cookie后将其存放在浏览器缓存中，只要客户端浏览器不关闭，浏览器中缓存的cookie就不会消失

当用户提交第二次请求后，会将缓存中的这个cookie，伴随着请求的头部信息，一并发给服务器端
```

![image-20201204151934047](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201204151934047.png)

4.从session列表中查找

```
根据cookie的jSsessionID,找到对应的session对象
```

## session失效

默认30分钟

![image-20201204152456855](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201204152456855.png)

# 6.设计模式

## 单例模式

### 饿汉式

```
//静态常量版(线程安全，反射不安全，反序列化不安全)
public class Singleton{
      //私有构造器
      private Singleton(){};
      
      private final static Singleton instance  = new Singleton();
      
      //提供公共方法获取实例
      public static Singleton getInstance(){
         return instance;
      }
}
```

```
/**
 * @Author:cbx
 * @Date:2020/11/08/21:04
 * 静态代码块版的单例模式
 */
public class Singleton2 {

    private static Singleton2 instance;
   static {
         instance=new Singleton2();
    }
     private Singleton2(){

     }

     public static Singleton2 getInstance(){
        return instance;
     }
}
```

**反射不安全，反序列化不安全**

```
 /**
     * 反射是不安全的
     */
    @Test
    public void test1() throws Exception{
        Class clazz = Singleton1.class;
        Constructor constructor = clazz.getDeclaredConstructor();
        constructor.setAccessible(true);
        Singleton1 s1 = Singleton1.getInstance();
        Singleton1 s2 = (Singleton1) constructor.newInstance();

        System.out.println(s1==s2);  //false
    }
    
    
  //因为静态的变量在序列化的时候不会被保存，所以在反序列化的时候就会生成一份新的
  在代码中加入一行方法
  private Object readResolve(){
       return instance;
  }
```

### 登记式(静态内部类式)

**线程安全，防止反射攻击，反序列化不安全**

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:21
 * 静态内部类法
 */
public class Singleton6 {

    private Singleton6(){

    }

    public static Singleton6 getInstance(){
        return SingletonProducer.instance;
    }

    private static class SingletonProducer{
        private static Singleton6 instance=new Singleton6();
    }
}

```

**防止反射攻击的版本**

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:21
 * 静态内部类法
 */
public class Singleton6 {
    public static void main(String[] args) throws ClassNotFoundException {
        Class.forName("com.cbx.Singleton.Singleton6");
    }

    private Singleton6(){
        System.out.println("构造方法执行");

        //防止反射攻击
        if (SingletonProducer.instance!=null){
            throw  new IllegalStateException();
        }
    }

    public static Singleton6 getInstance(){
        return SingletonProducer.instance;
    }

    private static class SingletonProducer{
        private static Singleton6 instance=new Singleton6();
    }
}

```

### 枚举式(最佳方式)

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:25
 * 枚举法
 * 线程安全，防止反序列化问题 (推荐使用******)，防止反射攻击
 */
public enum Singleton7 {
   INSTANCE;
}


//newInstance的时候会报NoSuchMethodException 所以自动防止了反射攻击
  Class<Singleton7> singleton7Class = Singleton7.class;
        Constructor<Singleton7> constructor = singleton7Class.getDeclaredConstructor();
        constructor.setAccessible(true);
        Singleton7 singleton7 = (Singleton7) constructor.newInstance();
        
        java.lang.NoSuchMethodException: com.cbx.Singleton.Singleton7.<init>()

```

### 懒汉式

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:11
 *  懒汉式(线程不安全版)
 */
public class Singleton3 {
    private static Singleton3 singleton;

    private Singleton3(){

    }

    public static Singleton3 getInstance(){
         if (singleton==null){
             singleton=new Singleton3();
         }
         return singleton;
    }
}

```

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:13
 * 懒汉式(线程安全版)
 * 同步方法 和 同步代码块一样
 */
public class Singleton4 {
    private static Singleton4 singleton;

    private Singleton4(){

    }
     //加锁保证线程安全
    public static synchronized Singleton4 getInstance(){
        if (singleton==null) {
             singleton=new Singleton4();
        }
        return singleton;
    }

    //加锁保证线程安全
    public static  Singleton4 getInstance2(){
        synchronized (Singleton4.class){
            if (singleton==null) {
                singleton=new Singleton4();
            }
            return singleton;
        }

    }


}

```

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:16
 * 双重检索方法
 */
public class Singleton5 {
    //防止指令重排
    private static volatile Singleton5 instance;

    private Singleton5(){

    }

    public static Singleton5 getInstance(){
        if (instance==null){
              synchronized (Singleton5.class){
                  if (instance==null){
                      instance=new Singleton5();
                  }
              }
        }
        return instance;
    }
}

```

**虽然A instance 分配了地址，但是还没有开始初始化，所有后面线程进来返回的是没有初始化的对象**

**正常的是 （1）（3）（2）**

![image-20201207125426070](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201207125426070.png)

### ThreadLocal版 保证每个线程内部是单例的

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/12/07/13:06
 *
 */
public class Singleton8 {

     private static Singleton8 instance = null;

     private Singleton8(){

     }

     private static final ThreadLocal<Singleton8> threadLocalSingleton = new ThreadLocal<Singleton8>(){
         @Override
         protected Singleton8 initialValue() {
             return new Singleton8();
         }
     };

     public static Singleton8 getInstance(){
         return threadLocalSingleton.get();
     }
}

```

```
  @Test
    public void test4(){
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                Singleton8 s1 = Singleton8.getInstance();
                Singleton8 s2 = Singleton8.getInstance();
                System.out.println(Thread.currentThread().getName()+" ,"+(s1==s2)+" "+s1);
                //Thread-3 ,true com.cbx.Singleton.Singleton8@14a5ac19
                //Thread-15 ,true com.cbx.Singleton.Singleton8@4022220c 每个线程中都能保证是单列的
            }).start();
        }
    }
```

### 利用CAS进行操作

```
package com.cbx.Singleton;

import java.util.concurrent.atomic.AtomicReference;

/**
 * @Author:cbx
 * @Date:2020/12/07/13:23
 */
public class Singleton9 {

    private static final AtomicReference<Singleton9> instance = new AtomicReference<>();

    private Singleton9(){
        System.out.println("构造方法");
    }
    public static final Singleton9 getInstance(){
        for(;;){
            Singleton9 current = instance.get();
            if (current!=null){
                return current;
            }
            current = new Singleton9();
            //如果是null,我们就设为current
            if (instance.compareAndSet(null,current)){
                return current;
            }
        }
    }
}

```



# 7.说说序列化跟反序列化

https://www.cnblogs.com/xdp-gacl/p/3777987.html

```
序列化就是把对象转换成字节序列(二进制)的过程，反序列化就是将字节序列回复成对象的过程，可以保存到磁盘或者网络发送。
　　对象的序列化主要有两种用途：
　　1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中；
　　2） 在网络上传送对象的字节序列。

　　在很多应用中，需要对某些对象进行序列化，让它们离开内存空间，入住物理硬盘，以便长期保存。比如最常见的是Web服务器中的Session对象，当有 10万用户并发访问，就有可能出现10万个Session对象，内存可能吃不消，于是Web容器就会把一些seesion先序列化到硬盘中，等要用了，再把保存在硬盘中的对象还原到内存中。

　　当两个进程在进行远程通信时，彼此可以发送各种类型的数据。无论是何种类型的数据，都会以二进制序列的形式在网络上传送。发送方需要把这个Java对象转换为字节序列，才能在网络上传送；接收方则需要把字节序列再恢复为Java对象。


 1.java.io.Serializable
 2.java.io.Externalizable
 3.ObjectInputStream
 4.ObjectOutPutStream
 
 怎么让变量不被序列化
 1.变量声明为静态变量 static
 2.变量声明为瞬态变量 transient
 
 serialVersionUID 如果没有指定的话，系统自动生成，后面如果改了对象，导致反序列化的时候serialVersionUID不相同，就会反序列化失败，并且抛出java.io.InvalidClassException
 
 
 如果静态变量在同一个jvm中，还是显示的效果是可以序列化的，本质不可以，因为读取的是静态变量提前加载好了
 如果在网络上。别的机器反序列化就会显示Null,因为虚拟机没有提前加载好该静态变量。
```

# 8.java深拷贝与浅拷贝与对象拷贝

https://www.jianshu.com/p/94dbef2de298

```
浅拷贝：创建一个新对象，然后将当前对象的非静态字段复制到新对象，如果字段是值类型，拷贝的就是值，如果是引用类型，拷贝的就是内存地址。因此其中一个对象改变了，会影响到另一个对象。因为两个对象指向了同一个内存地址。
```

```
如果将浅拷贝换为对象拷贝，对象拷贝后不会生成新的对象，二者的对象地址是一样的，而浅拷贝的对象地址时不一样的。
```

```
深拷贝：浅拷贝会带来数据安全方面的隐患。如果只想修改一个对象里面的一个引用类的成员，会影响到另一个对象中的成员。

深拷贝，在拷贝引用类型成员变量时,为引用数据类型的成员变量另辟了一个独立的内存空间，实现真正内容上的拷贝。

拷贝基本类型的数据时，和浅拷贝是一样的，对于引用数据类型，改变其中一个，也不会影响另外一个。

对于有多层对象的，每个对象都需要实现Cloneable并重写clone()方法，进而实现了对象的层层拷贝。

深拷贝相比于浅拷贝速度较慢并且花销更大
```

![image-20201207184345727](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201207184345727.png)



![image-20201207184403776](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201207184403776.png)



# 9.说说HashMap,Hashtable,和ConcurrentHashMap?HashMap多线程下会有什么问题？

```
hash的基本概念就是将任意长度的输入通过一个hash算法之后映射成固定长度的输出。

hash冲突：如果通过hash算法之后算出两个相同的hash值，就会发生hash冲突

1.jdk8中HashMap现在是数组加链表加红黑树构成的。每个数据单元都是一个Node结构，Node结构中有key字段，value字段,next字段，以及hash字段，next字段就是当发生hash冲突时，连接下一个链表元素用的，数组的默认长度时16，

2. 散列表是懒加载机制，只有第一次put的时候才创建，执行无参的构造函数时，只是指定了加载因子为0.75f,只有当put的时候才会通过resize()方法去初始化数组

3. 链表转红黑树有两个指标： 1.链表长度达到了8 2.当前散列表的数组容量已经达到64  必须连个都满足，不然不会转换为红黑树，只是会发生一次resize

4. Node中的hash值是我们存放元素的key.hashcode嘛？
 不是，是key.hashcode二次加工的得到的，通过hash(key)方法，传入hashcode,让它高16位异或上低16位得到，
 
 主要是因为hash寻址算法的缘故，首先那个散列表的长度必须是2的次方数，寻址算法是hash & (table.length-1),length转换为二进制后，一定是1是高位，然后低位全是0，减掉1之后就会出现全是0，低位全是1的情况，任何数与这种数相与之后，得到的数值其实只是跟我们hash的低位有关，高位并没有参与进来，这样就会增加了冲突的可能性，所以我们需要尽可能让高位也参与进来，通过高16位异或上低16位

5.put方法的具体流程？
  ## 首先是进行初始化工作的，通过resize()方法，将数组的长度初始化为16
    &&&&&&&&&&&&&&&&& 没有链化前 &&&&&&&&&&&&&&&&&&&&&&&&&&
  ## 如果计算出来的slot位置没有元素，直接将key,value,hash,next=null包装成一个Node对象放入数组对应的slot位置上
  
  ## 如果计算出来的slot位置上有元素了，判断该元素的key与传入进来的元素的key是否相等，如果完全相等，就会发生替换操作，将新值的value覆盖老的value,并且有返回值，是oldValue，如果key不相等，就是真二八经的哈希冲突了，就在链表尾部追加该Node节点，尾插法
    &&&&&&&&&&&&&&&&& 链化后 &&&&&&&&&&&&&&&&&&&&&&&&&&
  ## 跟上面的流程类似，遍历链表，如果有key相等的就会覆盖，如果遍历到了尾部，就直接尾插，最后需要检查一下链表的长度有没有超过树化的阈值，从0开始到7，就相当于达到8的长度就开始转换为红黑树
  

6.红黑树插入操作
  先聊一下TreeNode这个数据结构，它是继承了Node结构的，在Node的基础上加了几个字段，指向父节点的Parent,指向左孩子的left,指向右孩子的right，还有表示颜色的red,首先找到一个合适的插入点，就是找到插入节点的父节点。找父节店的操作是跟二叉排序树一样的，二插排序树就是左子树的值小于当前节点，右子树上的值大于当前节点，然后找到父节点之后，如果查找期间有相同Key的元素，那么也就进行覆盖，之后需要根据红黑树的平衡算法来进行调整红黑树
  
7.红黑树的特性

 ## 对于每个节点，从该节点到其它子孙节点的所有路径上包含相同的黑节点
 
 ## 插入的节点默认是红色的(如果黑插，可能不能满足性质1)  ，根节点是黑色的
 
 ##不可能有连在一起的红色节点
 
 ##每个红节点的两个子节点是黑色的
 
8.扩容
HashMap是基于hash表实现的，每一个元素是一个key-value对，其内部是通过单链表解决hash冲突的，当size超过阀值时，hashmap会进行扩容。 扩容是之前的老数组容量左移一位 ，就是*2得到新数组的容量，使用左移是因为cpu毕竟是不支持乘法运算，所有乘法运算最终在指令层面都转化为加法实现的，效率不高，位运算对cpu更友好一点

9.数据迁移
对于只有单个节点的链表，我们可以直接通过hash值存放在对应的新数组的slot位置，如果发生过hash冲突，也就是头结点.next不为空的情况，将原链表分为两种情况(拆分成高位链和低位链)，我们hash算法是hash & (tab.length-1) 然后我们扩容了两倍，举个列子，如果刚开始数组的长度是16，==》0000 1111  ， 32 0001 1111, 与上hash的话，要么就是原位置不变，要么就是加上原数组长度的位置。

扩容会发生在一下两种情况

a 当前存入数据大于阈值即发生扩容

b 存入数据到某一条链表上，此时数据大于8，且总数量小于64即发生扩容，链表转换为红黑树
```

## hashmap和hashtable的区别？

```
1.继承的父类不同，hashtable继承自Dictionary,而HashMap继承自AbstractMap类，但二者都实现了Map接口。

2.hashtable是线程安全的，里面的方法加了synchronize关键字修饰。

3.hashtable的key,value不允许出现null值，运行时会抛出NullPointerException.HashTable中，Null可以作为主键，这样的键只有一个，但是可以有一个或多个键对应的值为null.

4.当get()方法返回null值时，可能是 HashMap中没有该键，也可能使该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。

5.Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 。

6.HashTable直接使用对象的hashCode,HashMap在hashcode上重新进行了计算。int index = (hash & 0x7FFFFFFF) % tab.length; hash & 0x7FFFFFFF（0111 1111111.。。。。）为了让负数变为正数。
hashtable使用取模运算，hashmap使用的是&运算。

7.HashTable在不指定容量的情况下的默认容量为11，而HashMap为16，Hashtable不要求底层数组的容量一定要为2的整数次幂，而HashMap则要求一定为2的整数次幂。Hashtable扩容时，将容量变为原来的2倍加1，而HashMap扩容时，将容量变为原来的2倍。
```

# 10.如何减少上下文切换？

https://blog.csdn.net/qq_24672657/article/details/102731489

```
cpu通过时间片分配算法来循环执行任务，当前任务执行一个时间片后就会切换到下一个任务。但是，在切换之前会保存上一个任务的状态，以便下一次切换回这个任务时，可以加载这个任务的状态。所以任务从保存到再加载的一次过程就是一次上下文切换。

1.使用无锁并发编程，多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以使用一些办法避免锁。如将数据的ID按照hash算法取模分段，不同的线程处理不同段的数据。

2.CAS算法。Java的Atomic包使用CA算法，不需要加锁。

3.使用最少线程，避免创建不需要的线程，比如任务很少，但是创建了很多线程来处理，会造成大量线程都处于等待状态。

4.协程：在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

为什么无锁就可以减少上下文切换呢？
 无锁只能是减少上下文切换，不能避免的。
  首先我们得知道引起线程切换的条件： 1.线程运行的时间片时间用完了，线程切换 2.线程在时间片结束前阻塞或结束，线程切换。
   有锁并发编程，线程在没有竞争到锁的时候会进行阻塞，造成线程的提前切换，无锁并发编程只有在时间片用完之后在切换。对于CAS,当线程使用CAS算法更新数据时，如果发现不是期望的数据，那么他会进入循环进行更新，或者时间片用完后切换线程，从而减少了线程的切换。
   
```

# 11.volatile关键字

```
volatile是轻量级的synchronized，它在多处理器开发中保证了共享变量的可见性。可见性是指当一个线程修改一个共享变量时，另一个线程能读到这个修改的值。如果一个字段被声明成了volatile，java线程内存模型保证所有的线程看到这个变量的值是一致的。

在有volatile变量修饰的共享变量进行写操作时，会多出一行汇编代码 lock add 

有lock前缀的指令在多核处理器下会引发两件事情：
   1.将当前处理器缓存行的数据写会到系统内存。
   2.这个写回内存的操作会使其他cpu里缓存了该内存地址的数据无效。（MESI缓存一致性协议）
   
 处理器不直接和内存进行通信，处理器内部有缓存，处理器会先将系统内存的数据读到内部缓存(线程的)后再进行操作，如果是volatile修饰的并且是写操作的。jvm会向处理器发送一条Lock前缀的指令，将这个变量所在的缓存行的数据写回到系统内存，并使其他缓存行的失效，当处理器对这个数据进行修改时，会重新从系统内存中读取数据到内部缓存中。
```

![image-20201208155516461](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208155516461.png)

jmm数据原子操作

![image-20201208155808366](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208155808366.png)

```
总线加锁：(早期的cpu)
cpu从主内存读取数据到高速缓存，会在总线对这个数据进行加锁，这样其它cpu没法去读或写这个数据，直到这个cpu使用完数据释放锁之后其它cpu才能读取该数据。

mesi缓存一致性协议：
多个cpu从主内存读取同一个数据到各自的高速缓存，当其中某个cpu修改了缓存里的数据，该数据马上同步回主内存，其它cpu通过总线嗅探机制可以感知到数据的变化从而将自己缓存里的数据失效。
```

![image-20201208163125183](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208163125183.png)

![image-20201208165254111](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208165254111.png)

**volatile不保证原子性**

```
从java内存模型的层面去谈：
假设我们现在是num++操作
线程1 在cpu的核心1中 操作
线程2 在cpu的核心2中 操作

线程1 read操作将num = 0 读取到工作内存的缓存中， use操作，进行num++,assign回工作内存，然后store到主内存中，num = 1,如果此时线程2 也进行了num++的操作，并且assign到工作内存，但是线程1 先到达总线，并且进行了加锁，由于mesi机制，线程2的工作内存数据失效，所以等线程2再次执行的时候，丢失了一次num++的操作，得到的数永远是<=2的。
```

![image-20201208181932132](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208181932132.png)

# 12.synchronized

```
synchronized用的锁是存放在java对象头中的。

java对象头：我就举32位虚拟机中的例子。如果对象是数组类型的，虚拟机用三个字宽，也就是12个字节来存储对象头，一般普通的对象，用两个字节存储对象头。对象头分为三个部分 MarkWord(存储对象的hashcode,age(分代年龄),锁信息，Gc标记。。。)，Kclass Word (存储到对象类型数据的指针)，if 数组 ArrarLength 数组的长度。


MarkWord 结构 
 ##Normal  25 hashcode  4 age 1 biased_lock:0  01
 ##Biased  23 thread 2 epoch 4 age 1 biased_lock:1  01 
 ##轻量级锁  30 锁记录指针 00
 ##重量级锁  30重量级锁的指针 10
 
```

![image-20201208143243932](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208143243932.png)

![image-20201208150032280](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208150032280.png)

**可以通过openjdk下的jol来查看**

![image-20201208150123093](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208150123093.png)

![image-20201208150408763](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208150408763.png)

# 13.Object类下有哪些方法？

![image-20201208200329026](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208200329026.png)

# 14.基本数据类型

![image-20201208200658829](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201208200658829.png)

# 15.java的自动拆箱与装箱

```
自动装箱就是Java自动将原始类型值转换成对应的对象，比如将int的变量转换成Integer对象，这个过程叫做装箱，反之将Integer对象转换成int类型值，这个过程叫做拆箱。因为这里的装箱和拆箱是自动进行的非人为转换，所以就称作为自动装箱和拆箱。原始类型byte, short, char, int, long, float, double 和 boolean 对应的封装类为Byte, Short, Character, Integer, Long, Float, Double, Boolean。

自动装箱时编译器调用valueOf将原始类型值转换成对象，同时自动拆箱时，编译器通过调用类似intValue(),doubleValue()这类的方法将对象转换成原始类型值。


public static Integer valueOf(int i) {
  //判断i是否在-128和127之间，如果不在此范围，则从IntegerCache中获取包装类的实例。否则new一个新实例
  if (i >= IntegerCache.low && i <= IntegerCache.high)
      return IntegerCache.cache[i + (-IntegerCache.low)];
      return new Integer(i);
}
```

# 16.String的常量池，StringBuilder,StringBuffer

1.8

https://blog.csdn.net/qq_41884976/article/details/83353389

https://blog.csdn.net/weixin_40208575/article/details/104528388

```
字符串的分配，和其他的对象分配一样，耗费高昂的时间与空间代价，大量频繁的创建字符串，极大程度地影响程序的性能
所以我们使用字符串常量池充当一个缓存的作用。

直接使用字面量生成的字符串，如果在常量池中没有，会生成一份放入常量池，之后再获取就从常量池拿。

intern():当调用 intern 方法时，如果池已经包含一个等于此 String 对象的字符串（该对象由 equals(Object) 方法确定），则返回池中的字符串；否则，将此 String 对象添加到池中，并且返回此 String 对象的引用

好处：
“String.intern()方法可以使得所有含相同内容的字符串都共享同一个内存对象,可以节省内存空间。

String的底层char类型的value数组是通过final关键字修饰的，不可变，每次对String的操作都会生成新的String对象，效率低下并且浪费内存空间。

当对字符串进行修改的时候，特别是字符串对象经常改变的情况下，需要使用 StringBuffer 和 StringBuilder 类。

和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。
底层都使用可变的char类型的value数组

StringBuilder 速度快 不安全  StringBuffer底层方法上都加了synchronized关键字，加锁，速度慢，安全
```

# String的底层

![image-20201215184009659](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215184009659.png)

![image-20201215184338671](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215184338671.png)

# String拼接操作和StringBuilder的append操作

```
StringBUilder的append方法，只会创建一个StringBuilder对象，以及toString()时new()一个对象

使用字符串拼接时，如果是变量，底层是会先new 一个StringBuilder()，toString()new一个对象，所以会重复new 很多对象

改进：
  如果基本确定前后添加的字符串大小不高于某个限定值(highLevel),建议使用构造器
  StringBuilder s = new StringBuilder(highLevel);//new char[highLevel]
  这样的好处是，避免了数组的扩容操作，节省了时间，提高效率
```

# intern()以及new String（）生成的对象

![image-20201217180315063](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217180315063.png)

https://www.bilibili.com/video/BV1PJ411n7xZ?p=126

```
直接new String("a") 会在字符串常量中有"a" ldc指令(将常量压入栈中)

然而StringBuilder()的toString()虽然return new String(,,,),但是字节码中没有ldc，说明没有在常量池中生成
```

![image-20201215201547453](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215201547453.png)

![image-20201215202511856](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215202511856.png)

![image-20201217180248447](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217180248447.png)

# 17.jdk,jre,jvm

```
jdk ==> java development kit java开发工具包
jre ==> java runtime environment java运行时环境
jvm ==> java Virtual Machine java虚拟机

jdk是给开发人员用的，jre和jvm是给普通用户用的，

jdk中包含jre,在JDK的安装目录下又一个jre的目录，目录下有bin和lib两个文件夹，bin可以理解为就是jvm,lib就是jvm工作所需要加载的类库，jvm和lib合起来就是jre

jdk是整个java的核心，包括了java运行环境JRE,java工具(javac/java等)和java基础的类库

JRE是Java运行环境，并不是一个开发环境，所以没有包含任何开发工具（如编译器和调试器）
```

# 18.类加载机制

```
类加载子系统负责从文件系统或者网络上加载Class文件，class文件在头位置带有特定的文件标识。CA FE BA BE

ClassLoader负责class文件的加载，能否运行，是由执行引擎决定的。

加载的类信息存放于一块称为方法区的内存空间。除了类信息，方法区还会存放运行时的常量池信息，可能还包括字符串常量，数字常量等。。
```

![image-20201209150541220](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209150541220.png)

![image-20201209151937370](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209151937370.png)

没有类变量和静态代码快不会有<Clinit>方法

![image-20201209152638163](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209152638163.png)

## 类加载器

```
1.引导类加载器 Bootstrap Class Loader
  ##这个类是由c实现的，嵌套在JVM的内部。
  ##用来加载java的核心类库（JAVA_HOME/jre/lib/rt.jar(String就在rt.jar下)、resources.jar或者sun.boot.class.path路径下内容），用于提供JVM自身需要的类
  ##不需要继承java.lang.ClassLoader，没有父加载器
  ##加载扩展类和应用程序类加载器，并指定为他们的父类加载器
  ##安全考虑，只会加载java,javax,sun开头的类
  
2.扩展类加载器
  ##间接继承于ClassLoader类
  ##父类加载器为启动类加载器
  ##从java.exit.dirs系统属性所指定的目录中加载类库，或从jdk的安装目录jre/lib/ext的子目录下加载类库，
    如果用户创建的Jar放在此目录下，会由该加载器加载。
3.系统类加载器
  ##间接继承于ClassLoader类
  ##父类加载器为启动类加载器
  ##负责加载环境变量classpath或者系统属性java.class.path下指定的类库
  ##是程序中默认的类加载器  ClassLoader.getSystemClassLoader()获得
  

```

![image-20201209155318185](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209155318185.png)

![image-20201209155618282](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209155618282.png)

![image-20201209160501290](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209160501290.png)

## 自定义类加载器

https://blog.csdn.net/huazai30000/article/details/85296671

```
因为系统的ClassLoader只会加载指定目录下的class文件,如果你想加载自己的class文件,那么就可以自定义一个ClassLoader.

而且我们可以根据自己的需求，对class文件进行加密和解密。

1..可以继承ClassLoder,然后重写findClass()方法，方法内获取对应class文件的字节码数组，然后通过defineClass()方法将字节码数组转换成Class类的实例

2.也可以直接继承URLClassLoader类,避免findclass()方法的处理。
```

## 获取ClassLoader的几种方法

![image-20201209214416798](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209214416798.png)

## 类的主动使用和被动使用

![image-20201209221241613](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209221241613.png)

# 运行时数据区

https://blog.csdn.net/weixin_42762133/article/details/95735737

![image-20201214195953401](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214195953401.png)

![image-20201214200120487](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214200120487.png)

## PC寄存器

![image-20201214203741615](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214203741615.png)



### PC寄存器的常见面试问题

![image-20201214205739362](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214205739362.png)

![image-20201214210051904](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214210051904.png)

## java虚拟机栈

### 栈中可能出现的Error

![image-20201214211743871](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214211743871.png)

### -Xss+size 1m  256k .....设置的是虚拟机栈大小

### 栈帧的内部结构

![image-20201214214046956](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201214214046956.png)

### 局部变量表

#### slot的理解(如果当前帧是由构造方法或实例方法创建的，this(最先放)将会存放在Index为0的位置)

![image-20201215103736298](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215103736298.png)

![image-20201215103756182](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215103756182.png)

#### slot的重复利用

![image-20201215104736817](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215104736817.png)

![image-20201215104926529](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215104926529.png)

### 操作数栈

![image-20201215110359317](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215110359317.png)

**代码解释**

bipush 就是将byte类型的数转为Int再进行存入 sipush就是将short类型转为Int再存入

![image-20201215110933433](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215110933433.png)



![image-20201215111036672](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215111036672.png)

### 栈顶缓存技术

![image-20201215125752084](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215125752084.png)

### 动态链接

```
也可以称为指向运行时常量池的方法引用
```

![image-20201215130453046](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215130453046.png)

![image-20201215130711786](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215130711786.png)

### 早期绑定跟晚期绑定

![image-20201215132209898](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215132209898.png)

### 虚方法跟非虚方法

```
继承中会有虚方法，如果子类中没有加super，就不确定调用的是子类的还是父类的，所以是虚方法。

特殊情况：final，不加super，也显示为虚方法，其实调用的还是父类的，因为没法重写(本质是special的)，当然你加了super,肯定是显示invokespecial的
```

![image-20201215133626792](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215133626792.png)

### 静态语言跟动态语言

![image-20201215134210784](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215134210784.png)

### 虚方法表

```
非虚方法不需要，因为是固定位置的
```

![image-20201215135848325](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215135848325.png)

![image-20201215140116925](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215140116925.png)

### 方法返回地址

```
如果异常返回，需要用到异常处理表，根据它来进行相应的操作
```

## Heap

### TLAB

```
所有线程是可以共享java堆的

但是堆里面也是有一块线程私有的缓冲区(Thread local Allocation Buffer),这块内容不是共享的
```

### 细分内存结构

```
1.8之后分为新生区+老年区+元空间 (之前是永久区)
```

![image-20201215145647287](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215145647287.png)

### 堆空间大小设置及查看

```
默认情况下 堆：初始内存大小是物理电脑内存（可用）/64  最大： .../4


查看方式1：jps找到线程id 然后jstat -gc 线程号
查看方式2：加参数 -XX:+PrintGCDetails
```

![image-20201215153606319](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215153606319.png)

![image-20201215153951648](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215153951648.png)

### 新生代跟老年代的堆结构参数

![image-20201215160210701](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215160210701.png)

### MinGC,MajorGC,FullGC

![image-20201215163231620](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215163231620.png)

#### minGc触发机制

![image-20201215163341372](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215163341372.png)

#### majorGc触发机制

![image-20201215164141543](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215164141543.png)

#### /FullGc触发机制

![image-20201215164254830](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201215164254830.png)

# 19.各种排序算法

# 20.双亲委派机制

```
优势：
  1.避免了类的重复加载(只有一个加载器能加载)
  2.保护程序安全，防止核心Api被随意篡改
如果在项目中，新建了java.lang包，再新建一个String.class,使用的时候并不会使用自己的，而是会向上委托。
如果在项目中，自定义了java.lang.xxx随便名字的类，也会报错，因为java.lang包下归引导类加载器管，在引导类加载器中没有该类，就会报错，这是出于安全的考虑 java.lang.SecurityException:Prohibited package name : java.lang
```

![image-20201209214922169](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209214922169.png)

# 21.沙箱安全机制

```
面试的时候就直接举列子说明
```

![image-20201209220725873](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201209220725873.png)

# 22.数据库的隔离级别

# 23.mysql

## 索引

```
在生产环境中，我们遇到最多的，也是最容易出问题的，还是一些复杂的查询操作，因此对查询语句的优化显然是重中之重。索引是存储引擎用于快速找到记录的一种数据结构。
```

## 为什么主键一般选择自动递增

```
更好的维护叶子节点，因为叶子节点必须是递增的双向链表，直接一直放到后面就可以，提高insert的效率，不然可能引起分裂。就需要向上合并这颗b树
```

## ACID及其原理

```
原子性：Undo log是为了实现事务的原子性，在mysql的innodb引擎中，还使用Undo log来实现多版本控制(MVCC)。

在操作任何数据库之前，首先将数据备份到一个地方(这个存储数据的地方就是undo log)，然后进行数据的修改，如果过程中出现了错误或者rollback了，系统可以利用undo log的备份将数据恢复到事务开始前的状态。

undo log 是逻辑日志:
  可以这样理解==》当delete/update/insert一条记录时，undo log中记录对应的操作
  
持久性：
   redo log是实现持久性的原理
```

## 数据更新流程&&redo log两阶段提交

https://www.cnblogs.com/f-ck-need-u/p/9010872.html#auto_id_1

https://www.cnblogs.com/wupeixuan/p/11734501.html

![image-20201211154431803](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211154431803.png)

![image-20201211154402106](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201211154402106.png)

# 24.mybatis

# 25.值传递跟引用传递

https://blog.csdn.net/u014745069/article/details/86649062

```
值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

总结：
值传递是传递实参副本，函数修改不会影响实参；引用传递是传递实参地址，函数修改会影响实参。

java中只有值传递，传递的是对象的地址，当我们函数中传递引用类型时，原本对象会发生变化，但是传递的对象地址没有发生变化，区分引用传递和值传递的关键是在于实参是否被修改，地址没有被修改。如果你修改了地址，新建了一个对象，是不会对原来对象有影响的，这也就证明了是值传递！

```

# 26.封装 继承 多态

https://blog.csdn.net/qq_43304572/article/details/82949989

```
封装可以隐藏实现细节，使得代码模块化；

继承可以扩展已存在的代码模块（类）；它们的目的都是为了——代码重用。

多态则是为了实现另一个目的——接口重用！多态的作用，就是为了类在继承和派生的时候，保证使用“家谱”中任一类的实例的某一属性时的正确调用。
```

# 27.hash码相等,equals一定为true嘛？

```
1.首先解释一下hash码是啥：
  类的hashCode()方法继承自Object类，因此每个对象都有一个默认的散列码，他的值为对象的存储地址（由对象的物理存储地址通过散列转换来的）。
  
2.一般情况下，如果重写了equals方法的话，就需要重写hashcode.

3.hashcode相等的两个元素不一定equals就相等，equals相等的元素大多数情况下hashcode相等。特殊情况 "通话"，“重地”,它们hashcode相同，但是equals却是不相同的。

4.一般集合中都需要两个方法都重写，因为如果只重写了equals，可能会出现一个集合元素中，出现多次相同的数据，因为hashcode没有重写，是object原生的。
```

# 28.BIO，NIO，AIO基本概念及区别

# 29.GC

```
1.为什么要进行GC？
 垃圾就是指没用指针引用的对象。
 一方面如果不进行垃圾回收，内存迟早被消耗完
 另一方面：除了释放没用的对象外，垃圾回收也可以清除内存的记录碎片。碎片整理将所用的堆内存移动到堆的一端，以便JVM将整理出的内存分配给新的大对象。
 
```

## 标记阶段

https://blog.csdn.net/luzhensmart/article/details/81431212

```
1.引用计数法(java没有使用)
会有循环引用问题

2.可达性分析算法(java使用) 也叫跟搜索算法，追踪性垃圾收集
为了解决循环依赖问题，在Java中采取了 可达性分析法。该方法的基本思想是通过一系列的“GC Roots”对象作为起点进行搜索，如果在“GC Roots”和一个对象之间没有可达路径，则称该对象是不可达的，不过要注意的是被判定为不可达的对象不一定就会成为可回收对象。被判定为不可达的对象要成为可回收对象必须至少经历两次标记过程，如果在这两次标记过程中仍然没有逃脱成为可回收对象的可能性，则基本上就真的成为可回收对象了。

在Java语言中，可作为GC Roots的对象包含以下几种：

1.虚拟机栈(栈帧中的本地变量表)中引用的对象。(可以理解为:引用栈帧中的本地变量表的所有对象)
2.方法区中静态属性引用的对象(可以理解为:引用方法区该静态属性的所有对象)
3.方法区中常量引用的对象(可以理解为:引用方法区中常量的所有对象)
4.本地方法栈中(Native方法)引用的对象(可以理解为:引用Native方法的所有对象)

```

![image-20201217185750051](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217185750051.png)

**循环引用**

![image-20201217190019050](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217190019050.png)

**只有对象在不可触及的情况下才可以被回收**

```
finalize()方法中的逻辑可能会让对象进行复活的，所以在可达性root到对象没有路径的时候，是不能立即进行回收的

finalize()只调用一次
```

![image-20201217192759111](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217192759111.png)

**两次标记的过程**

![image-20201217193316439](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217193316439.png)

## 清除阶段

### 标记清除算法

![image-20201217201610255](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217201610255.png)

![image-20201217202227036](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217202227036.png)

### 复制算法(用于年轻代。s0 s1区域复制最合适，因为存活的对象较少)

![image-20201217202919581](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217202919581.png)

![image-20201217203033141](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217203033141.png)

### 标记压缩算法(一般用于老年代)(标记整理算法)

![image-20201217204414894](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217204414894.png)

![image-20201217204848142](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217204848142.png)

### 三种算法的对比

![image-20201217205242246](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217205242246.png)

## 增量收集算法

```
让垃圾收集线程跟用户线程交替执行，而不是stop the world，用户线程全部停止


缺点：线程切换和上下文转换的消耗，会使垃圾回收的总体成本增加，造成吞吐量下降
```

![image-20201217210644581](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201217210644581.png)

## 分区算法

![image-20201227201213324](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227201213324.png)

## 指针碰撞跟空闲列表

```
1.指针碰撞
假设Java堆中内存时完整的，已分配的内存和空闲内存分别在不同的一侧，通过一个指针作为分界点，需要分配内存时，仅仅需要把指针往空闲的一端移动与对象大小相等的距离。使用的GC收集器：Serial、ParNew，适用堆内存规整（即没有内存碎片）的情况下。

2.空闲列表法
事实上，Java堆的内存并不是完整的，已分配的内存和空闲内存相互交错，JVM通过维护一个列表，记录可用的内存块信息，当分配操作发生时，从列表中找到一个足够大的内存块分配给对象实例，并更新列表上的记录。使用的GC收集器：CMS，适用堆内存不规整的情况下。
```

## System.gc()方法的理解

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227202429670.png)

## 内存泄漏

```
只有当对象不被程序使用到，并且Gc又不能把回收他们的情况，叫做内存泄漏

举例：1.单例对象，对象的生命周期跟应用程序一样长，如果引用了外部的对象，并且已经不再使用，此时也无法回收该外部对象
     2.类似于数据库连接，网络连接等没有调用close()方法也会造成内存泄漏
```

## Stop The World

![image-20201227211112429](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227211112429.png)

```
无论哪款垃圾收集器都会有这种现象，只是做了优化而已
```

![image-20201227211220368](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227211220368.png)

## 垃圾回收的并行与串行

![image-20201227211902969](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227211902969.png)

![image-20201227212053248](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227212053248.png)

# 30.线程池

https://www.bilibili.com/video/BV16J411h7Rd?p=206

### 线程池五种状态

![image-20201218140324997](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201218140324997.png)

![image-20201218140621714](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201218140621714.png)



```
最高位是1==>代表的是负数  所以是最小的

###为什么不用两个整数，一个表示线程池状态，一个表示线程数量的原因？

这些信息都存储在一个原子变量ctl中，目的是将线程池状态与线程个数合二为一，这样就可以用一次cas操作进行赋值
```



![image-20201222120228208](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201222120228208.png)

# 31.进程间的通信方式以及线程间的通信方式

https://blog.csdn.net/qq_41333582/article/details/83988370

## 共享存储

![image-20201220193701472](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220193701472.png)

## 管道通信

![image-20201220194051598](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220194051598.png)

## 消息传递

![image-20201220194330251](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201220194330251.png)

# 32.Redis持久化机制

## RDB

```
Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据(变量，环境变量，程序计数器等)数值都是和原进程保持一致的，但是是一个全新的进程，并作为原进程的子进程。

保存在dump.rdb文件中

默认有三个save机制
 save 60 10000 1分钟万次
 save 300 10  5分钟10次
 save 900 1  15分钟1次
如果想要禁用 直接 save ""


触发的四个时机：
  1 在指定的时间间隔内，执行指定次数的写操作
  2 执行save（阻塞， 只管保存快照，其他的等待） 或者是bgsave （异步）命令
    ##save 跟 bgsave的区别 
       save只管保存，不管其他，全部阻塞。
       bgsave会让redis在后台异步进行操作，快照同时还可以响应客户端请求
  3 执行flushall 命令，清空数据库所有数据，意义不大。
  4 执行shutdown 命令，保证服务器正常关闭且不丢失任何数据，意义...也不大。
  
如何恢复：
 将dump.rdb文件赋值到redis的安装目录下并启动服务即可
如何修复：
  redis-check-dump  --fix  xxrdb文件
 2据完整性和一致性要求不高
##劣势：意外宕机，会丢失最后一次快照后所有的修改，
       fork的时候，内存中的数据被克隆了一份，如果原数据很大的话就很浪费性能
```

![image-20201223203908024](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201223203908024.png)

## AOF

```
默认是关闭的 appendonly yes 就行

如果在aof文件里手动添加了错误的信息，会启动不了redis服务。

dump.rdb跟aof文件共存时，启动的时候会先加载aof文件。

使用redis-check-aof --fix aof文件名 来自动修复，会删除不符合语法的信息

持久化策略：
AppendFsync + 策略
  1.Always 每次发生数据变更都会被立即记录到磁盘 性能差数据完整性比较好
  2.Everysec:每秒记录，如果一秒内宕机，丢失一秒内的数据
  3.no
  
重写ReWrite机制：

AOF采用文件追加的方式，文件会越来越大，为避免这种状况，新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis会启动AOF文件的内容压缩。只会保留可以恢复数据的最小指令集，可以使用命令bgrewriteaof

触发机制：
redis会记录上次重写时AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大小大于64Mb后触发。


优势：保证数据的完整性那块更好一点，可以调整持久化的策略。

劣势：AOF是采用追加模式的，所以aof文件会较大。
     AOF的效率要慢于RDB的效率
```

![image-20201224135801691](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201224135801691.png)

# 33.Spring源码

![image-20201224152933749](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201224152933749.png)

## BeanFactoryPostProcessor

```
处理Bean的定义信息，

比如我们在配置数据库连接信息时<bean id = datasource>
                           <property name="driverClassName"                                                            value="${}"></property>
                         </bean>
   我们需要在xml文件中加入 <context:property-placeholder location="classpath:db.properties"/>
去jdbc.properties文件去取数据，这些数据是需要在bean被实例化之前就要取得的，

所以在实例化之前可以对beanDefinition信息进行处理  PlaceholderConfigurerSupport是BeanFactoryPostProcessor的实现类可以处理
```

## BeanPostProcessor

## Refresh()方法

```java
synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			prepareRefresh();

       创建的是DefaultListableBeanFactory
			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

                调用执行BeanFactoryPostProcessors
				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

                注册BeanPostProcessors
				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);
           
                国际化
				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

                实例化Bean对象 
				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```

# 34.java中的引用

![image-20201227213126163](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227213126163.png)

## 强引用

![image-20201227213626295](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227213626295.png)

## 软引用

![image-20201227213836833](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227213836833.png)



## 弱引用

![image-20201227214809610](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227214809610.png)

## 虚引用

```
追踪垃圾回收的过程。
```

![image-20201227215533366](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227215533366.png)

![image-20201227215750395](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227215750395.png)

## 终结器引用(了解一下即可)

![image-20201227220436771](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201227220436771.png)

# 35.Redis数据结构底层

http://www.360doc.com/content/20/0513/18/58006001_912127432.shtml

## bitmaps使用

```
最大长度为512MB 2^32位，本质还是string

setbit key index value
getbit index
bitcount key (from end) 默认统计1的个数

场景：
朋友圈点赞
set 朋友圈id 点赞人id 1 
```

## 布隆过滤器

```
解决缓存穿透问题
```

### google布隆过滤器

```
<dependency>  
  <groupId>com.google.guava</groupId>  
  <artifactId>guava</artifactId>  
  <version>19.0</version>  
</dependency>
```

```
是基于jvm内存的一种布隆过滤器，重启之后会失效
本地内存无法使用在分布式场景下
不支持大数据量的存储
```

## Redis布隆过滤器

## Zset底层(跳表)

https://blog.csdn.net/wangxuelei036/article/details/106272680/

```
是一种有序的数据结构，它通过在每个节点中维持多个指向其他节点的指针（可以理解为维护了多条路径），从而达到快速访问节点的目的。

1.每层都是一条有序的链表
2.维护了多条节点路径。
3，最底层的链表包含了所有元素。
4.平均查找为o(logn),最坏为o(n)

使用到两个结构zskiplist以及zskiplistNode

zskiplist: 1.header:指向跳跃表的表头节点
            2.tail:指向跳跃表的表尾节点
            3.level:记录跳跃表层数最大的节点的层数(表头不计算在内)
            4.跳跃表目前包含节点的数量(表头节点不计算在内)
zskiplistNode:
层(level)：节点中用L1、L2、L3等字样标记节点的各个层，L1代表第一层，L2代表第二层，依次类推。每个层都带有两个属性：前进指针和跨度。前进指针用于访问位于表尾方向的其他节点，而跨度则记录了前进指针所指向节点和当前节点的距离。

当程序从表头向表尾进行遍历时，访问会沿着层的前进指针进行。

后退(backward)指针：节点中用BW字样标记节点的后退指针，它指向位于当前节点的前一个节点。后退指针在程序从表尾向表头遍历时使用。

分值(score)：各个节点中的1.0、2.0和3.0是节点所保存的分值。在跳跃表中，节点按各自所保存的分值从小到大排列。

成员对象(obj)：各个节点中的o1、o2和o3是节点所保存的成员对象。
注意表头节点和其他节点的构造是一样的：表头节点也有后退指针、分值和成员对象，
```

