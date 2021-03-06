## 简介
Java中的知识量大且琐碎，往往学习一个知识点的时候会忘记之前学习的知识点  
## 指导思想
* 提炼出高频知识，理解学会  
* 高频优先级最高，次高频次之，次次高频...
* 每天从高频学起 
## 前人经验
除开知识点，一定要准备好以下套路：

**个人介绍**： 需要准备1分钟和5分钟两个版本，包括学习经历、工作经历、项目经历、个人优势、一句话总结。一定要自己背得滚瓜烂熟，张口就来，中英文两份    
**抽象概念**： 当面试官问你是如何理解多线程的时候，你要知道从定义、来源、实现、问题、优化、应用方面系统性地回答  
**项目强化**： 至少与知识点的比例是五五开，所以必须针对简历中的两个以上的项目，形成包括【架构和实现细节】，【正常流程和异常流程的处理】，【难点+坑+复盘优化】三位一体的组合拳  
**压力练习**： 面试的时候难免紧张，可能会严重影响发挥，通过平时多找机会参与交流分享，或找人做压力面试来改善  
**表达练习**： 表达能力非常影响在面试中的表现，能否简练地将答案告诉面试官，可以通过给自己讲解的方式刻意练习  
**重点针对**： 面试官会针对简历提问，所以请针对简历上写的所有技术点进行重点准备  

## 项目问题  
### 1. 你的项目中都有哪些亮点  
### 2. 攻克过哪些项目难点  
* 接手项目安全加密整改，紧急上马，两天搞定；  
* 项目版本由1.0升级2.0，负责推进升级进度，变动点方案解决；  
* 与FBB项目对接，解决对接过程中出现的问题；负责最短路径，环链识别等核心算法的设计与开发；  
#### 4. 做工哪些技术支撑，攻克过哪些技术难点（详细）
* 微服务内存溢出问题定位及JVM调优-微服务执行还原算法时内存溢出，配置jvm参数-XX:+HeapDumpOnOutOfMemoryError，当微服务发生内存溢出时自动生成dump文件到指定目录下（也可以使用jmap生成），然后使用MAT(Eclipse Memory Analysis Tools) 打开dump文件进行分析，MAT中可以看到哪个对象比较大，然后再代码中找到大对象进行分析和修改；然后还使用jvm参数-XX:PrintGC对GC情况进行实时查看（主要看GC是发生在新生代还是老年代，对比GC前后的内存大小，分析GC效果怎么样，是否存在频繁fullGC），分析哪个还原阶段内存压力较大，然后分析修改代码，这样做可以及时发现内存溢出的潜在风险；有些还原算法比较复杂且数据量较大，如XXXX还原，XX万的网元，X百万的链路和端口，会还原出X千万级的结果，这种情况优化代码后仍需要大量的计算资源，我们就提高了JVM的最大堆大小-Xmx16G，由于计算最短路径时存在方法的递归调用，偶尔会导致栈内存溢出，我们给-Xss参数做了调优，由默认1m改成了2m（临时方案，后来重构了最短路径计算方法）另外还设置了-Xms初始堆大小为4G。
* 支撑同事使用zookeeper做分布式锁-我们的微服务还原任务对服务器的CPU及内存资源要求较高，所以代码中限制每次只能跑一个还原任务，防止出现资源耗尽导致程序宕机；另外微服务还提供了页面的相关功能，一些页面的查看功能也存在一定计算量，为了防止跑任务过程中同时进行一些页面的相关计算而增大微服务的压力，我们做了集群部署，使用Nginx做负载均衡，这样当我们在跑还原任务的时候，页面的一些查询请求交由另外一个微服务来处理；但这种集群模式存在两个问题：一是集群中一个微服务正在跑任务，这个时候发起一个新的任务请求，Nginx就会把请求转发到另外一个负载较低的微服务上；二是如果跑任务时出现服务器宕机，我们的任务会一直处于执行中的状态，这样无法发起新的还原任务，为了解决这两个问题提出使用zookeeper做分布式锁，即还原任务开始请求时连接zookeeper创建一个临时目录，如果有新的任务请求进来，就会判断zookeeper上是否存在这个临时目录，存在则任务发起失败，不存在则开始执行还原任务；执行任务过程中如果服务宕机，临时目录就会自动删除，也就不会影响发起新的还原任务。为什么用zookeeper而不用Redis？

