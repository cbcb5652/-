# Redis

## redis基本环境配置

首先下载redis（linux环境下的）：https://redis.io/download  建议下载5.0.8版本的

```bash
$ wget http://download.redis.io/releases/redis-6.0.3.tar.gz
$ tar xzf redis-6.0.3.tar.gz
$ cd redis-5.0.8
$ make
```

进入redis-5.0.8文件夹之后。

```bash
yum install gcc-c++

make

make install
```

安装成功后，如下目录有文件：

![1589994594710](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589994594710.png)

## 测试性能

**redis-benchmark**是一个压力测试工具

![1589995229841](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589995229841.png) 

简单测试:

```bash
# 测试：100个并发连接   100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

![1589995513578](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589995513578.png)

## 基础的知识

redis默认有16个数据库

![1589995615871](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589995615871.png)

默认使用第0个数据库 

可以使用select进行切换数据库

```base
127.0.0.1:6379> select 3 #切换数据库
OK
127.0.0.1:6379[3]> dbsize	#查看数据库大小
(integer) 0
```

查看所有的key

```bash
keys *
```

清空数据库

```java
flushdb
```

清楚全部数据库的内容

```java
flushall
```

> Redis是单线程的！

CPU不是Redis的性能瓶颈，Redis的性能瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了！

Redis是C语言写的；100000+QPS

### Redis：为什么单线程还这么快？

运行速度：CPU>内存>硬盘

核心：redis是将所有的数据全部放在内存中，所以说使用单线程取操作效率就是最高的！多线程（CPU上下文切换：耗时

### redis五大数据类型

> 官方文档

![1589996462200](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589996462200.png)

 Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作==数据库==、==缓存==和==消息中间件==。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和[地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。 

#### Redis-Key

```bash
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> move name 1	//移除1库中的name
(integer) 1
127.0.0.1:6379> keys *	//查看所有key
(empty list or set)
127.0.0.1:6379> set name chenbin
OK
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> EXPIRE name 10	//设置当前过期事件
(integer) 1
127.0.0.1:6379> ttl name	//查看剩余的时间
(integer) 6
127.0.0.1:6379> ttl name
(integer) 1
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> type name	//查看字段类型
string
127.0.0.1:6379> type age
string
```

**官网帮助文档查看**

http://www.redis.cn/commands.html

#### String

```bash
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> get key1
"v1"
127.0.0.1:6379> keys *
1) "key1"
127.0.0.1:6379> EXISTS key1
(integer) 1
127.0.0.1:6379> APPEND key1 hello	#添加值,如果当前key不存在，就相当于set key
(integer) 7
127.0.0.1:6379> get key1
"v1hello"
127.0.0.1:6379> STRLEN key1	#获得value的长度
(integer) 7
127.0.0.1:6379> APPEND key1 chenbin
(integer) 14
127.0.0.1:6379> get key1
"v1hellochenbin"
```

```bash
## 截取字符串
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> INCR views	#自增1
(integer) 1
127.0.0.1:6379> INCR views	
(integer) 2
127.0.0.1:6379> get views 
"2"
127.0.0.1:6379> DECR vies	#自减1
(integer) -1
127.0.0.1:6379> DECR views
(integer) 1
127.0.0.1:6379> INCRBY views 10	#增加指定数值
(integer) 11
127.0.0.1:6379> INCRBY views 10
(integer) 21
127.0.0.1:6379> DECRBY views 5	#减少指定数值
(integer) 16
```

```bash
127.0.0.1:6379> set key1 "chenbinhello"	#设置key1的值
OK
127.0.0.1:6379> get key1
"chenbinhello"
127.0.0.1:6379> GETRANGE key1 0 3	#截取字符串
"chen"
127.0.0.1:6379> GETRANGE key1 0 -1
"chenbinhello"
```

指定位置替换

```java
127.0.0.1:6379> set key2 abcdefg
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> SETRANGE key2 1 xx
(integer) 7
127.0.0.1:6379> get key2
"axxdefg"
```



```bash
# setex (set with expire)	#设置过期时间
# setnx (set if not exist)	#不存在再设置 (在分布式锁中会常常使用!)
127.0.0.1:6379> SETEX key3 30 "hello"	#设置key3 的值，设置30秒过期
OK
127.0.0.1:6379> ttl key3	#查看过期剩余时间
(integer) 27
127.0.0.1:6379> get key3
"hello"
127.0.0.1:6379> SETNX mykey "redis"	#如果mykey存在 则创建失败
(integer) 1
127.0.0.1:6379> keys *
1) "mykey"
2) "key2"
3) "key3"
127.0.0.1:6379> ttl key3
(integer) -2
127.0.0.1:6379> setnx mykey "mongodb"
(integer) 0
127.0.0.1:6379> get mykey
"redis"

