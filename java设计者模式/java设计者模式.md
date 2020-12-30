# 静态代理

## 继承实现

被代理对象

```
public class UserDaoImpl {
    public void query(String name){
        System.out.println("query name ="+name);
    }
}

```

代理对象

```
package lunban.proxy;

import lunban.dao.UserDaoImpl;

public class LogUserProxy extends UserDaoImpl {
    @Override
    public void query(String name) {
        System.out.println("log name= "+name);
        System.out.println("query name ="+name);

    }
}

```

测试

```
public class Test1 {


    public static void main(String[] args) {
        UserDaoImpl userDao=new LogUserProxy();
        userDao.query("cc");
    }
}

```

## 聚合实现

公共的接口

```
public interface UserDao {
    public void query(String name);
}

```

被代理对象

```
package lunban.dao;

public class UserDaoImpl implements UserDao{
    public void query(String name){
        System.out.println("query name ="+name);
    }
}
```

日志代理类

```
package lunban.proxy2;

import lunban.dao.UserDao;
import lunban.dao.UserDaoImpl;

public class LogUserProxy implements UserDao {
    public UserDao userDao;
    public LogUserProxy(UserDao userDao){
        this.userDao=userDao;
    }
    public void query(String name) {
        System.out.println("log...");
      userDao.query(name);
    }
}
```

时间代理类

```
package lunban.proxy2;

import lunban.dao.UserDao;

public class TimerUserProxy implements UserDao {
    public UserDao userDao;
    public TimerUserProxy(UserDao userDao){
        this.userDao=userDao;
    }
    public void query(String name) {
        System.out.println("Timer...");
      userDao.query(name);
    }
}
```

测试类

```
public class Test1 {


    public static void main(String[] args) {
//        UserDaoImpl userDao=new LogUserProxy();
//        userDao.query("cc");
        UserDao userDao=new UserDaoImpl();
        UserDao proxy1=new lunban.proxy2.LogUserProxy(userDao);
        UserDao proxy2=new TimerUserProxy(proxy1);
         proxy2.query("zhangsan");
//        Timer...
//        log...
//        query name =zhangsan

    }
}
```

# 动态代理

![image-20200628125957232](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200628125957232.png)

# 设计模式7大原则

```
1.单一职责原则
2.接口隔离原则
3.依赖倒转原则
4.里氏倒转原则
5.开闭原则 ocp
6.迪米特法则
7.合成复用原则
```

## 单一职责

![image-20201107220658250](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107220658250.png)

## 接口隔离

![image-20201107221527055](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107221527055.png)

## 依赖倒转

**面向接口编程**

![image-20201107221934089](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107221934089.png)

## 里式替换原则

**继承时，子类尽量不要去修改父类的方法，不然会对继承体系造成破坏**

![image-20201107222904158](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107222904158.png)

![image-20201107223349453](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107223349453.png)

![image-20201107224211509](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107224211509.png)

## 开闭原则

![image-20201107224543599](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201107224543599.png)



## 迪米特法原则(最少知道原则)

![image-20201108201538145](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201108201538145.png)

## 合成复用原则

```
尽量使用合成/聚合的方式 ，而不是使用继承
```

# 设计模式分类

![image-20201108205820107](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201108205820107.png)

# 1.单例模式(8种)

![image-20201108210013831](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201108210013831.png)

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:02
 * 基于静态常量的饿汉式（线程安全，反射不安全，反序列化不安全）
 */
public class Singleton1 {
    //构造器私有化
      private Singleton1(){

      }

      private final static Singleton1 instance= new Singleton1();

      //提供公共方法获取实例
      public static Singleton1 getInstance(){
          return instance;
      }
}

```

```
package com.cbx.Singleton;

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

    public static synchronized Singleton5 getInstance(){
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

```
package com.cbx.Singleton;

/**
 * @Author:cbx
 * @Date:2020/11/08/21:25
 * 枚举法
 * 线程安全，防止反序列化问题 (推荐使用******)
 */
public enum Singleton7 {
   INSTANCE;
}

```

## jdk中的RunTime类就使用的是饿汉式

![image-20201108213129258](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201108213129258.png)

# 2.工厂模式

```
简单工厂

工厂方法

抽象工厂
```

[参考博客](https://blog.csdn.net/xiaoddt/article/details/74937952?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf)