## 反问面试官
1. 目前招聘的岗位是接替老员工的工作还是新成立的项目？
2. 开发过程中有规范的文档吗？比如明确的需求文档，设计文档，测试用例？

## 内容目录
### JVM
1. JVM内存结构
2. 类加载过程
3. 类加载机制-双亲委派
4. 逃逸分析技术
5. GC回收机制垃圾回收算法
6. 垃圾收集器
7. 三色标记法
8. Stop The World
9. JVM调优
10. Java内存模型-JMM
11. 先行发生原则-happens-before

### 并发&&多线程
1. 线程的声明周期
2. Java多线程的理解
3. 锁
4. volatile
5. ThreadLocal
6. 线程池
7. 并发工具包-CountDownLatch/Semaphore/CyclicBarrier
8. CAS

### 集合框架
1. HashMap
2. ArrayList与LinkedList的区别
3. ConcurrentHashMap原理

### MySQL
1. InnoDB与MyISAM区别
2. 数据库性能优化
3. SQL调优-explain分析SQL
4. 事务四大特性-原子性、一致性、隔离性、持久性
5. 什么是脏读、幻读、不可重复读
6. 事务的隔离级别
7. Innodb事务的redo和undo日志
8. MVCC底层原理
9. MySQL主从复制
10. MySQL基础架构
11. SQL语句在MySQL中是怎么执行的
12. 覆盖索引、回表
13. 索引下推
14. 数据库三大范式-了解
15. MySQL分库分表-了解

### Spring
1. BeanFactory和ApplicationContext区别
2. Spring的生命周期
3. Spring如何解决循环依赖
4. SpringAOP
5. Spring事务的传播行为
6. SpringMVC工作原理
7. SpringBoot自动配置原理
8. SpringBoot如何解决跨域问题

### Redis
1. 缓存穿透、缓存雪崩、缓存击穿原因及解决方案
2. Redis分布式锁及缺陷
3. Redis如何保证集群的高可用-主从复制和哨兵机制
4. Redis是单线程的，为什么还这么快
5. Redis数据淘汰策略
6. Redis的过期键的删除策略
7. Redis事务

### Zookeeper
1. zookeeper是什么 有什么用途
2. 命名服务、配置管理、集群管理
3. znode有几种类型 zookeeper的数据模型是怎样的
4. znode节点里面存储的是什么 每个节点的数据最大不能超过多少
5. znode节点上的监听机制
6. Zookeeper的特性
7. zookeeper是如何保证事务的顺序一致性
8. Zookeeper的服务器有几种角色 Zookeeper下Server有几种工作状态
9. ZooKeeper是如何保证主从节点数据一致性的
10. ZooKeeper选举机制
11. zk分布式锁的实现原理

### MyBatis
1. mybatis 中 #{}和 ${}的区别
2. 什么是SQL注入 如何避免
3. mybatis 的一级缓存和二级缓存
4. mybatis延迟加载的原理
5. Mybatis是如何进行分页的 分页插件的原理是什么
6. 几种分页方式-SQL分页 拦截器分页 RowBounds分页
7. MyBatis框架的优点和缺点
8. 实体类中的属性名和表中的字段名不一样怎么办-项目中做法：XML文件中使用resultMap自定义映射规则

### LVS&Keepalived
1. 回顾文档理解

### 分布式
1. CAP理论
2. 分布式锁
3. 分布式事务
4. ID生成方式
5. 一致性hash

### Java设计模式
1. 单例模式
2. 工厂模式
3. 观察者模式

### 数据结构与算法
1. 数组
2. 链表
3. 树
4. 图