```

设置多个值

```
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3	#同时设置多个值
OK
127.0.0.1:6379> keys *
1) "k2"
2) "k1"
3) "k3"
127.0.0.1:6379> mget k1 k2 k3	# 同时获得多个值
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1 k4 v4	#原子性，要么一起成功 要么一起失败
(integer) 0
127.0.0
```

对象的操作

```bash
127.0.0.1:6379> mset user1:1:name zhangsan user1:1:age 2	
OK
127.0.0.1:6379> mget user1:1:name user1:1:age
1) "zhangsan"
2) "2"
127.0.0.1:6379> 
```

组合命令

```bash
127.0.0.1:6379> getset db redis	#如果不存在值 则返回null，并设置
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongodb	#存在则覆盖
"redis"
127.0.0.1:6379> get db
"mongodb"
```

#### List

所有的list操作都是l开头的

```bash
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> LPUSH list one	#将一个值或多个值，插入到列表头部 (左)
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1	#通过区间获取具体的值
1) "three"
2) "two"
127.0.0.1:6379> RPUSH list right	#将一个值或多个值，插入到列表头部(右)
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
```

LPOP RPOP

```bash
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
127.0.0.1:6379> LPOP list	# 移除list的第一个元素
"three"
127.0.0.1:6379> RPOP list	# 移除list的最后一个元素
"right"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
```

通过下标获得值

```bash
127.0.0.1:6379> LINDEX list 1
"one"
127.0.0.1:6379> LINDEX list 0
"two"
#获取列表长度
127.0.0.1:6379> LLEN list
(integer) 2
```

移除指定的value

```bash
127.0.0.1:6379> LREM list 1 one 
(integer) 1
127.0.0.1:6379> LREM list 1 three
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "four"
2) "two"
```

截取指定长度的元素

```bash
127.0.0.1:6379> RPUSH mylist "hello"
(integer) 1
127.0.0.1:6379> RPUSH mylist "hello1"
(integer) 2
127.0.0.1:6379> RPUSH mylist "hello2"
(integer) 3
127.0.0.1:6379> RPUSH mylist "hello3"
(integer) 4
127.0.0.1:6379> LTRIM mylist 1 2	#只截取指定的元素
OK
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello1"
2) "hello2"
```

移除列表最后一个元素，将他移动到新的列表中

```bash
127.0.0.1:6379> RPUSH mylist "hello"
(integer) 1
127.0.0.1:6379> RPUSH mylist "hello1"
(integer) 2
127.0.0.1:6379> RPUSH mylist "hello2"
(integer) 3
127.0.0.1:6379> RPOPLPUSH mylist myotherlist
"hello2"
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "hello1"
127.0.0.1:6379> LRANGE myotherlist 0 -1	# 查看目标列表中，确实存在该值
1) "hello2"
```

lset 将列表中指定的值替换为另外一个值

```bash
127.0.0.1:6379> LPUSH list value1
(integer) 1
127.0.0.1:6379> LRANGE list 0 0
1) "value1"
127.0.0.1:6379> lset list 0 item	#如果存在，更新当前下标的值
OK
127.0.0.1:6379> LRANGE list 0 0
1) "item"
127.0.0.1:6379> lset list 1 other
(error) ERR index out of range
```

linsert 将某个具体的value插入到列表中某个元素的前面或后面

```bash
127.0.0.1:6379> RPUSH mylist hello
(integer) 1
127.0.0.1:6379> RPush mylist world
(integer) 2
127.0.0.1:6379> LINSERT mylist before world other
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
127.0.0.1:6379> LINSERT mylist after world new
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
4) "new"
```

> 小结

* 实际上是一个双向链表，before Node after ，左右都可以插入
* 如果key不存在，创建新的链表
* 如果key存在，新增内容
* 如果移除了key，空链表，不存在
* 在两边插入或者改动值，效率最高！中间元素，相对来说，效率会低点~

#### Set

Set中的值是不能重复的！

```bash
127.0.0.1:6379> SADD myset hello	#set集合中添加元素
(integer) 1
127.0.0.1:6379> SADD myset chenbin
(integer) 1
127.0.0.1:6379> SADD myset loverongrong
(integer) 1
127.0.0.1:6379> SMEMBERS myset	#查看指定set的所有值
1) "hello"
2) "loverongrong"
3) "chenbin"
127.0.0.1:6379> SISMEMBER myset hello	# 判断set中的元素
(integer) 1
127.0.0.1:6379> SISMEMBER myset world
(integer) 0
```



```bash
127.0.0.1:6379> SCARD myset #获取集合中元素个数
(integer) 4
```

移除指定元素

```bash
127.0.0.1:6379> SREM myset hello
(integer) 1
127.0.0.1:6379> SCARD myset
(integer) 3
127.0.0.1:6379> SMEMBERS myset
1) "loverongrong"
2) "chenbin"
3) "loverongrong2"
```

set无序 不重复 抽随机

```bash
127.0.0.1:6379> SRANDMEMBER myset	#随机抽选出一个元素 ，后面加一个数目可以抽取指定个数元素	
"chenbin"
127.0.0.1:6379> SRANDMEMBER myset
"loverongrong"
127.0.0.1:6379> SRANDMEMBER myset
"chenbin"
127.0.0.1:6379> SRANDMEMBER myset
"chenbin"
127.0.0.1:6379> SRANDMEMBER myset
"loverongrong2"
```

删除指定的key，随机删除key

```bash
127.0.0.1:6379> SMEMBERS myset
1) "loverongrong"
2) "chenbin"
3) "loverongrong2"
127.0.0.1:6379> SPOP myset	# 随机删除一些set集合中的元素！
"loverongrong2"
127.0.0.1:6379> spop myset
"chenbin"
127.0.0.1:6379> SMEMBERS myset
1) "loverongrong"
```

将一个指定的值，移动到另外一个set集合中！

```bash
127.0.0.1:6379> 
127.0.0.1:6379> SADD myset chenbin
(integer) 1
127.0.0.1:6379> SADD myset hello
(integer) 1
127.0.0.1:6379> SADD myset world
(integer) 1
127.0.0.1:6379> sadd myset2 set2
(integer) 1
127.0.0.1:6379> SMOVE myset myset2 chenbin
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "hello"
2) "world"
127.0.0.1:6379> SMEMBERS myset2
1) "chenbin"
2) "set2"
```

微博，B站！共同关注！(并集)

数字集合类：

	- 差集
	- 交集
	- 并集

```bash
127.0.0.1:6379> SADD key1 a
(integer) 1
127.0.0.1:6379> SADD key1 b
(integer) 1
127.0.0.1:6379> SADD key1 c
(integer) 1
127.0.0.1:6379> SADD key2 c
(integer) 1
127.0.0.1:6379> SADD key2 d
(integer) 1
127.0.0.1:6379> SADD key2 e
(integer) 1
127.0.0.1:6379> SDIFF key1 key2	#差集
1) "b"
2) "a"
127.0.0.1:6379> SINTER key1 key2	#交集
1) "c" 
127.0.0.1:6379> SUNION key1 key2	#并集
1) "c"
2) "a"
3) "b"
4) "d"
5) "e"
```

#### Hash

Map集合，key-value集合！这时候值是一个map集合

```bash
127.0.0.1:6379> hset myhash field1 chenbin	#set一个具体的key-value
(integer) 1
127.0.0.1:6379> hget myhash field1	#获取一个字段值
"chenbin"
127.0.0.1:6379> hmset myhash field1 hello field2 world	#set多个具体的key-value
OK
127.0.0.1:6379> HMGET myhash field1 field2	#获取多个字段值
1) "hello"
2) "world"
127.0.0.1:6379> HGETALL myhash	#获取全部的数据
1) "field1"
2) "hello"
3) "field2"
4) "world"
127.0.0.1:6379> HDEL myhash field1	#删除指定的key
(integer) 1
127.0.0.1:6379> HGETALL myhash
1) "field2"
2) "world"
```

判断hash的长度

```bash
127.0.0.1:6379> HLEN myhash
(integer) 1
127.0.0.1:6379> HMSET myhash field1 hello field2 world
OK
127.0.0.1:6379> HGETALL myhash
1) "field2"
2) "world"
3) "field1"
4) "hello"
127.0.0.1:6379> HLEN myhash
(integer) 2
```

判断指定字段是否存在

```bash
127.0.0.1:6379> HEXISTS myhash field1
(integer) 1
127.0.0.1:6379> HEXISTS myhash field3
(integer) 0
```

只获得所有的field或value

```bash
127.0.0.1:6379> HKEYS myhash
1) "field2"
2) "field1"
127.0.0.1:6379> HVALS myhash
1) "world"
2) "hello"
```

指定自增，

```bash
127.0.0.1:6379> HSET myhash field3 5	#指定增量
(integer) 1
127.0.0.1:6379> HINCRBY myhash field3 1
(integer) 6
127.0.0.1:6379> HINCRBY myhash field3 -1
(integer) 5
127.0.0.1:6379> HSETNX myhash field4 hello	# 如果不存在则可以设置
(integer) 1
127.0.0.1:6379> HSETNX myhash field4 hello	# 如果存在则不能设置
(integer) 0
```

hash变更的数据 user name age 尤其是用户信息或经常变动的信息！ hash更适合对象的存储,String更加适合字符串存储

#### Zset(有序集合)

在set的基础上，增加了一个值，set k1 v1 | zset k1 score1 v1

```bash
127.0.0.1:6379> ZADD myset 1 one	#添加一个 
(integer) 1
127.0.0.1:6379> ZADD myset 2 two 3 three	#添加多个
(integer) 2
127.0.0.1:6379> ZRANGE myset 0 -1
1) "one"
2) "two"
3) "three"
```

排序如何实现

```bash
127.0.0.1:6379> ZADD salary 2500 xiaohong	#添加三个用户
(integer) 1
127.0.0.1:6379> ZADD salary 5000 zhangsan
(integer) 1
127.0.0.1:6379> ZADD salary 500 chenbin
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf	#现实全部的用户，从小到大
1) "chenbin"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores	#显示全部的用户，并且附带成绩
1) "chenbin"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores #显示公资小于2500员工的升序 排序！
1) "chenbin"
2) "500"
3) "xiaohong"
4) "2500"
```

移除rem中的元素

```bash
127.0.0.1:6379> ZRANGE salary 0 -1
1) "chenbin"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZREM salary xiaohong #移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> ZRANGE salary 0 -1
1) "chenbin"
2) "zhangsan"
127.0.0.1:6379> ZCARD salary	#获取集合中的元素
(integer) 2
```

```bash
127.0.0.1:6379> zadd myset 1 hello 
(integer) 1
127.0.0.1:6379> zadd myset 2 world 3 chenbin
(integer) 2
127.0.0.1:6379> ZCOUNT myset 1 3	#获取指定区间成员数量!
(integer) 3
127.0.0.1:6379> ZCOUNT myset 1 2
(integer) 2
```

案例思路:set 排序 存储班级成绩表，公资表排序！

普通消息，1 ，重要消息 2，带权重进行判断！

排行榜应用实现，取Top N 测试！

### Redis 三种特殊的数据类型

#### geospatial 地理位置

![1590042907633](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590042907633.png)

> getadd

```bash
# 添加指定的地理位置
#规则：两级无法直接添加，我们一般会下载城市数据，直接通过java程序一次性导入
#参数 key 值(维度，经度，名称)
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijin
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqing
(integer) 1
127.0.0.1:6379> geoadd china:city 114.05 22.52 shenzhen
(integer) 1
127.0.0.1:6379> geoadd china:city 120.16 30.24 hangzhou
(integer) 1
127.0.0.1:6379> geoadd china:city 108.96 34.26 xian
(integer) 1
```

> getpos

获得当前定位：一定是一个坐标值!

```bash
127.0.0.1:6379> GEOPOS china:city beijin	#获取指定的经度和维度
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> GEOPOS china:city hangzhou
1) 1) "120.1600000262260437"
   2) "30.2400003229490224"
