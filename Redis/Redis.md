# Redis是什么

![image-20200514091500214](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514091500214.png)

## 特点

![image-20200514095133103](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514095133103.png)

##  缺点

![image-20200514100238388](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514100238388.png)

# web2.0的难题

![image-20200514092626054](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514092626054.png)

# NOSQL的类别

## 键值存储数据库

![image-20200514094012392](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514094012392.png)

## 列存储数据库

![image-20200514094403044](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514094403044.png)

## 文档型数据库

![image-20200514094427354](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514094427354.png)

## 图形数据库

![image-20200514094643214](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514094643214.png)

# Redis的应用场景

![image-20200514095314746](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514095314746.png)

![image-20200514095701953](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514095701953.png)



![image-20200514095823950](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514095823950.png)

# Redis安装

## gcc安装

### 出现端口被占用情况

再进行安装就可以了

```
rm -f /var/run/yum.pid
```

c语言的编译器

```
yum -y install gcc automake autoconf libtool make
```

## 解压到/opt目录下

```
tar zxvf .... -C /opt
```

## 编译

cd 进入该目录

```
make 进行编译
```

## 指定安装位置

必须在/opt下那个编译后的redis目录下使用下面的命令

如果没有redis目录，会自动创建的

```
make PREFIX=/usr/local/redis install
```

## 查看安装后的文件

![image-20200514144502535](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514144502535.png)

## 启动服务端

在当前的bin目录下

```
./redis-server
```

## 启动客户端

在当前的bin目录下

```
./redis-cli
```

# Redis配置详解

redis默认定义了默认的配置，但在实际开发中一般都会通过手动配置完成

## 配置redis

将解压目录下的redis.conf配置文件复制到安装文件的目录下

```
cp /opt/XXXXX/redis.conf /usr/local/redis
```

## 配置后台运行

![image-20201013223133134](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201013223133134.png)

## 查看redis进程

```
ps -ef| grep redis
```

## 关闭redis

```
在客户端直接shutdown命令 即可关闭服务端和客户端
```

## redis自带的压力测试工具redis-benchmark（了解即可）

## redis.conf

### 前10个

![image-20200514151545717](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514151545717.png)

![image-20200514151703372](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514151703372.png)

![image-20200514151919337](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514151919337.png)

### 中间10个

![image-20200514152411297](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514152411297.png)

![image-20200514152635426](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514152635426.png)

![image-20200514152659479](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514152659479.png)

### 结尾10个

![image-20200514152802261](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514152802261.png)

![image-20200514152815697](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514152815697.png)

## Redis中的内存维护策略

### 方案一

为数据设置超时时间(比如验证码这些数据，多少秒过后就没有用了)

如果没有设置超时时间，默认为-1 就是永远不会过期，一加载就会在内存里占用

如果设置了过期时间，之后又想让缓存永不过期，使用persist key

```
expier key time(seconds)
```

## 方案二

采用LRU算法动态将不用的数据删除

![image-20200514165354150](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514165354150.png)

## 自定义配置Redis

![image-20200514165731540](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514165731540.png)

### 启动的时候需要带上自定义配置文件的位置

![image-20200514170243202](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514170243202.png)

## 使用客户端shutdown方式关闭

可以保证数据保存了

直接在客户端shutdown

# Centos防火墙设置

## 查看已经开放的端口

```
firewall-cmd --list-ports
```

## 开启端口

```
firewall-cmd --zone=public --add-port=6379/tcp --permanent
```

## 重启防火墙

```
firewall-cmd --reload
systemctl stop firewalld.service #停止
systemctl disable firewalld.service #禁止firewall开机启动
```



# Redis远程连接

## 修改配置

设置外面的机器可以连接

![image-20200514201646678](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514201646678.png)

必须设置密码

![image-20200514201843548](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514201843548.png)

## 指定配置文件来启动redis服务端

![image-20200514202015257](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514202015257.png)

