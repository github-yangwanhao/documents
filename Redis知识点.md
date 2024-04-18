# 通用命令

- keys *：用于查看所有的key，大数据量情况下慎用
- del key：删除一个key和对应的value，返回值代表成功删除了几个key
- del key1 key2 key3...：同时删除多个key和对应的value，返回值代表成功删除了几个key
- exists key：判断key是否存在，存在则返回1不存在返回0
- expire key seconds：给一个key设置过期时间，单位是秒。返回1代表设置成功，返回0代表该key不存在
- ttl key：查看一个key剩余的过期时间
  - 返回正整数代表key的剩余过期时间，单位是秒
  - 返回-2代表key不存在
  - 返回-1代表key永久有效，没有过期时间
- flushdb：清空整个redis的键值对，慎用

# String类型的常用命令

String类型是redis的最基础的类型，value都是字符串。不过根据字符串的格式不同又可以分为三类：

1. string 普通字符串
2. int 整数类型，可以做自增自减的操作
3. float 浮点数类型，可以做自增自减的操作

以下是String的常用命令

- set key value EX|PX time NX|XX
  - set key value是必须参数
  - 如果key不存在就新建，key存在就对值做覆盖
  - 当需要同步设置过期时间时需要传入EX|PX time两个参数，EX代表单位是秒，PX代表单位是毫秒
  - NX|XX：NX代表只有当key不存在时才对key进行操作；XX代表只有当key存在时才对其进行操作
- get key：获取一个key对应的value，如果key不存在返回(nil)
- mset key1 value1 key2 value2......：同时设置多个key和对应的value，没有其他参数
- mget key1 key2......：同时获取多个key，返回的是字符串数组，value的顺序和key的顺序一直，不存在的key对应位置也会返回(nil)
- incr key：使一个整数类型的key自增1，返回值为该key自增1后的值，存在以下几种情况
  - key存在且为整数，正常自增1且返回自增后的数字
  - key存在且为浮点数/字符串，报错返回**(error) ERR value is not an integer or out of range**
  - key不存在，那么就会生成一个key且value为0，并且立刻自增返回自增后的1
- incrby key increment：让一个整数类型的key自增并指定步长
  - increment为正数则为加法，increment为负数则为减法
  - key存在且为整数，正常自增指定步长且返回自增后的数字
  - key存在且为浮点数/字符串，报错返回**(error) ERR value is not an integer or out of range**
  - key不存在，那么就会生成一个key且value为0，并且立刻自增指定步长返回自增后的值
  - increment参数必须是整数，否则返回**(error) ERR wrong number of arguments for 'incr' command**
- incrbyfloat key increment：让一个浮点数类型的key自增指定步长
  - increment为正数则为加法，increment为负数则为减法
  - key存在且为整数/浮点数，正常自增指定步长且返回自增后的数字
  - key存在且为字符串，报错返回**(error) ERR value is not a valid float**
  - key不存在，那么就会生成一个key且value为0，并且立刻自增指定步长返回自增后的值
  - increment必须是整数/浮点数，否则返回**(error) ERR value is not a valid float**
- setnx key value：等同于set key value nx
  - 如果key存在，就不做任何操作，返回-1
  - 如果key不存在，就设置key和value，返回1
- setex key seconds value：效果等同于set key value EX time

# String类型的应用场景

- 单值缓存
- 计数器，使用incrby key increment命令自增自减
- 分布式锁，使用setnx key value命令实现
- id生成
- 存储token等令牌

# List类型的常用命令

list类型的值其value的结构类似于java的LinkedList。**元素有序(指插入顺序)，可重复。**

- lpush key **e1 e2 e3**......：
  - 向集合的左边依次插入一个或多个元素，返回的是插入后集合元素的个数
  - 集合的数据顺序类似于**e3 -> e2 -> e1**，和命令中元素的顺序**相反**