```

> 

两人之间的距离

单位：

* m 表示单位为米
* km 表示单位为千米
* mi 表示单位为英里
* ft表示单位为英尺

```bash
127.0.0.1:6379> GEODIST china:city beijin shanghai	#查看北京到上海的直线距离
"1067378.7564"
127.0.0.1:6379> GEODIST china:city beijin hangzhou
"1127337.7813"
```

> georadius 以给定的经纬度为中心，找出某一半径内的元素

定位：通过半径来查询！

所有的数据都应该录入：china:city 才会让结果更加清晰!

```bash
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km	#以110，30 这个经纬度为中心，寻找方圆1000KM内的城市
1) "chongqing"
2) "xian"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withdist 	#显示到中间距离的位置
1) 1) "chongqing"
   2) "341.9374"
2) 1) "xian"
   2) "483.8340"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withcoord	#显示他人的定位信息
1) 1) "chongqing"
   2) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "xian"
   2) 1) "108.96000176668167114"
      2) "34.25999964418929977"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withdist withcoord count 1#筛选出指定的结果
1) 1) "chongqing"
   2) "341.9374"
   3) 1) "106.49999767541885376"
```

> GEORADIUSBYMEMBER

```bash
# 找出位于指定元素周围的其他元素!
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijin 1000 km
1) "beijin"
2) "xian"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijin 400 km
1) "beijin"
```

> GEOHASH命令- 返回一个或多个位置元素的Geohash表示

该命令将返回11个字符的Geohash字符串!

```bash
# 将二维的经纬度转换为一维的字符串！如果两个字符串越接近，那么则距离越近
127.0.0.1:6379> GEOHASH china:city beijin hangzhou
1) "wx4fbxxfke0"
2) "wtmkn31bfb0"
```

> GEO 底层的实现原理就是Zset！ 我们可以使用Zset命令来操作geo！

```bash
127.0.0.1:6379> ZRANGE china:city 0 -1	#查看地图中全部元素
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "hangzhou"
5) "shanghai"
6) "beijin"
127.0.0.1:6379> ZREM china:city beijin	#移除地图中的某个元素
(integer) 1
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "hangzhou"
5) "shanghai"
```

####  Hyperloglog

> 什么是基数

A{1,3,5,7,8,9,7}	B{1,3,5,7,8}

基数：不重复的元素 = 5，可以接受误差!

> 简介

基数统计的算法！

优点：占用的内存是固定的，2^64不同的元素的技术，只需要耗费12KB的内存！如果要从内存角度来比较的话

**网页的UV(一个人访问一个网站多次，但是还是算作一个人)**

传统的方式，set保存用户的id，然后就可以统计set中的元素数量作为标准判断！

这个方式如果保存大量的用户id，就会比较麻烦！我们的目的是计数，而不是保存用户id；

> 测试使用

```bash
127.0.0.1:6379> PFADD mykey a b c d e f g h i k	#创建第一组元素
(integer) 1
127.0.0.1:6379> PFCOUNT mykey		#统计mykey中元素的数量
(integer) 10
127.0.0.1:6379> PFADD mykey2 i j z x c v b n m
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2	#第二组数量
(integer) 9
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2	#合并两组，并查看数量
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 16
```

#### Bitmaps

> 位存储

位图，操作二进制位来进行记录，就只有0和1两个状态！

用来记录周一到周日的打卡

![1590139674022](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590139674022.png)

查看某一天是否有打卡！

```bash
127.0.0.1:6379> GETBIT sign 3
(integer) 1
127.0.0.1:6379> GETBIT sign 5
(integer) 0	
```

统计操作，打卡天数!

```bash
127.0.0.1:6379> BITCOUNT sign	#统计这周的打卡记录，就知道这周是否有全勤
(integer) 4
```

### 事务

Redis事务本质:一组命令的集合！一个事务中的所有命令都会被序列化,在事务的执行中，会按照顺序执行！

一次性！顺序性，排他性，执行一些列命令！

==没有隔离级别的概念==

==Redis单条命令是保存原子性的，但是事务不保证原子性==

redis的事务：

* 开启事务`multi `
* 命令入队`...`
* 执行事务` exec`

> 正常执行事务!

```bash
127.0.0.1:6379> multi	#开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec	#执行事务
1) OK
2) OK
3) "v2"
4) OK
```

> 放弃事务

```bash
127.0.0.1:6379> multi	#开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> DISCARD 	#取消事务
OK
127.0.0.1:6379> get k4
(nil)
```

> 编译型异常(代码有问题！命令有错！)，事务中所有的命令都不会被执行！

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3	#错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k5	#所有的命令都没被执行
(nil)
127.0.0.1:6379> get k1
(nil)
```

