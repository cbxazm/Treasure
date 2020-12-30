# Rabbitmq简介

**基于AMQP协议**

```
以熟悉的电商场景为例，如果商品服务和订单服务是两个不同的微服务，在下单的过程中订单服务需要调用商品服务进行扣库存操作。按照传统的方式，下单过程要等到调用完毕之后才能返回下单成功，如果网络产生波动等原因使得商品服务扣库存延迟或者失败，会带来较差的用户体验，如果在高并发的场景下，这样的处理显然是不合适的，那怎么进行优化呢？这就需要消息队列登场了。

消息队列提供一个异步通信机制，消息的发送者不必一直等待到消息被成功处理才返回，而是立即返回。消息中间件负责处理网络通信，如果网络连接不可用，消息被暂存于队列当中，当网络畅通的时候在将消息转发给相应的应用程序或者服务，当然前提是这些服务订阅了该队列。如果在商品服务和订单服务之间使用消息中间件，既可以提高并发量，又降低服务之间的耦合度。

RabbitMQ就是这样一款我们苦苦追寻的消息队列。RabbitMQ是一个开源的消息代理的队列服务器，用来通过普通协议在完全不同的应用之间共享数据。
```

# 应用场景

```
1.异步处理。把消息放入消息中间件中，等到需要的时候再去处理。
2.流量削峰。例如秒杀活动，在短时间内访问量急剧增加，使用消息队列，当消息队列满了就拒绝响应，跳转到错误页面，这样就可以使得系统不会因为超负载而崩溃。
3.日志处理
4.应用解耦。假设某个服务A需要给许多个服务（B、C、D）发送消息，当某个服务（例如B）不需要发送消息了，服务A需要改代码再次部署；当新加入一个服务（服务E）需要服务A的消息的时候，也需要改代码重新部署；另外服务A也要考虑其他服务挂掉，没有收到消息怎么办？要不要重新发送呢？是不是很麻烦，使用MQ发布订阅模式，服务A只生产消息发送到MQ，B、C、D从MQ中读取消息，需要A的消息就订阅，不需要了就取消订阅，服务A不再操心其他的事情，使用这种方式可以降低服务或者系统之间的耦合。
```

# AMQP协议

![image-20200902143359249](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902143359249.png)

![image-20200902210832762](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902210832762.png)

# rabbitmq的安装

![image-20200902143950999](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902143950999.png)

## 普通安装

**过于繁琐**

## docker安装

**参考浏览器中的学习博客**

```
docker pull rabbitmq:3.7.18-management
docker run -d --hostname localhost --name myrabbit -p 15672:15672 -p 5672:5672 rabbitmq:3.7.18-management
```

使用guest,guset账户密码来登录(使用docker 默认就是开启来宾账户的)

![image-20200902154826027](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902154826027.png)

## 7大模型

https://www.rabbitmq.com/getstarted.html

![image-20200902182010416](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902182010416.png)

![image-20200902182023619](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902182023619.png)

<img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902182033882.png" alt="image-20200902182033882" style="zoom:50%;" />

## java整合rabbitmq

### 引入依赖

```
 <!--引入amqp的依赖-->
    <dependency>
      <groupId>com.rabbitmq</groupId>
      <artifactId>amqp-client</artifactId>
      <version>5.7.2</version>
    </dependency>
```

# 第一种模型(直连)

![image-20200902211441061](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200902211441061.png)

```
1.p：生产者，就是发送消息的程序
2.c:消费者，消息的接受者，会一直等待消息到来
3.红色部分是消息队列，类似一个邮箱，可以缓存消息，生产者投递消息，消费者从中取出消息
```

## 1.开发生产者

```
package HelloWorld;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.MessageProperties;
import org.junit.Test;
import utils.RabbitmqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @Author:cbx
 * @Date:2020/09/02/21:17
 * 生产者
 */
public class Provider{

    @Test
    public void testSendMessage() throws IOException, TimeoutException {
        Connection connection = RabbitmqUtils.getConnection();
        //获取连接中的通道对象
        Channel channel = connection.createChannel();
        //通道绑定对应的消息队列
        //参数1：队列名称，如果没有该队列,那就自动生成该队列
        //参数2：用来定义队列是否持久化 false不持久化，关闭rabbitmq就会没有该队列（就算持久化了队列，消息在这里还是不能持久化）
        //参数3：用来定义是否独占队列 false不独占，独占就是指只有该连接可以使用
        //参数4：用来定义是否在消费完成后自动删除队列 true:自动删除 false：不自动删除(断了连接后才会删除)
        //参数5：附加参数
        channel.queueDeclare("hello",true,false,false,null);

        //发布消息
        //第一个参数是交换机名称
        // 第二个是消息队列的名称
        //参数3 传递消息的额外设置（MessageProperties.PERSISTENT_TEXT_PLAIN,重启服务后，队列的消息还在）
        //参数4：消息的具体内容
        channel.basicPublish("","hello", MessageProperties.PERSISTENT_TEXT_PLAIN,"hello rabbitmq".getBytes());
       RabbitmqUtils.closeConnectionAndChannel(channel,connection);
    }
}


```

## 2.开发消费者

```
package HelloWorld;

import com.rabbitmq.client.*;
import org.junit.Test;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @Author:cbx
 * @Date:2020/09/02/21:40
 * 消费者
 */
public class Customer {
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建连接mq的连接工厂对象
        ConnectionFactory connectionFactory = new ConnectionFactory();
        //设置连接rabbit的主机
        connectionFactory.setHost("192.168.48.128");
        //设置端口号
        connectionFactory.setPort(5672);
        //设置连接哪个虚拟主机
        connectionFactory.setVirtualHost("/cbxhost");
        //设置访问虚拟主机的用户名和密码
        connectionFactory.setUsername("cbx");
        connectionFactory.setPassword("131224abcd");
        //获取连接对象
        Connection connection = connectionFactory.newConnection();
        //获取连接中的通道对象
        Channel channel = connection.createChannel();
        channel.queueDeclare("hello", false, false, false, null);

        //消费消息
        //参数1 ：消费哪个队列的消息
        //参数2：开启消息的自动确认机制
        //参数3：消费时的回调接口
        channel.basicConsume("hello", true, new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("new String(body) =" + new String(body));
            }
        });
//        channel.close();
//        connection.close();

    }
}

```

## 3.封装工具类

```
package utils;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @Author:cbx
 * @Date:2020/09/02/22:06
 */
public class RabbitmqUtils {
    private static ConnectionFactory connectionFactory;
    static {
        //重量级资源，类加载的时候进行，并且只执行一次
        connectionFactory=new ConnectionFactory();
        //设置连接rabbit的主机
        connectionFactory.setHost("192.168.48.128");
        //设置端口号
        connectionFactory.setPort(5672);
        //设置连接哪个虚拟主机
        connectionFactory.setVirtualHost("/cbxhost");
        //设置访问虚拟主机的用户名和密码
        connectionFactory.setUsername("cbx");
        connectionFactory.setPassword("131224abcd");
    }
    /**
     * 定义提供连接对象的方法
     * @return
     */
    public static Connection getConnection(){
        try {
            //获取连接对象
            Connection connection = connectionFactory.newConnection();
            return connection;
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
        return null;
    }
    /**
     * 定义关闭连接的方法
     */
    public static void closeConnectionAndChannel(Channel channel,Connection connection){
            try {
                if (channel!=null){
                    channel.close();
                }
                if (connection!=null){
                    connection.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            } catch (TimeoutException e) {
                e.printStackTrace();
            }
    }
}


```

