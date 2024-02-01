# Redis

## 基础数据类型

1. 查看所有的key： `keys *`，也支持通配符查找，不推荐使用，因为如果`redis`里面~过多会阻塞`redis`
2. 查看有多少个key：`dbsize`，返回的是`key`的数量
3. 检查key是否存在：`exists key`，存在返回1，不存在返回0
4. 获取对应的key值：`get key`
5. 获取对应key值的类型：`type key`
6. 设置key对应的值：`set key val`
7. 删除对应的key：`del key`，返回删除key的数量，key可以是多个，用空格分隔
8. 设置过期时间（秒）：`expire key time`，`ttl key`显示过期时间，返回`-2`说明`key`过期，不存在，`-1`表示未设置过期时间,`0`表示刚好过期，也可以设置成毫秒：`pexpire key time`，设置过期在哪个点：`expireat key time`，这里的`time`是时间戳
9. 重命名：`rename oldName newName`，可能会有坑，`rename`可能会重命名重复的key，此时get会取到`oldName`，进行覆盖，解决办法，使用`renamenx`，如果遇到改名后有重复的key，则不会删除

常见的数据类型：`string，hash，list，set，zset`（字符串，哈希，列表，集合，有序集合）

### String操作命令

1. `setnx key val` ：设置键和键值，如果原本有key则无法设置，即：不存在才设置，也可以这么写：`set key val nx`
2. `mset key val key2 val2 key3 val3`：批量设置键值对，`mget key1 key2 key3`：批量获取
3. `incr key`：自增+1，`decr key`，自减-1，`incryb(decr) key val`，每次`+val(-val)`，浮点数使用 `incrbyfloat`
4. `append key val`：拼接`val`
5. `strlen key`：返回长度
6. `getset key val`：设置key对应的值，会返回原来key的值
7. `setrange key number val`：设置`key`对应`number`位置的值为`val`，`getrange key start end`：取start到end对应的值

### Hash操作命令

1. `hset hashName field value`：设置hash类型，名称为 `hashName` ， field 为key ，value为对应的值；`hmset`可以批量设置 ，`hmset hashName field1 value1 field2 value2`，同理，`hsetnx hashName field value`为不存在则设置
2. `hget hashName  field`： 获取对应 `field` 对应的值
3. `hdel hashName field`：删除对应的 `field`
4. `hlen hashName `：获取有多少对键值对
5. `hmget hashName field1 field2 ...`：返回`field`对应的`value`，也就是批量取`value`
6. `hexists hashName field`：判断 `field` 是否存在
7. `hkeys hashName`：返回所有的 `field`，`hval hashName`：返回所有的 `value`
8. `hgetall hashName`：返回所有的键值对，`field value field value` 这种形式
9. `hincrby hasnName field number`：自增 `number`

### List操作命令

可以看成是一个列表，列表中元素可以重复出现，可以从左边操作，也可以从右边操作。元素是有顺序的！！

1. `rpush（lpush） listName val`：从右边（左边）插入 `val`，可以插入多个，`rpush listName val1 val2 val3`，返回值为`list`里面有多少元素
2. `lrange listName start end`：查看`list`从 `start` 到 `end` 的元素，`end`为`-1` 表示所有元素
3. `lpop（rpop） listName number`：弹出左（右）侧元素，返回值为弹出元素，`number`为可选，默认为1，表示弹出一个
4. `lrem listName number val`：删除 `number` 个 `val` 值，返回值为实际删除的元素个数
5. `ltrim listName start end`：裁剪`list`，从`start`开始到`end`
6. `lset listName index val`：把下标为 `index` 的元素改为 `val`
7. `lindex listName index`：获取 `index` 下标的值
8. `llen listName`：返回 `list` 的长度
9. `blpop（brpop） listName timeout`：阻塞 `list`，`timeout` 是阻塞时间（s），`0`表示一直阻塞，因为有阻塞，所以可以实现 `MQ`

### Set操作命令

`Set` 是一个集合，是没有顺序的，但是不允许有重复的元素，没有所谓的索引下标，可以对两个或多个 `Set` 进行交集、并集等

1. `sadd setName val`：添加元素，可以添加多个，允许重复，重复数据只添加一次，返回值为成功添加的个数
2. `smembers setName`：返回所有的元素
3. `srem setName val`：删除元素，可以批量删除，返回删除元素的个数
4. `scard setName`：返回元素的个数
5. `sismember setName val`：判断元素是否存在，存在为1，不存在为0
6. `srandmember setName`：随机返回一个元素
7. `spop setName`：随机弹出一个元素（会改变 `set`）
8. `sinter setName1 setName2`：取交集，`sinterstore newSetName setName1 setName2`：取交集并存在 `newSetName` 里
9. `sunion setName1 setName2`：取并集，`sunionstore newSetName setName1 setName2`：取并集并存在 `newSetName` 里
10. `sdiff setName1 setName2`：取差集（以 `setName1` 为基准），`sdiffstore newSetName setName1 setName2`：取差集并存在 `newSetName` 里

### `ZSet`操作命令