> 运行时异常(1/0),如果事务队列中存在语法性，那么执行命令的时候，其他命令可以正常执行的,错误命令抛出异常

```bash
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> INCR k1	#执行的时候失败
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range	#虽然命令报错了，但是依旧执行成功了！
2) OK
3) OK
4) "v3"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"v3"
```

> 监控！Watch

**悲观锁:**

* 很悲观，无论什么时候都会加锁！

**乐观锁**

* 很乐观，认为什么时候都不会出现问题，无论什么时候都不会加锁！更新数据的时候去判断一下，在此期间是否有人修改过数据。version！
* 获取Version
* 更新的时候比较version

> Redis的监视测试

正常执行成功！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> WATCH money 
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```



测试多线程修改值，使用watch可以当作redis的乐观锁操作！

```bash
127.0.0.1:6379> watch money	#监视 money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 10
QUEUED
127.0.0.1:6379> INCRBY out 10
QUEUED
127.0.0.1:6379> exec	#执行之前，另外一个线程，修改了我们的值，这个时候，就会导致事务执行失败
(nil)
```

如果修改失败获取最新的值就好！

![1590141396851](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590141396851.png)

> 监控！ Redis使用乐观锁 面试常问

### Jedis

使用java来操作Redis

> Redis官方推荐的使用java的连接工具!使用java操作redis中间件！

1.导入对应的依赖

```xml
<!--        导入jedis包-->
    <dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>
