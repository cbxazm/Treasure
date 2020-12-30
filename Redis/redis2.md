# redis的基础知识

redis共有16个数据库

select  0-15 来选择数据库

flushdb来清空数据库的数据

FLUSHALL清空全部数据库的数据

# 五大数据类型

## String

```
set key value 设置值
exists key  是否存在
expire key 秒数 设置key的过期时间
del key 删除某个key
ttl key 查看key的剩余时间  -1代表永久有效
type key 查看key是什么类型的
append key value  在指定key后追加 (如果当前key不存在，就相当于set一个key)
strlen key 获取字符串的长度
decr key key自减1
incr key key自增1
incrby key 10 自增10 
getrange key 0 3 获取从0位置到3位置之间的字符串 (收尾都包含的)
getrange key 0 -1 获取全部的字符串
setrange key 1 ** 将key1位置开始的字符串替换为** 比如cbx 在0位置开始就是 **x
setex(set with expire) set key 过期时间 value 设置带有过期时间的key
setnx(set if not exist) setnx key value 如果该key不存在才会设置，不然不会操作
mset k1 v1 k2 v2 批量设置key-value
mget k1 k2 k3 批量get到key的值
msetnx k1 v1 k2 v2 批量设置，如果key不存在才设置，这是一个原子性的操作，要么都成功,要么都失败
##设置对象
mset user:1:name zhangsan user:1:age 2
getset key value 先获取到key的值，再去set key的值为value （如果当前key为空，返回nil）
```

## List

list实际是一个双端链表，可以操作为栈，队列，阻塞队列

可以存在重复值

栈操作

```
127.0.0.1:6379> lpush name aa bb cc   //插到列表的头部
(integer) 3
127.0.0.1:6379> lrange name 0 -1
1) "cc"
2) "bb"
3) "aa"
127.0.0.1:6379> 

###
127.0.0.1:6379> rpush name aa bb cc  //插到列表的尾部
(integer) 3
127.0.0.1:6379> lrange name 0 -1
1) "aa"
2) "bb"
3) "cc"
127.0.0.1:6379> 


###
lpop key  从左边移除一个元素
rpop key  从右边移除一个元素
lrem key 移除的数量 value    lrem list 2 one 就是移除两个one


##
lindex key 1 获取下标为1的值

##
llen key  获取list的长度

##ltrim

127.0.0.1:6379> lrange name 0 -1
1) "aa"
2) "bb"
3) "ee"
4) "cc"
127.0.0.1:6379> ltrim name 1 2  //截取1-2位置的字符串
OK
127.0.0.1:6379> lrange name 0 -1
1) "bb"
2) "ee"

##组合命令
rpoplpush 移除key1最后一个元素，并放到新的列表key2中去
rpoplpush key1 key2 

##lset

lset key index value 将指定下标的值替换成新的值(不存在值会报错)

##linsert 向指定的数据前或者后插入数据

127.0.0.1:6379> linsert name before ee aa
(integer) 3
127.0.0.1:6379> lrange name 0 -1
1) "bb"
2) "aa"
3) "ee"
127.0.0.1:6379> 



```



## Set

跟java类似，是无序的集合

```
127.0.0.1:6379> sadd name a b c
(integer) 3
127.0.0.1:6379> SMEMBERS name
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> SISMEMBER b  //判断某值是否存在于set集合中
127.0.0.1:6379> SISMEMBER name b
(integer) 1

scard key #查看集合中元素的个数

srem key value #删除set集合中的某个元素

SRANDMEMBER name count #随机获取指定集合中指定数量的元素（不写count默认就是1）

spop key 随机删除指定key集合里的一个元素

smove key1 key2 value 将key1中的指定value移动到key2中


##集合的共同元素 比如微博的共同关注。。。
sdiff key1 key2 求出key1中有的，key2中不存在的(求差集)
sinter key1 key2（求交集）
sunion key1 key2（求并集）



```

## Hash

key-map (存的value是Map集合)

```
127.0.0.1:6379> hset myhash name zhangsan age 15
(integer) 2
127.0.0.1:6379> hget myhash name
"zhangsan"
127.0.0.1:6379> hget myhash age
"15"
127.0.0.1:6379> hgetall
(error) ERR wrong number of arguments for 'hgetall' command
127.0.0.1:6379> hgetall myhash //以key-value的形式展示全部的数据
1) "name"
2) "zhangsan"
3) "age"
4) "15"

hmset 批量插入
hmset key filed value[.....]

##删除指定的field
hdel myhash field

##获取多少个键值对
hlen myhash

##hexists myhash filed  查看指定的字段是否存在

##hkeys 查看所有的字段值

##hvals 查看所有的value值

##hincrby myhash filed 3 将指定的hash中的key的value值增加3

##hsetnx myhash filed value 如果不存在filed才创建，存在就不操作，跟字符串的setnx是一样的
```

应用场景：**存储表更的数据 比如 hset user:1 name xx age **,特别是用户的信息**

## Zset（有序集合）

在set集合的基础上增加了一个权值

```
zadd myzset 1 aa 2 bb 3 cc 存起来会根据权值的大小存储

##正序输出数据
127.0.0.1:6379> zrange myzset 0 -1
1) "one"
2) "two"
3) "three"

##逆序输出数据
zrevrange myzset 0 -1

##带权值的逆序输出数据
127.0.0.1:6379> ZREVRANGE myzset 0 -1 withscores
1) "three"
2) "3"
3) "two"
4) "2"
5) "one"
6) "1"

##返回不带权值
127.0.0.1:6379> ZRANGEBYSCORE myzset -inf +inf
1) "one"
2) "two"
3) "three"

##返回带权值的数据
127.0.0.1:6379> ZRANGEBYSCORE myzset -inf +inf withscores (也可以指定区间如-100 200)
1) "one"
2) "1"
3) "two"
4) "2"
5) "three"
6) "3"
127.0.0.1:6379> 

##删除集合中的元素
zrem myzet value

##zcard myzet 获取有序集合中的个数

##zcount myzset 1 100 获取权值在1-100中间的元素数量

```