## 指定密码开启redis客户端

![image-20200514202406841](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514202406841.png)

## 打开远程连接工具(百度网盘)

![image-20200514202857554](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200514202857554.png)

# Docker安装redis

```
docker search redis
docker pull redis:4.0.1
docker run -d --name redis6379 -p 6379:6379 redis:4.0.1 --requirepass "131224abcd" --appendonly yes  //开启数据持久化，不然关闭之后下次连接没有数据   //启动并且设置了密码
docker exec -it redis6379 redis-cli -a 131224abcd

```

## 启动之后远程连接的就是这个

# Redis命令

## 常用命令key管理

![image-20200515123301460](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515123301460.png)

## String类型

![image-20200515132833514](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515132833514.png)

### 赋值

![image-20200515133235296](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515133235296.png)

### 取值

![image-20200515133359963](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515133359963.png)

### 删值及自增自减操作

![image-20200515134137340](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515134137340.png)

### 应用场景

#### 保存单个字符串或json字符串

#### 保存图片文件的内容

#### 计数器:微博数，粉丝数

## Hash类型

### 赋值取值

![image-20200515144207968](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515144207968.png)

### 删值

![image-20200515144224210](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515144224210.png)

### 其他语法

![image-20200515145638911](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515145638911.png)

### 应用场景

#### 存储对象信息

#### 为什么不用String存储字符串

![image-20200515150919312](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515150919312.png)

# 常见的redis客户端介绍及对比

![image-20200515151200484](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200515151200484.png)

## SpringBoot整合Jedis(项目bootjedis)

### 引入jedis依赖

```
<!--引入jedis-->
		<dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
			//springboot默认就是引入了jedis,无需再声明版本号
		</dependency>
```

### application.yml

```

server:
  port: 8080
spring:
  redis:
    host: 192.168.48.128
    password: 131224abcd
    port: 6379
    jedis:
      pool:
        max-idle: 6 #最大空闲数
        max-active: 10  #最大连接数
        min-idle: 2   #最小空闲数
    timeout: 2000ms  #连接超时
```



### 编写config

```
package com.cbx.bootjedis.config;

import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.util.logging.Logger;

//因为jar包不是springboot-starters下的，所以需要自己配置
@Configuration  //相当于一个配置文件
public class JedisConfig {
  private org.slf4j.Logger logger=LoggerFactory.getLogger(JedisConfig.class);
    @Value("${spring.redis.host}")
    private String host;

    @Value("${spring.redis.port}")
    private int port;

    @Value("${spring.redis.password}")
    private String password;

    @Value("${spring.redis.timeout}")
    private String timeout;

    @Value("${spring.redis.jedis.pool.max-active}")
    private int maxActive;

    @Value("${spring.redis.jedis.pool.max-idle}")
    private int maxIdle;

    @Value("${spring.redis.jedis.pool.min-idle}")
    private int minIdle;
    @Bean
    public JedisPool jedisPool(){
        JedisPoolConfig jedisPoolConfig=new JedisPoolConfig();
        jedisPoolConfig.setMaxIdle(maxIdle);
        jedisPoolConfig.setMinIdle(minIdle);
        jedisPoolConfig.setMaxTotal(maxActive);
        timeout=timeout.substring(0,timeout.length()-2);
        int timeout1 = Integer.parseInt(timeout);
        JedisPool jedisPool=new JedisPool(jedisPoolConfig,host,port,timeout1,password);
       logger.info("jedisPool连接成功"+host+"\t"+port);
        return jedisPool;

    }
}


```

## 操作String类型

### 编写业务层