<!--        fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
    </dependencies>
```

#### 常用的API

String

List

Set

Hash

Zset

### Springboot 整合

> 整合测试

jedis: 采用的是直连，多个线程操作的话，是不安全的，如果要像避免不安全。使用jedis pool 连接池

lettuce：采用netty 实例可以在多个线程共享，不存在线程的不安全情况！可以减少线程数据，更像NIO模式！

1. 导入依赖
2. 配置连接

```xml
# SpringBoot 所有的配置类，都有一个自动配置类
# 自动配置类都会绑定一个properties 配置文件
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

3. 测试！

企业中，我们的所有pojo都会序列化 SPringBoot

编写一个直接的RedisTemplate

### Redis.conf 详解

> 单位

![1590165872369](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590165872369.png)

* 配置文件unit单位 对大小写不敏感

> 包含

![1590165926496](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590165926496.png)

就好比SPring，Import

> 网络

```bash
bind 127.0.0.1	#绑定的IP
protected-mode yes #保护模式
port 6379 #端口设置
```

> 通用GENERAL

```bash
daemonize yes# 以守护进程的方式运行，默认是no，我们需要自己开启yes

pidfile /var/run/redis_6379.pid # 如果以后台的方式运行，我们就需要指定一个pid文件
```

![1590166137576](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590166137576.png)