# 三种特殊的数据类型

## geospatial地理位置

底层是用zset来实现的，可以使用zset的基本命令来操作该类型的数据

可以推算地理位置的信息，以及方圆几里的人。

城市经纬度查询http://www.jsons.cn/lngcode/

```
//添加城市的经纬度
geoadd china 118.72 32.06 nanjing

//获取指定位置的经纬度
geopos china nanjing


//获取集合中指定范围（半径）的元素
georadius china 118 32 1000 km [WITHDIST(显示到中心的距离信息),WITHCOORD(显示该数据的经纬度信息),count 数量 (显示几个)]

//获取集合中指定元素周围的元素
GEORADIUSBYMEMBER china nanjing 100 km


//将二维的hash转换成一维的字符串（11个字符长度）字符串越接近，距离越近
geohash china nanjing
```

## Hyperloglog

**基数**(不重复的元素)

A{1,2,3,4,5}

B{1,2,3,4}

A，B合并就是{1,2,3,4,5}

![image-20201019203737067](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019203737067.png)

## Bitmaps 

位存储

![image-20201019204320243](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019204320243.png)

```
setbit key index value
getbit index
bitcount key (from end) 默认统计1的个数
```

# redis的基本事务操作

![image-20201019211415466](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019211415466.png)

```
multi -->开启事务
exec -->执行事务
```

![image-20201019211620101](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019211620101.png)

**放弃事务**

![image-20201019211744107](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019211744107.png)

## 异常

编译期异常（代码有问题，命令有错），事务中所有的命令都不会被执行

![image-20201019212119403](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019212119403.png)

**运行时异常(事务队列中存在语法特性，那么执行命令时，其他命令可以正常执行，错误命令抛出异常)**

![image-20201019212342682](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019212342682.png)

# redis实现乐观锁

**redis的监视测试**

正常情况下

![image-20201019213111923](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019213111923.png)

**测试多线程修改值（watch可以当redis的乐观锁操作）**

![image-20201019213230979](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019213230979.png)

**事务执行失败，就去先解锁**

![image-20201019214405541](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019214405541.png)

# jedis

**导入依赖**

![image-20201019215247332](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201019215247332.png)

# springboot整合

![image-20201021145435900](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021145435900.png)

![image-20201021150623836](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021150623836.png)

```
#redis
spring.redis.port=6379
spring.redis.host=localhost
spring.redis.database=1

##对于jedis，2.x以后有些配置没有补全

```

## 生成的key是unicode编码

默认序列化是用jdk的，会转义字符串

![image-20201021151454902](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021151454902.png)

## 自定义配置redisTemplate

```
package com.cbx.springboot_redis.config;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.net.UnknownHostException;

/**
 * @Author:cbx
 * @Date:2020/10/21/15:17
 */
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate();
        redisTemplate.setConnectionFactory(redisConnectionFactory);
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

# Redis.conf详解

## 网络

![image-20201021155811280](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021155811280.png)

## 通用general

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021160346293.png)

## 快照

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021160638946.png)

## 安全

![](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021160852285.png)

## 限制

![image-20201021161037678](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021161037678.png)

## aof配置

![image-20201021161212244](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021161212244.png)

# Redis持久化(***)RDB

redis是内存数据库，如果不及时将数据状态保存到磁盘，断电就会丢失数据

# AOF

# redis发布订阅

![image-20201021203107365](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021203107365.png)

![image-20201021203150988](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021203150988.png)

![image-20201021203200689](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021203200689.png)

![image-20201021203428609](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021203428609.png)

# 主从复制

![image-20201021203947316](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021203947316.png)

## 作用

![image-20201021204141424](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021204141424.png)

## 搭建集群环境

1.生成三个redis.conf

![image-20201021212654354](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021212654354.png)

```
修改里面对应的
1.port
2.dump.rdb的名字  -->dump80.rdb ... 用端口命名
3.logfile  用端口.log命名
4.pidfile 名字也是用端口来处理

```

2.启动三个服务

```
redis-server ../redis79.conf 
redis-server ../redis80.conf 
redis-server ../redis81.conf 
```

3.启动三个客户端

```
[root@localhost bin]# redis-cli -p 6379 -a 131224abcd
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info replication //查看主从的信息
# Replication
role:master
connected_slaves:0
master_replid:8be45cff9f52907b5b7c30c4f0daaca9a6a1963f
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
127.0.0.1:6379> 

```

# 哨兵模式

**自动将从库转换为主库**

![image-20201021215119607](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021215119607.png)

![image-20201021215237229](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021215237229.png)

![image-20201021215322434](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021215322434.png)

![image-20201021215435009](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021215435009.png)

![image-20201021220057953](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220057953.png)

# Redis的缓存穿透和雪崩(*********)

**缓存穿透是因为查不到导致的**

**缓存击穿是因为查的量太大导致的**

![image-20201021220407084](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220407084.png)



![image-20201021220423985](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220423985.png)

## 解决方案1.布隆过滤器

![image-20201021220521685](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220521685.png)

## 解决方案2.缓存空对象

![image-20201021220630968](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220630968.png)

### 存在的问题

![image-20201021220650470](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220650470.png)

# 缓存击穿(查询量太大，缓存过期导致的)

![image-20201021220950502](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021220950502.png)

# 雪崩

![image-20201021221214580](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021221214580.png)

## 解决方案

![image-20201021221342393](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201021221342393.png)