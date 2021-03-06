# Redis

寻址和带宽



### bitmap

```shell
用户系统，统计用户登录天数，且窗口随机
setbit ydx 1 1 (ydx，第二天登录了系统)
setbit ydx 364 1 (ydx, 第365天登录了系统)
BITCOUNT ydx -2 -1 (ydx，最后两周的登录总情况)
```

![1614504792120](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1614504792120.png)

### SkipList

![1614597565823](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1614597565823.png)

### 缓存击穿

* 前提：发生了高并发

* Key过期造成并发访问数据库

  **如何解决？**

  ```
  setnx() -->约等于1把锁
  这样最后高并发，最后也只会有一个请求打到数据库上
  其他人等待
  1. getKey
  2. setNx
  	3.1 queryDb
  	3.2 wait a minute
      	4. 循环 getKey、setNx......
  ```

  这样解决的问题：死锁

  * 如果设置了过期时间，但第一个人没挂，但是锁超时了。。。

  * 多线程操作：一个线程取DB，一个线程监控是否取回来了；动态延长锁时间

    <font color=red>自己实现分布式锁很麻烦</font>

### 缓存穿透

搜索本身就不存在的内容（redis失效、数据库无效重复查询）

1. 布隆过滤器+bitmap
2. redis设置一个为值为null的键值对【布隆过滤器失效的补偿】

```
问题：
新增元素，会涉及数据库和布隆过滤器的双写
删除元素，布隆过滤器还不支持删除

counter bloom filter: 支持删除（每一位从涂黑变成了计数）:消耗空间从1位变成了1个int
```

### 缓存雪崩

**大量的key同时失效，间接造成大量访问到达数据库**

1. 时点性无关的数据：均匀分布、随机分布过期时间

2. 必须每天更新的数据（每天数据必须更新，则要求时间必须在0点后）：

   * 强依赖缓存击穿的方案

   * 业务层加判断：零点延时

     

### 分布式锁

* setnx：时间过期了也没写完
* 多线程（守护线程）：延长过期时间
* redisson：lua脚本做原子性
* zookeeper

### Redis作为数据库/缓存的区别 

缓存不是全量数据，缓存应该随着访问变化 ，只保留热数据，**缓存不重要**

* key的有效期

* 内存设置： 1 ~ 10 g最优

  ```
  maxmemory <bytes>
  maxmemory-policy noeviction：达到最大内存后，内存淘汰机制
  ```

### 内存淘汰机制

1. 定时淘汰
   * 每秒淘汰10次：随机选择key，查看key是否过期，过期则删除

2. 惰性删除：
   * 查询的时候检查key是否过期，过期则删除
3. 策略淘汰

| 策略            | 解释                          |
| --------------- | ----------------------------- |
| noeviction      | 直接抛错                      |
| allkeys-lru     | 所有key进行最近最少使用的回收 |
| volatile-lru    | 有过期的键进行lru             |
| allkeys-random  | 所有key随机回收               |
| volatile-random | 有过期的键进行随机回收        |
| volatile-ttl    | 有过期的键回收ttl较短的       |
| volatile-lfu    | 有过期的键回收使用频率低的    |

4. 发生写，会剔除ttl

### RDB：snapshot

创建 fork 子进程：fork + copy on write

* 父子进程对数据的修改，对方看不到

**快照版本**：极端情况会丢失时间窗口直接的数据

```
save: 阻塞方式【非常明确的场景，如关机维护】
bgsave：异步方式
可在配置文件编写 bgsave规则
save <seconds> <changes>
save 900 1
save 300 10
save 60 10000
```

优点：恢复的速度相对快（类似序列化反序列化）

### Linux管道

* 衔接前一个命令的输出，作为后一个命令的输入
* 管道会触发创建子进程

```
echo $$ | more
echo $BASHPID | more

$$ 优先级 高于管道
```

```
父子进程
./test.sh &: 后台运行
环境变量：子进程的修改，不会影响到父进程。父进程修改不会影响子进程
```

**创建子进程的速度快么？**（rdb）

如果父进程是redis，内存数据很大

1. 速度？
2. 内存空间够不够？

fork()

* 速度快
* 空间小
* copy on write：写时复制。创建子进程并不发生复制，加快创建子进程的速度

### AOF: append on file, replica

redis的写操作记录到文件中

* 丢失的数据少
* redis中，RDB和AOF可以同时开启
  * 如果开启了AOF，只会用AOF恢复

```
redis运行了10年，开启了AOF
10年后，redis挂了
1. A0F多大：很大，10个T
2. 恢复要多久：非常久
```

**AOF缺点**

1. 体量无限变大 --> 恢复慢
   * 解决：让日志只记录增量的，存量的用RDB
   * redis 4.0以后，存量数据用RDB，增量数据用AOF
2. redis是内存数据库，写操作会触发IO
   * NO
   * ALWAYS
   * 每秒

```
appendonly yes #开启AOF

appendfilename "xxx"

appendfsync no			#什么时候buffer满了，内核就把整个buffer刷到磁盘
appendfsync alyways		#每次写操作都刷到磁盘
appendfsync everysec	#每秒刷一次
```

## Redis集群

单节点缺点：单点故障、容量有限、压力较大

### AKF

X轴：全量镜像

Y轴：redis按业务划分存储数据 

Z轴：按优先级、逻辑再拆分

#### Redis哨兵：`sentinel`

```
redis-sentinel
redis-server --sentinel
配置文件：redis源码目录
	port 26379
	sentinel monitor mymaster 127.0.0.1 6379 3 
```