```bash
loglevel notice
logfile	""  #日志文件的位置
databases 16 #数据库的数量
always-show-logo yes # 是否总是显示LOGO
```

> 快照

持久化，在规定的时间内，执行了多次操作，则会持久化到文件 rdb.aof

redis是内存数据库，如果没有持久化，那么数据会丢失

```bash
# 900秒内，如果至少有一个key进行修改，我们进行持久化操作
save 900 1
save 300 10
save 60  10000

# 持久化如果出错了，是否需要继续工作
stop-writes-bgsave-error yes   #持久化如果出错，是否继续工作

rdbcompression yes # 是否压缩 rdb文件，需要消耗一些cpu

rdbchecksum yes # 保存rdb文件的时候，进行错误检测

dir ./  #rdb保存的目录
```

> REPLICATION 复制

> SECURITY 安全

可以设置redis密码，默认是没有密码的！

> 限制CLIENTS



```bash
maxclients 10000 # 设置能连接上reids的最大客户数量

maxmemory <bytes>  #redis 配置最大的内存容量

maxmemory noeviction #内存到达上限后的处理策略
```

![1590166782739](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590166782739.png)

> APPEND ONLY 模式 aof配置

```bash
appendonly no #默认是不开启aof模式的，默认使用rdb方式，大部分情况下rdb够用

appendfilename "appendonly.aof"  #持久化文件的名字

#appendfsny alawys		#每次修改都会sync 消耗性能
appendfasyc everyesc	#每秒执行一次，可能会丢失一秒的数据
#appendfsync no			#不执行 sync，这个时候操作系统自己同步数据，最快!

# rewrite 重写
```

