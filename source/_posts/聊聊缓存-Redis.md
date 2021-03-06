---
title: 简单聊聊缓存-Redis
date: 2018-11-05 09:23:42
tags: Redis
categories: Redis
---
# 分布式缓存Redis
## 为什么使用Redis？
使用Redis主要是考虑项目的性能和并发。从性能上来说：当我们执行一个繁琐但是不经常变动的SQL时，重复的浪费时间在执行SQL上了，此时加上缓存就可以直接访问缓存，大大节约了访问数据库的时间。从并发性上说：当很多个连接去访问数据库，数据库很容易出现异常等问题。
当然还有Redis具有分布式锁等等优点。
## 单线程的Redis为什么这么快？
首先，Redis是常驻内存进程，纯内存操作，你说能不快吗。
第二，Redis是单线程操作，避免频繁的上下文切换。
第三，Redis采用非阻塞I/O多路复用机制，可百度详细介绍，作深入了解←_←
## Redis的数据类型及常用场景
1. String。最常规的set/get操作，可存储value可以是string也可以是数字。一般做一些复杂的计数功能的缓存。
2. Hash。特别适合用于存储对象。有些单点登录的实现，就是借助Redis的Hash缓存cookID和对应的用户信息。
3. List。字符串列表，按照插入顺序排序。这种数据类型可以做一些简单的队列，也可以利用lrange实现redis的分页功能。
4. Set。因为set堆放的是一些不可重复值的集合。可以做去重的作用。
5. Sorted Set。相对于Set，多了一个权重参数sorce。可以根据sorce进行排序，例如排行榜等。

<!-- more -->
## Redis的过期策略和内存淘汰机制
我们已经知道Redis是在内存中工作的，如果一直不停的往Redis写数据，那内存很快就不够用了，所以需要进行删除。
一般来说，删除策略有三种：**立即删除**，**惰性删除**，**定时删除**。
**立即删除：**键值一过期就进行删除，保证了缓存最大的鲜活度，**但是对CPU却不友好**。删除是需要CPU时间的，如果CPU在高峰期，此时还要进行删除操作，无疑添加了CPU的负荷。
**惰性删除：**到了过期时间不会立即删除，而是下次使用到了进行删除，显而易见，这种方式**浪费了内存空间**。
**定时删除：**这是一种折中于上面两者之间的策略。每隔一段时间进行删除操作，并通过限制删除操作的时长和频率来减少对CPU的影响。
**而Redis采用定时删除+惰性删除。**使用这种删除策略就一定没有问题吗，很明显不是的。如果定时删除没有删除key，你也长时间没有访问该key，那么还是白白浪费了内存空间。这时就要提到内存淘汰机制。
在redis.conf配置文件中有这么一行配置：
`# maxmemory-policy volatile-lru`
该配置就是用来配置内存淘汰机制的。
1. **noeviction：**当内存不足以容纳新写入数据时，新写入操作会报错。应该没人用吧。
2. **allkeys-lru：**当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 Key。推荐使用，目前项目在用这种。
3. **allkeys-random：**当内存不足以容纳新写入数据时，在键空间中，随机移除某个 Key。应该也没人用吧，你不删最少使用 Key，去随机删。
4. **volatile-lru：**当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 Key。这种情况一般是把 Redis 既当缓存，又做持久化存储的时候才用。不推荐。
5. **volatile-random：**当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 Key。依然不推荐。
6. **volatile-ttl：**当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 Key 优先移除。不推荐。

**注意：**如果没有设置 expire 的 Key，不满足先决条件(prerequisites)；那么 volatile-lru，volatile-random 和 volatile-ttl 策略的行为，和 noeviction(不删除) 基本上一致。
## Redis会有那些缺点？
任何事物都不可能是完美的，使用Redis也会出现一些问题。比如下面这些：
1. 缓存和数据库双写一致性问题。
2. 缓存穿透问题。
3. 缓存雪崩问题。
4. 缓存的并发竞争问题。

有问题我们就需要解决。
### 缓存和数据库双写一致性问题
一致性问题是分布式最常见的问题。一致性又可细分为最终一致性和强一致性。
对于强一致性的数据，是不建议放缓存的，我们做的一切只能保证最终一致性。
所以我们只能做好更新策略，先更新数据库，再删除缓存。对于缓存删除失败可以使用消息队列来做补偿措施。
### 缓存穿透问题
当有黑客故意发出大量请求缓存中不存在的数据时，就会发生缓存穿透，导致大量请求怼到数据库，造成数据库异常。
对应的解决方案：
+ **利用互斥锁**：缓存失效的时候，先去获得锁，得到锁了，再去请求数据库。没得到锁，则休眠一段时间重试。
+ **采用异步更新策略，无论 Key 是否取到值，都直接返回。**
+ **提供一个能迅速判断请求是否有效的拦截机制**：常用的如布隆过滤器，虽然不能保证百分百的过滤，但是很适合作为第一道门槛。

### 缓存雪崩问题
当有一大片缓存同时失效，而同时又来了一堆请求，就会出现大量请求直接访问到数据库，操作数据库异常。
对应的解决方案：
+ **给缓存的失效时间，加上一个随机值，避免集体失效。**
+ 使用互斥锁，很明显这种容易造成吞吐量下降。
+ **双缓存**。

### 缓存的并发竞争问题
这种情况类似于有多个子系统要set同一个key。
百度很多说是利用redis事务机制来解决，单机可能有用，但是在redis集群中，数据做了分片就显得十分麻烦。
对于无顺序要求的，使用分布式锁，抢到锁就进行set，这种比较简单。
对于有顺序的，一种抢到锁进行set操作同时加上时间戳，如A要set这个key的value为a，B要set value为b，C要set value为c，在写入数据库的时候我们带上一个时间戳
``` bash
系统A key（value a 11:00）
系统B key（value b 11:01）
系统C key（value c 11:02）
```
假如B先抢到锁，执行完set操作，此时A抢到锁，发现自己的时间戳早于此时B已经写入的时间戳，那么不进行set操作。
还有一种就是利用队列的串行实现顺序操作。
还是那句话，技术是服务于实际业务的，多从实际业务中总结经验。