- rpush key **e4 e5 e6**......：
  - 向集合的右边依次插入一个或多个元素，返回的是插入后集合元素的个数
  - 集合的数据顺序类似于**e4 -> e5 -> e6**，和命令中元素的顺序**一致**
- lpop key：从集合的左边**弹出(删除)**一个元素并返回该元素的值，如果集合为空返回(nil)
- rpop key：从集合的右边**弹出(删除)**一个元素并返回该元素的值，如果集合为空返回(nil)
- lrange key start end：返回一段下标中所有的元素，顺序按照集合内的顺序来排列
  - 注意：**lpop和rpop会删除元素,lrange只是查询和返回，不会对集合的元素做操作！**
  - start从0开始(0代表左边第一个元素)，使用正数代表从左边开始计数，使用负数代表从右边开始计数
  - end可以大于集合本身的长度
  - 如果start小于end，返回**(empty list or set)**
- blpop和brpop：blpop key timeout
  - 功能都与lpop、rpop类似，只不过如果没有元素时会阻塞等待直到timeout规定的时间，如果超时后还未获取到才会返回(nil)
  - timeout的单位是秒

# List类型的应用场景

- 出口和入口在同一侧可以构建一个栈(先进后出FILO)
- 入口和出口在不同侧可以构建一个队列(先进先出FIFO)，比如任务队列
- 阻塞队列，lpush+brpop就可以变成一个阻塞队列

# Hash类型的常用命令

hash类型的值其value的结构类似于java的HashMap。

- hset key field1 value1 field2 value2......(hmset已弃用，hset也可以设置多个值)
- hget key field：获取某个key中某个field的值
- hgetall key：获取某个key下所有的**键值对**，返回一个数组，数组的值为[field1,value1,field2,value2.......]
- hkeys key：获取某个key下所有的**键**集合，返回一个数组
- hvals key：获取某个key下所有的**值**集合，返回一个数组
- hincrby key field increment：使某个key下的某个键的值自增并指定步长(只能增长整数类型)
- hsetnx key field value：作用类似于String的setnx命令

# Hash类型的应用场景

- 缓存对象/数据字典/配置信息
- 购物车
- 统计用户行为

# Set类型的常用命令

set类型的值其value的结构类似于java的HashSet。**元素无序，不可重复。**

- sadd key m1 m2 m3......：向set中添加一个或多个元素。返回的是成功添加的元素个数。这个动作会自动进行去重，包括set中本身已经存在的元素或者参数中重复的元素。
- srem key m1 m2 m3......：从set中移除一个或多个元素。返回的是成功删除的元素个数。如果返回值小于参数的个数，说明一部分参数本身在set中不存在。
- scard key：返回集合中元素的个数。如果set不存在则返回0.
- sismember key member：判断某个元素在set中是否存在。存在则返回1；set或者元素不存在则返回0。
- smembers key：获取set中所有的元素。
- sinter key1 key2 key3......：获取多个set的交集
  - 如果其中一个set为空(不存在)，那么返回**(empty list or set)**
  - 如果所有的set中没有交集，那么返回**(empty list or set)**
- sdiff key1 key2 key3......：获取第一个set和后边一个或多个set的元素的差集。就是第一个set中有，但是后边的一个或多个set中都没有的元素。
- sdiffstore destKey key1 key2 key3......：获取第一个set和后边一个或多个set的元素的差集并放入key为destKey的set中
- sunion key1 key2 key3......：获取所有set去重后的合集。
- sunionstore destKey key1 key2 key3......：获取key1、key2、key3去重后的合集并放入key为destKey的set中
- srandmember key count：从set中**随机查找(不删除)**count个元素返回，count不输入默认1。
- spop key count：从set中**随机弹出(删除)**count个元素返回，count不输入默认1。

# Set类型的应用场景

- 抽奖程序：使用srandmember 或者spop 抽取中奖者
- 查找共同好友/联系人/可能认识的人

# ZSet(Sorted Set)类型的常用命令

Sorted Set是一个可排序的set集合，底层是一个调表(SkipList)加Hash表实现的。具有有序性(可排序)和元素不重复性。