加了一个 `score` 的 `set`

1. `zadd zsetName [nx|xx] score val`：添加元素，`val` 对应的分数为 `score`,`nx` 为不存在才添加，`xx` 为存在才添加
2. `zcard zsetName`：返回元素的个数
3. `zscore zsetName key`：返回 `key` 的分数
4. `zrank zetName key`：返回 `key` 对应的排名，从低到高（升序），从 0 开始
5. `zrevrank zetName key`：返回 `key` 对应的排名，从高到低（降序）
6. `zrem zsetName key`：删除对应的 `key`
7. `zrange zsetName [start end withscores]` ：返回全部（start 到 end）的元素（升序），加了 `withscores` 会列出对应的分数
8. `zrevrange zsetName [start end withscores]`：返回全部（start 到 end）的元素（升序）
9. `zincrby zsetName addScore key`：`key` 对应的 `score` 增加 `addScore` 分数
10. `zrangebyscore zsetName score1 score2`：返回 `score1` 到 `score2` 之间的元素，`zrangebyscore zsetName -inf +inf`：返回最大到最小之间的元素

交集并集改成 z 就行

## 高级数据类型

### `BitMap`操作命令

类似于一个 `bit` 数组，可以节省很大的空间，提高内存使用率（对位进行操作）

1. `setbit key offset val`：设置 `bitmap`，在 `offset` 的位置设置值为 `val`（0，1）
2. `getbit key offset`：查询 `offset` 位置的值
3. `bitcount key start end`：统计值为 1 的数量（范围：`start-end，end=-1为最后一个，-2为倒数第二个`）

#### `BitMap`与布隆过滤器

解决的问题：用来判断一个元素是否在一个集合中

布隆过滤器实际是一种算法，该算法由一个二进制数组和一个Hash算法组成

1. 根据值通过Hash算法放入到二进制数组中（取模）
2. 把想判断的元素通过同样的方式算出来对应的位置，取值，如果取出来是0，则该元素不在集合中，如果取出来是1，有可能在，也有可能不在（称为哈希冲突），也就是，0 是 100% 不在集合，1 则不确定
3. 用处：黑名单、邮件过滤、爬虫网址判重、缓存穿透
4. 如何应对哈希冲突？增大数组 or 增加 hash 函数

#### 布隆过滤器应用

##### 缓存穿透

Eg：用户注册，发起一个注册请求，写到数据库，然后会把这个数据写道缓存中，用户再次查询就能够用缓存；但是比如一些黑客等，注册->写数据库->更新缓存->查询用户(会有兜底方案，缓存查不到进入数据库查询)->查询不到（使用不存在的缓存key）->查数据库（刻意造成了缓存穿透）

如何解决？在用户查询的时候通过某种方法来快速判断用户是否在缓存中，如果不在，那么就不需要去数据库查询。（布隆过滤器）用户注册->更新布隆过滤器（把所有的user都放到bitmap中）->用户查询->通过布隆过滤器判断（不存在直接返回，存在继续下一步）->从缓存中读取

### `HyperLogLog`操作命令

用处：大型网站每天统计 `uv` 数据（独立访客、`ip` 等），需要去重处理，第一时间考虑 集合(`Set`) ，但是集合占用的存储空间过高,此时 `HyperLogLog` 就能够很好地解决问题。

`HyperLogLog` 提供了一种不精准的去重计数方案，误差很小，可以不考虑

1. `pfadd key element`：往 `key` 里面添加一个（多个）`element`
2. `pfcount key`：统计数量
3. `pfmerge destkey sourcekey `：合并两个（多个）数据集合，存储在 `destkey` 中

为什么 `HyperLogLog` 占用存储空间那么小？基于概率论伯努利试验和极大似然估算法。并且用了分桶优化

### GEO （地图）操作命令

支持存储地理位置信息来实现附件的人等功能，使用的是`zset`的一个结构（`type`来进行查看）

1. `geoadd key longitude latitude member`：往 `key` 里面添加一条经纬度的成员 `member`

## Redis高级特性

### Redis 慢查询及配置

什么是慢查询？慢查询是指数据库中查询时间超过指定阈值的SQL；

命令的流程：客户端发送命令->`redis` 排队 -> `redis` 执行命令（此处记录时长） -> 返回结果给客户端

Redis慢查询两个参数：多慢算慢？——设置阈值（默认10ms）；存放慢查询日志的条数

Redis慢查询记录存储在哪？存储在一个`slowlog`的链表中（内存），不能通过get、set来进行访问，通过 `slowlog get len`取出 `len` 条数据；`slowlog reset` 清空慢查询记录

慢查询的建议：

1. 阈值：线上高并发、高流量可以设置为1ms（1000）左右
2. 队列的大小：1000+，因为很长的命令会被截断进行记录
3. 慢查询的日志可以定期 `slowget` 进行日志持久化

### `PipeLine`

是一种通过批量操作多条命令来减少客户端和服务器之间通信次数，从而减少 `rtt`，提高 `Redis` 性能

### Redis 与 Lua 脚本

