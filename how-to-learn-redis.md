++redis作为一个C语言编写的内存型nosql数据，在性能以及编码风格上都十分优秀，之前一直想要学习。这里借鉴了别人的分类学习方法，根据不同的模块分类文件。++ 

++后续的学习也将按照划分的不同阶段进行，同时也可以锻炼md语法。一举两得++ 

*链接* [link](https://www.zhihu.com/question/28677076) 

## 第一阶段：数据结构部分 
- 内存分配 zmalloc.c   zmalloc.h
- 动态字符串 sds.c  sds.h
- 双端链表 adlist.c adlist.h
- 字典 dict.h dict.c
- 跳表   server.h    zskiplist   zskiplistNode  t_zset.c 中zsl开头的函数
- 基数统计 hyperloglog.c    hllhdr结构   hll开头的函数

## 第二阶段：redis内存编码结构
- 整数集合数据结构 intset.h  intset.c
- 压缩列表数据结构 ziplist.h  ziplist.c

## 第三阶段：熟悉redis数据类型实现
- 对象系统 object.c
- 字符串键 t_string.c
- 列表键 t_list.c
- 散列键 t_hash.c
- 集合键 t_set.c
- 有序集合键 t_zset.c  除zsl开头的函数之外的所有函数
- HyperLogLog键  hyperloglog.c中所有pf开头的函数

## 第四阶段：熟悉redis单机数据库的实现
- 数据库实现 redis.h 文件中的redisDb结构， 以及db.c文件
- 通知功能实现  notify.c
- RDB持久化(RDB persistence) rdb.c
- AOF持久化(AOF persistence) aof.c
- 发布和订阅模块  redis.h --- pubsubPattern结构   pubsub.c
- 事务  redis.h --- multiState结构  multiCmd结构   multi.c

## 第五阶段：熟悉client server端代码实现
- 事件处理模块  ae.c  ae_epoll.c  ae_evport.c  ae_kqueue.c  ae_select.c
- 网络库相关  anet.c  networking.c
- server端  redis.c
- client端  redis-cli.c
- lua脚本  scripting.c
- 慢查询 slowlog.c
- 监视 montior.c

## 第六阶段：redis多机分布式实现
- replication部分 replication.c
- redis哨兵   sentinel.c
- 集群 cluster.c


## 其他测试文件
- memtest.c 内存检测
- redis-benchmark.c   redis性能测试
- redis_check_aof.c  更新日志检查的实现
- redis_check_dump.c   用于本地数据库检查的实现
- testhelp.c   C风格的小型测试框架


## 相关的工具类实现
- bitops.c  GETBIT  SETBIT等二进制位操作命令的实现
- debug.c 调试使用
- endianconv.c 高低位转换，一般网络字节序与主机字节序转换
- help.h  辅助命令的提示信息
- lzf_c.c lzf_d.c 压缩算法系列
- rand.c  随机数产生
- release.c  发布时使用
- sha1.c sha加密算法的实现
- util.c 通用工具实现
- crc64.c 循环冗余校验
- sort.c SORT命令的实现
- bio.c  backgroundIO，开启后台线程使用
- latency.c  延迟类
- migrate.c 命令迁移类，命令的还原与迁移
- pqsort.c 排序算法类
- rio.c  redis定义的一个IO类
- syncio.c  用于同步socket和文件IO的操作