- zadd key [score member]......：向set内添加一个或者多个元素,返回的是添加成功的个数(会自动去重)
- zrem key member：从set内移除一个元素
- zscore key member：查询set内某个元素的score。score使用浮点数表示会有精度问题。
- zrank key member：查询某个元素在集合中的排名。排名是score从低到高排序，且排名从0开始。
- zcard key：获取set中元素的总个数。
- zcount key min max：统计score值在min和max范围中的元素的个数。
- zincrby key increment member：使set内某个元素的score自增指定步长。如果元素不存在则添加元素并赋值score给0然后自增指定步长。
- zrange key start stop withscores：查询从start排名到stop排名的元素(排名从低到高，从0开始)，withscores为可选参数，没有该参数则只返回元素，带有该参数则同时返回元素对应的score。
- zrangebyscore key min max withscores：查询score在min和max范围内的元素。withscores为可选参数，没有该参数则只返回元素，带有该参数则同时返回元素对应的scor
- **上面说到的排序都是指升序排序，也就是从低到高。如果想要降序排序，只需要在命令的z后面加上rev，如zrevrank、zrevrange。**
- zpopmin/zpopmax count：**弹出(并删除)**count个score最小/最大的元素，count不给则默认1

# ZSet(Sorted Set)类型的应用场景

- 动态排行榜：新闻热搜排行榜

# 缓存三大问题

- 缓存穿透：通过请求不存在的数据进行攻击。
  - 解决方案：短期的缓存空值、布隆过滤器、请求频率限制
- 缓存击穿：某个热点key突然失效导致大量请求到达数据库。
  - 解决方案：互斥锁、热点数据永不过期
- 缓存雪崩：缓存中数据大量失效，导致大量的请求到达数据库。
  - 不同的key设置不同的过期时间

# Redis的持久化方式

- RDB：Redis Database Backup file，简单来说就是把当前内存中的所有数据都备份到磁盘中。
  - 命令分为save和bgsave。save使用主线程保存文件，会阻塞其他请求；bgsave会新开线程异步执行保存，不会阻塞主线程的请求。
  - RDB会丢失部分数据。
- AOF：将修改的每一条指令写入文件中。
  - appendfsync always：每执行一条指令就立刻写入
  - appendfsync everysec：每秒钟执行一次写入指令，可能会丢失一秒钟的数据。
  - appendfsync no：不主动写入，交给操作系统处理。
  - 恢复数据会特别慢。
  - AOF可以配置重写命令，整理重复的命令。
- 混合持久化方式：aof重写时不再重写全部指令，而是把内存数据以二进制方式放入文件，然后继续追加指令。

# Redis的过期删除策略

- 定时删除：在设置键的过期时间时，同时建立一个定时器，让定时器在键过期时执行删除动作。
  - 优点：可以保证每个键都在过期时被及时删除。
  - 缺点：为每个有过期时间的键设置定时器也会带来CPU消耗，而且对键值和过期时间的修改删除等动作也要操作对应的定时器。
- 惰性删除：对于已经过期的键不做任何操作，只有在下一次访问这个已经过期的键时才对其执行删除操作并返回空值。
  - 优点：这种操作的优点是执行操作少，只有在访问过期键时才会发生删除操作，占用CPU资源也少。
  - 缺点：如果过期的key不被访问，就会一直存在于内存之中，会大量占用内存。
- 定期删除：Redis会将每个设置了过期时间的key放入一个独立的数据字典中，会定时(每秒10次)遍历这个字典来删除已经过期的key。过期扫描不会扫描整个字典，而是采用了一种贪心算法。首先，从过期字典中选取20个key并删除其中的已过期的key；第二，如果这次筛选中过期的比例超过四分之一，那么重新执行上一步；为了保证递归一定会有一个出口，防止无限时间的扫描和删除，算法还增加了扫描时间的上限，默认不会超过25ms。
  - 优点：限制了删除操作的时长和频率，减少了CPU的消耗。
  - 缺点：如果遇上缓存雪崩(多个key同时过期)，就会持续扫描过期字典，直到过期字典中的过期key变得稀疏为止，就会导致CPU被频繁消耗。此外，也可能会造成一些过期键未被及时删除。