```
package com.cbx.bootjedis.service.impl;

import com.cbx.bootjedis.service.UserService;
import lombok.extern.java.Log;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;

/**
 * redis的String类型操作
 * redis什么命令 ==》jedis就是什么方法
 * 需求：用户输入一个key
 * 先判断在redis中是否存在该数据
 * 如果存在，在redis中进行查询，并返回
 * 如果不存在，在mysql中进行查询，将结果赋值给redis，并返回
 */
@Service
@Log
public class UserServiceImpl implements UserService {
    @Autowired
    private JedisPool jedisPool; //jedis的连接池

    @Override
    public String getString(String key) {
        String value=null;
//         得到jedis对象
        Jedis jedis = jedisPool.getResource();
       if(jedis.exists(key)){
              log.info("查询redis中的数据");
              value=jedis.get(key);

       }else {
           value="mysql中的数据";
           log.info("查询mysql数据库"+value);
//           将值赋值给redis
           jedis.set(key,value);

       }
//       关闭连接
        jedis.close();
        return value;

    }
}
```



### 编写测试类

```
package com.cbx.bootjedis;

import com.cbx.bootjedis.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import redis.clients.jedis.JedisPool;

@SpringBootTest
class BootjedisApplicationTests {
	@Autowired
   private JedisPool jedisPool;
	@Autowired
	private UserService userService;
	@Test
	void contextLoads() {
		System.out.println(jedisPool);
	}
//	模拟jedis操作String类型的数据
	@Test
	void t1(){
		String name = userService.getString("mysql");
		System.out.println(name);
	}

}

```



### 编写jedis工具类

```
package com.cbx.bootjedis.config;

import org.springframework.beans.factory.annotation.Autowired;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;

public class JedisUtil {
    @Autowired
    private JedisPool jedisPool;
    /**
     * 获取Jedis资源
     */
    private Jedis getJedis(){
        return jedisPool.getResource();
    }
    /**
     * 释放jedis连接
     */
    public void close(Jedis jedis){
        if (jedis!=null){
            jedis.close();
        }
    }
    /**
     * 。。。。。还可以自己定义很多业务逻辑的方法
     */
}

```

## 操作Hash类型

### 编写业务层

```
 @Override
    public User selectById(String id) {
        String key="user"+id;
        User user=new User();
//        得到jedis对象
        Jedis jedis=jedisPool.getResource();
        if (jedis.exists(key)){
            //存在
            log.info("查询的是redis的数据");
            Map<String, String> map = jedis.hgetAll(key);
              user.setId(map.get("id"));
              user.setName(map.get("name"));
              user.setAge(Integer.parseInt(map.get("age")));
        }else {
           user=new User();
            user.setId(id);
            user.setName("张三");
            user.setAge(18);
            log.info("查询的是Mysql数据库"+user);
            //存入redis
            Map<String,String> map=new HashMap<>();
             map.put("id",user.getId());
             map.put("name",user.getName());
             map.put("age",user.getAge().toString());
            jedis.hmset(key,map);
            log.info("向redis中存入数据");
        }
        jedis.close();
        return user;
    }
```

### 编写测试类

```
	//	模拟jedis操作Hash类型的数据
	@Test
	void t2(){
		User user = userService.selectById("1");
		System.out.println(user);
	}
```

# SpringBoot整合lettuce

## 引入依赖

```
	<!--springboot默认的就是lettuce客户端-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
		<!--redis依赖commons-pool 这个依赖一定要添加-->
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-pool2</artifactId>
		</dependency>
```

## application.yml

```
server:
  port: 8081
spring:
  redis:
    host: 192.168.48.128
    password: 131224abcd
    port: 6379
    lettuce:
      pool:
        max-idle: 6 #最大空闲数
        max-active: 10  #最大连接数
        min-idle: 2
      shutdown-timeout: 2000ms  #连接超时

```

## 编写config

```
package com.cbx.bootlettuce.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {
    /**
     * RedisTemplate配置
     * @param connectionFactory
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(LettuceConnectionFactory connectionFactory) {
        // 配置redisTemplate
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(connectionFactory);
      //String的类型
        redisTemplate.setKeySerializer(new StringRedisSerializer());//key序列化
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());//value序列化
//        Hash的类型
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }

}

```

## 操作String类型

