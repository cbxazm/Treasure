# String基本操作以及应用场景

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

**单值缓存，对象缓存，分布式锁**

![image-20201116213005474](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116213005474.png)

![image-20201116213216713](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116213216713.png)

# Hash基本操作及应用场景

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

![image-20201116215827655](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116215827655.png)

![image-20201116220347428](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116220347428.png)

![image-20201116221834012](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116221834012.png)

# list的基本操作及应用场景

**双端队列，可以作为栈使用**

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

**阻塞队列相当于java中的阻塞队列，没有元素的时候pop()就阻塞住,等待放入数据**

![image-20201116223415524](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116223415524.png)

![image-20201116223933483](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201116223933483.png)

# set的基本操作及应用场景

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

![image-20201117210825414](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201117210825414.png)

![image-20201117211611841](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201117211611841.png)

# Zset的基本操作及应用场景

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

![image-20201117212358296](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201117212358296.png)

# redis实现分布式锁

## 初步实现

![image-20201119204124316](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119204124316.png)

![image-20201119204326282](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119204326282.png)

**这把分布式锁，在高并发场景下会失效，如果线程1执行15 秒，第10秒锁就失效了**

**线程2进来，执行10秒 ， 此时线程1 会把线程2的锁删除掉 ，后面进来的线程就不会有锁的效果，**

**所以锁就失效了**

![image-20201119204840361](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119204840361.png)

## 初步解决锁失效问题

```
在高并发场景下还是会出现问题，因为锁在一定时间内会失效，这里不好控制

如果设置时间长，发生死锁的话，就会影响性能，秒杀用户就一直等着

提供一种方案

进来的线程再开辟一个看门狗线程即定时任务线程，监控该线程有没有释放锁，如果没有的话，就重新设置锁的过期时间

但是实现起来是有一定的复杂性的
```

![image-20201119205947814](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119205947814.png)

## 使用redisson线程的框架，可以解决上面的问题

### 引入依赖

```
<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.6.5</version>
</dependency>
```

### 配置

![image-20201119211227293](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119211227293.png)

### redisson分布式锁实现原理

![image-20201119212935388](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119212935388.png)

![image-20201119212751390](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119212751390.png)

![image-20201119212135054](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119212135054.png)

## redis主从复制的分布式锁问题(######)

```
当master的锁设置好之后，还没有来得及复制到slave，此时主节点挂了

线程1 已经执行了

线程2进来的时候 ，使用的是新的master节点 ，此时又需要进行加锁操作了 


因为redis保证高可用性，主节点加锁后，立即返回给客户端成功 ，还没来的及去复制到slave节点
所以会出现问题
```

![image-20201119214113960](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119214113960.png)

### 解决方案1 使用zookeeper(推荐使用)

```
保证slave节点都接收到加锁的信息后 ， 才会返回给客户端 

所以就算挂了主节点也不会影响slave节点加锁
```

### 解决方案2 使用redlock (不推荐)

![image-20201119214512796](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201119214512796.png)