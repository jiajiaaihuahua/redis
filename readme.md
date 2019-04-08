# Redis 简明教程 
### 1、Redis 被称为key-value存储，在NoSQL数据库中被提到.我们可以使用 SET 关键字存储一个值 "fido"。
```NoSQL
   SET server:name "fido" 
```
Redis 会把数据永久的存储，因此我们可以查询刚才我们存储的值通过 GET
```NoSQL
    GET server.name => "fido"
```
> tip: 箭头表示期望的结果

### 2、Redis提供了一些命令给我们使用。比如 INCR，这个命令是自增用的类似于 i++
```NoSQL
    SET connections 10
    INCR connections => 11
    INCR connections => 12
    DEL connections
    INCR connections => 1
``` 
> 那为什么我们不使用 i++ 呢，因为，INCR相对于i++有一个好处，就是他的原子性。想象一下如果是两个不同的客户端在相同的时间把connections 同时加一，那么最终connections会是多少呢，对！结果会是只加一。那么如果使用了INCR，因为它具有的原子性，所以最终的结果是加二。
```NoSQL
    Client A reads count as 10.
    Client B reads count as 10.
    Client A increments 10 and sets count to 11.
    Client B increments 10 and sets count to 11.
```

### 3、Redis中的数据默认是没有过期时间的，但是你可以设置过期时间。设定过期时间的关键字是EXPIRE，查看过期时间是TTL命令
```NoSQL
    SET resource:lock "Redis Demo"
    EXPIRE resource:lock 120
```
> 上面的命令导致的最终结果就是resource:lock中的值的过期时间是120秒，如果你随时想知道他还有多久过期，可以用TTL查看，TTL会返回这个值还有多少秒会被删除。
```NoSQK
    TTL resource:lock => 113
    // after 113s
    TTL resource:lock => -2
```
> -2 表示那个键的值已经不存在了，如果是-1，就表示这个值没有过期的限制，tip:如果你重新设置这个键的值，他的TTL会被重置
```NoSQL
    SET resource:lock "Redis Demo 1"
    EXPIRE resource:lock 120
    TTL resource:lock => 119
    SET resource:lock "Redis Demo 2"
    TTL resource:lock => -1
```

### 4、Redis 也支持几个完整的数据结构，第一个就是列表List，一个集合是一系列的有序的值，对于集合的操作，有一些比较重要的命令，eg：RPUSH,LPUSH,LLEN,LRANGE，LPOP 以及 RPOP.
```NoSQL
    RPUSH friends "Alice"
    RPUSH friends "Bob"
```
> RPUSH是向集合的尾部加入新的值（ 如果执行的RPUSH，集合会自动创建，不用显示创建集合。）
```NoSQL
    LPUSH friends "Sam"
```
> LPUSH是在集合的开头添加值
> LRANGE 顾名思义就是一个范围，其实就是给一个开始下标和一个结束下标，得到这两个下标中间的所有值，得到的集合简称是原集合的子集，有点类似字符串的subString 函数。

```NoSQL
    LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"
    LRANGE friends 0 1 => 1) "Sam", 2) "Alice"
    LRANGE friends 1 2 => 1) "Alice", 2) "Bob"
```
> 0 -1 取的就是集合中的所有值，0，1 就是取的下标0和下标1的值。
```NoSQL
    LLEN friends => 3
```
>LLEN 就是返回集合当前的长度
```NoSQL
    LPOP friends => "Sam"
```
>LPOP 移出第一个元素，并且把这个元素返回，让你看下你干掉的是什么元素
```NoSQL
    LPOP friends => "Sam"
```
>LPOP 移出最后一个元素，并且把这个元素返回，让你看下你干掉的是什么元素