### 编写业务层

```
package com.cbx.bootlettuce.Service.Impl;

import com.cbx.bootlettuce.Service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
@Slf4j
 public class UserServiceImpl implements UserService {
    @Autowired
   private RedisTemplate<String,Object> redisTemplate;
    @Override
    public String getString(String key) {
        String val="redistemplate测试数据";
//      log.info("redistemplate==>测试"+redisTemplate);
        if (redisTemplate.hasKey(key)){
//         存在
            log.info("redis中查出来的");
            return (String) redisTemplate.opsForValue().get(key);
        }else {
          log.info("mysql中查出来的");
          redisTemplate.opsForValue().set(key,val);
          return val;
        }
    }
    //添加key的有效时间  28小时
    @Override
    public void expireKey(String key, String value) {
         redisTemplate.opsForValue().set(key,value);
//         设置为28天
         redisTemplate.expire(key,28, TimeUnit.HOURS);
    }
}


```



### 编写测试类

```
	@Test
	void test2(){
		String str = userService.getString("redistemplateStr");
		System.out.println(str);
	}
```

## 操作Hash类型

### 编写业务层

```
  @Override
    public User selectById(String id) {
        //user:id user:1
        User user=null;
        if ( redisTemplate.opsForHash().hasKey("user",id)){
            user = (User) redisTemplate.opsForHash().get("user", id);

        }else {
            log.info("查询mysql数据库");
             user=new User();
            user.setAge(20);
            user.setId(id);
            user.setName("李四");
            redisTemplate.opsForHash().put("user",id,user);

        }
        return user;
    }
```



### 编写测试类

```
	@Test
	void test3(){
		User user = userService.selectById("1");
		System.out.println(user);
	}
```

# List数据类型

![image-20200517142955738](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517142955738.png)

## 赋值取值操作

![image-20200517143821865](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517143821865.png)

## 删除操作

![image-20200517144546898](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517144546898.png)

## 修改操作

before|after 后面加的是值，不是索引

![image-20200517144848320](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517144848320.png)

## 高级操作

![image-20200517145119110](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517145119110.png)

## 业务层

```
package com.cbx.bootlettuce.Service.Impl;

import com.cbx.bootlettuce.pojo.Article;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.ListOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

@Service
@Slf4j
public class ListCacheImpl {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    @Resource(name = "redisTemplate")
    private ListOperations<String, Article> opsForList;

    public void initArticle() {
        String key = "article:top5";
//        redisTemplate.opsForList();
        List<Article> list = new ArrayList<>();
        for (int i = 1; i < 6; i++) {
            Article article = new Article();
            article.setId("1000" + i);
            article.setTitle("文章标题" + i);
            article.setContent("文章数据内容" + i);
            article.setClickNum(new Random().nextInt(1000) + 1);
            article.setAuthor("张飞");
            article.setCreateDate(getCreateDate());
            list.add(article);
        }
        log.info("存入redis的指定key中");
      opsForList.rightPushAll(key,list);
    }

    /**
     * 查询主页显示的五个文章
     * @return
     */
      public List<Article> selectArticleTop5(){
          String key="article:top5";
          List<Article> range = opsForList.range(key, 0, 4);
          return range;
      }

    /**
     * 添加文章
     *  @return 返回值是列表的长度
     */

    public  long insertArticle(Article article){
          String key="article:top5";
          return opsForList.leftPush(key,article);
    }

    public String getCreateDate() {
        LocalDateTime localDateTime = LocalDateTime.now();
        String format = localDateTime.format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
        return format;
    }

}
//

```

## 测试类

```
package com.cbx.bootlettuce;

import com.cbx.bootlettuce.Service.Impl.ListCacheImpl;
import com.cbx.bootlettuce.pojo.Article;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
public class ListCacheTest {
    @Autowired
    private ListCacheImpl listCache;

    /**
     * 初始化文章信息
     */
    @Test
    void t1(){
        listCache.initArticle();
    }
    /**
     * 获取top5文章
     */
    @Test
    void t2(){
        List<Article> articles = listCache.selectArticleTop5();
        System.out.println(articles);
    }
}

```