实际生产过程中一般采用惰性删除+定期删除的策略。

# Redis的内存淘汰策略

当Redis中已使用的内存到了maxmemory限定的时候，就会触发主动清理策略，策略一共有8种。

这里说一下LRU算法和LFU算法。

- LRU：(Least Recently Used，最近最少使用)，淘汰最长时间未被使用的数据，以最近一次访问时间做参考。
- LFU：(Least Fequently Used，最不经常使用)，淘汰最近一段时间被访问次数最少的数据，以访问次数为参考。

大多数情况下都可以采用LRU算法，但是如果缓存的特点是有大量的热点数据时，LFU可能更合适一点。

# Redis的分布式架构

- 主从模式：一个主节点(master)+n个从节点(slave)，主可写可读，从只能读。在启动一台从节点后，从节点会向主节点发送一个psync请求，如果从节点是第一次连接主节点，就会触发一次全量复制。master会生成一个RDB快照，并把新的写请求放入缓存中，然后把RDB文件发给从节点，从节点收到文件后读取文件写入内存，然后master再将写入缓存的命令传递给slave。

  如果从节点很多，那么可以采用树形结构来避免大量从节点都从主节点同步数据，导致主从复制风暴问题。

- 哨兵模式：哨兵模式是主从的一个变种。主从模式一旦主节点宕机就只能等人工介入重启或者重新指定主节点，因此并不能完全满足高可用(从节点是只能读不能写的)。哨兵模式相对于主从模式，在主节点宕机的情况下增加了竞选机制，从所有的从节点中选举中一台新的主节点，保证了系统的高可用。

  首先客户端不会直接访问主节点，而是通过哨兵sentinel来访问主节点。sentinel特点：

  - 监控：它会监听主服务器和从服务器之间是否在正常工作
  - 通知：它能够通过API告诉系统管理员或者程序，集群中某个实例出了问题
  - 故障转移：它在主节点出了问题的情况下，会在所有的从节点中竞选出一个节点，并将其作为新的主节点
  - 提供主服务器地址：它还能够向使用者提供当前主节点的地址。这在故障转移后，使用者不用做任何修改就可以知道当前主节点地址

  sentinel本身也可以集群部署，sentinel可以通过发布与订阅自动发现Redis集群中的其他sentinel。集群中的所有sentinel不会并发着去对同一个主节点进行故障转移。故障转移只会从第一个sentinel开始，当第一个故障转移失败后，才会尝试下一个。当选择一个从节点作为新的主节点后，故障转移即成功了(而不会等到所有的从节点配置了新的主节点后)。这过程中，如果重启了旧的主节点，那么就会出现无主节点的情况，这种情况下，只能重启集群。

  主观下线：一台sentinel认为master宕机的时候，会把master标记为SRI_S_DOWN，这就是主观下线

  客观下线：master被标记为主观下线后，其他sentinel也会去查看master的状态，如果半数以上的sentinel都认为master宕机了，就会把把master标记为SRI_O_DOWN，这就是客观下线

  首先sentinel集群自己会进行选举，选举出一台用于处置故障转移的sentinel。然后这台sentinel会从剩下的slave中选出一个新的master，挑选标准：①没有掉线的②网络响应速度快的③与原master联络多的④优先原则。

  选中新的master后先通知新的master，让其断开与旧的master的链接，成为新的master；然后通知其他slave，告知新的master的IP和端口。

- 集群模式：由多个主从节点群组成的分布式服务集群，具有复制、高可用和分片的特性。客户端通过jedis对键值对做hash运算，进而决定将键值对分配到哪个集群上处理。所以集群模式的数据是分散在多个小集群的。