### Redis持久化

**redis必须持久化**

![1590167124093](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590167124093.png)

==rdb保存的文件是dump.rdb==  都是我们的配置文件中快照中进行配置的！

![1590167283866](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590167283866.png)

![1590167290661](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590167290661.png)

> 出发机制

1. save的规则满足的情况下
2. 执行flushdb命令
3. 退出redis

> 如何恢复rdb文件

1. 只需要将rdb文件放在redis的启动目录下就行 ，redis启动的时候自动检测
2. 查看需要存在的位置

![1590167553862](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590167553862.png)

> 几乎默认的配置就够用了，但是我们还是需要配置

优点：

1. 适合大规模的数据恢复
2. 对数据的完整性不高

缺点：

1. 需要一定的时间间隔进行操作！如果redis意外宕机了，这个最后一次修改的数据没有了
2. fork进程的适合，会占用一定的内存空间

#### AOF(Append only File)

将我们所有命令都记录下来，恢复的时候将这个文件重新执行一遍

==Aof保存的文件是，appendonly.aof文件==

> append

![1590199172485](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590199172485.png)

以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来(读操作不记录)，只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据->redis重启的话就根据日志文件的内容将写指令从前写到后执行一次以完成数据恢复工作。

![1590199477535](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590199477535.png)

默认是不开启的，我们可以手动配置！我们只需要将appendonly 改为yes就开启了aof！

重启rredis 就可以生效

如果这个aof文件有错误，我们可以使用`redis-check-aof  --fix  XXXX` 来修复aof文件

![1590199910408](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590199910408.png)

> 优点和缺点

优点:

1. 每一次修改都同步，文件的完整性更好
2. 每秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率是最高的

缺点：

1. 相对于数据文件来说。aof远远大于rdb，修复数据比rdb慢
2. Aof运行效率要比rdb慢，所以我们redis默认是rdb持久化，而不是aof

![1590200307516](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590200307516.png)

![1590200321421](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590200321421.png)

### Redis发布订阅

![1590200506393](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590200506393.png)

![1590200577194](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590200577194.png)

订阅端:

```bash
127.0.0.1:6379> SUBSCRIBE chenbin	#订阅一个频到
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "chenbin"
3) (integer) 1
# 等待读取信息
1) "message"	#消息
2) "chenbin"	# 哪个频到的消息
3) "helloworld"	#消息的具体内容
1) "message"
2) "chenbin"
3) "nihaoa"
```

发布端:

```bash
[root@VM_0_13_centos ~]# redis-cli -p 6379
127.0.0.1:6379> PUBLISH chenbin helloworld	#发布者发送消息到频道
(integer) 1
127.0.0.1:6379> PUBLISH chenbin nihaoa
(integer) 1
```

![1590201019071](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590201019071.png)

![1590201108942](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590201108942.png)

使用场景：