## 应用场景

### 对数据量大的集合数据进行删减

![image-20200517145747102](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517145747102.png)

### 任务队列

![image-20200517145804711](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517145804711.png)

### 案列1

![image-20200517145940691](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517145940691.png)

# Set类型数据

![image-20200517161646179](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517161646179.png)

## 赋值取值操作

![image-20200517161848235](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517161848235.png)

## 删除操作

![image-20200517161906230](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517161906230.png)

## 差集交集并集操作

![image-20200517162130296](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517162130296.png)

# ZSet集合(有序集合)

![image-20200517162617442](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517162617442.png)

## 赋值取值删除操作

![image-20200517163939508](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517163939508.png)

## 应用场景

![image-20200517165638748](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517165638748.png)

# HyperLogLog

![image-20200517170739430](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517170739430.png)

## 为什么使用

![image-20200517170821862](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517170821862.png)

## 常用命令

![image-20200517170954161](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517170954161.png)

## 应用场景

![image-20200517171122974](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517171122974.png)

# Redis发布订阅

![image-20200517171802834](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517171802834.png)

![image-20200517171812622](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517171812622.png)

## 常用命令

![image-20200517195117100](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517195117100.png)

## 应用场景

![image-20200517195156614](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517195156614.png)

# Redis多数据库

![image-20200517195246516](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517195246516.png)

# 数据库清空

flushdb:清空当前数据库的所有key

flushall:清空整个redis的数据库所有的key

#  Redis事务

![image-20200517195918979](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517195918979.png)

## 常用命令

![image-20200517200001918](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517200001918.png)

## 事务的三个阶段

开始事务

命令入队

执行事务

## 案例1

![image-20200517201343051](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517201343051.png)

## 事务的错误处理

### 情况一(报错的命令不执行，其他都执行)

![image-20200517202401141](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517202401141.png)

### 情况二(队列中出现了报告错误，整个队列都会被取消)

![image-20200517221612625](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517221612625.png)

## 事务的watch

![image-20200517222015965](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517222015965.png)

# Redis持久化

## RDB

![image-20200517222646764](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517222646764.png)

### 快照条件

![image-20200517222747546](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517222747546.png)

## AOF

![image-20200517223009233](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517223009233.png)

![image-20200517223125433](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517223125433.png)

### 三种方式

![image-20200517223144531](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517223144531.png)

### 产生的问题

![image-20200517223208218](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517223208218.png)

# Redis缓存与数据库的一致

## 实时同步

![image-20200517224040320](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517224040320.png)

## 非实时同步

![image-20200517225305652](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517225305652.png)

## 异步队列

![image-20200517225326730](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517225326730.png)

![image-20200517225603993](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200517225603993.png)

# Mysql主从复制

![image-20200518153135926](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518153135926.png)

![image-20200518153209088](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518153209088.png)

# 阿里同步工具canal

![image-20200518153309985](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518153309985.png)

# 缓存穿透

![image-20200518162044394](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518162044394.png)

# 雪崩

![image-20200518162332009](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518162332009.png)

## 解决方案

![image-20200518162359105](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518162359105.png)

# 热点key

![image-20200518162624848](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518162624848.png)

# redis高级配置

## 可能的问题

![image-20200518163225339](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518163225339.png)

## Redis主从复制

![image-20200518163527433](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518163527433.png)

### 好处

![image-20200518163710917](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518163710917.png)

## 创建从数据库

### 配置文件redis.conf

![image-20200518163951592](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518163951592.png)

### 启动从数据库

![image-20200518164015848](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518164015848.png)

### 登录到从数据库

![image-20200518164125033](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518164125033.png)

# 哨兵模式

![image-20200518164803102](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200518164803102.png)