1. 实时消息系统！
2. 实时聊天（频道当作聊天室，将消息回显给所有人即可！
3. 订阅，关注系统都是可以的！

稍微复杂的场景 我们会使用消息中间件来做！

###  环境配置

只配置 从库，不配置主库

```bash
127.0.0.1:6379> info replication	#查看当前库信息
# Replication
role:master	#角色
connected_slaves:0	#没有从机
master_replid:cf4d1200cc02e986536732c385d4c6e90977131e
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

复制3个配置文件，然后修改对应的信息

1. 端口
2. pid名字
3. log文件名字
4. dump.rdb名字

修改完毕之后启动三个redis服务器，可以通过进程信息查看！

#### 一主二从

==默认情况下，每台redis服务器都是主节点==

我们一般情况下只用配从机就好了！

![1590202742889](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590202742889.png)

![1590202756554](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590202756554.png)

> 细节

主机可以写，从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存！

主机写：

![1590202938460](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590202938460.png)

从机只能读：

![1590202950338](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590202950338.png)

测试：主机断开连接，从机依旧连接到主机的，但是没有写操作！这个时候，主机如果回来了从机依旧可以直接获取到主机的信息！

如果是使用命令行，来配置的主从，这个时候如果重启了，就会变回主机！只要变回从机，立马就会从主机中获取到数据的值！

> 复制原理

![1590203411905](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590203411905.png)

> 层层链路

上一个M连接下一个S！

![1590203596889](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590203596889.png)

这个时候也可以完成我们的主从复制！

如果主机断开了我们可以使用`SLAVEOF no one`让主机编程主机！其他的节点就可以手动连接到这个主节点(手动)

### 哨兵模式

(自动选举老大的概念)

> 概念

![1590203920746](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590203920746.png)

![1590203958786](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590203958786.png)

> 哨兵模式

如果主机此时回来了，只能归并到新的主机下，当作从机，这就是哨兵模式的规则！

优点：

1. 哨兵集群，基于主从复制模式所有的主从配置优点，它全有
2. 主从可以切换，故障可以转移，系统的可用性就会更好
3. 哨兵模式就是主从模式的升级，手动到自动，更加健壮！

缺点：

1. Redis 不好在线扩容，集群容量一旦达到上限，在线扩容就十分麻烦！
2. 实现哨兵模式的配置其实是很麻烦的，里面有很多的选择 

### Redis缓存穿透和雪崩(面试高频，工作常用)

Redis缓存的使用，极大的提升了性能和效率，特别是数据查询方面。但是同时，它带来了一些问题。其中，最要害的问题就是数据的一致性问题，从严格意义来说，这个问题是无解。如果对数据的一致性要求高，那么就不能使用缓存。

经典问题：缓存穿透，缓存雪崩和缓存击穿。

#### 缓存穿透(查不到)

> 概念

缓存穿透的概念：用户想要查询一个数据，发现redis内存数据库没有，也就是缓存数据库没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透

![1590213953767](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590213953767.png)

> 解决方案

布隆过滤器

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃。从而避免了对底层系统的查询压力。

![1590214075535](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590214075535.png)

但是这种方法会存在两个问题：

1. 如果空值能被储存起来，这就意味着缓存需要更多的键，因为这当中可能会有很多的空值的键
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口不一致，这对于需要保持一致性的业务会有影响。



#### 缓存击穿(量太大，缓存过期！)

> 概述

![1590214421329](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590214421329.png)

>  解决方案

![1590214485403](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590214485403.png)

![1590214494830](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590214494830.png)



#### 缓存雪崩

![1590214584127](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1590214584127.png)



> 解决方案

**Redus高可用**

意思是：既然redis有挂掉的可能，那我多增几台设备，这样一台挂掉之后还可用继续工作，其实就是搭建的集群(异地多活)

**限流降级**

在缓存失效之后，通过加锁或者队列来控制读取数据库写缓存的数量。比如对某个key只允许一个线程查看数据和写入缓存，其他线程等待。

**数据预热**

数据加热：在正式部署之前，把可能的数据先访问以便，这样大部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动加载缓存不同的key，设置不同的过期时间，让缓存失效的时间进来均匀。

> 小结

无敌！》！！

























​	