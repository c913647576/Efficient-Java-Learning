### 1. JVM内存结构
**JVM内存结构图如下：**  

![image](https://camo.githubusercontent.com/f1142530cbc3f678e46cd6ea61db318c272d6432055945f7ddb6df33b4db5403/68747470733a2f2f696d61676573323031352e636e626c6f67732e636f6d2f626c6f672f313133353139332f3230313730342f313133353139332d32303137303430343130353831383931302d3132313936323236372e6a7067)  

**内存比例**  

![image](https://user-images.githubusercontent.com/69468325/143801176-7efefdbe-4bad-4bbe-865f-33e05848ddc6.png)

**从图中可以看出：**  
* 堆大小 = 新生代 + 老年代。其中，堆的大小可以通过参数 –Xms、-Xmx 来指定。 
* 新生代 ( Young ) 与老年代 ( Old ) 的比例的值为 1:2 ( 该值可以通过参数 –XX:NewRatio 来指定 )，即：新生代 ( Young ) = 1/3 的堆空间大小。老年代 ( Old ) = 2/3 的堆空间大小。
* 新生代 ( Young ) 被细分为 Eden 和 两个 Survivor 区域，这两个 Survivor 区域分别被命名为 from 和 to，以示区分。 默认的，Eden : from : to = 8 : 1 : 1 ( 可以通过参数 –XX:SurvivorRatio 来设定 )，即： Eden = 8/10 的新生代空间大小，from = to = 1/10 的新生代空间大小。 
* JVM 每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。 因此，新生代实际可用的内存空间为 9/10 ( 即90% )的新生代空间。
### 2. 类加载过程
### 3. 类加载机制-双亲委派
### 4. 逃逸分析技术
### 5. GC回收机制垃圾回收算法
### 6. 垃圾收集器
### 7. 三色标记法
### 8. Stop The World
### 9. JVM调优
### 10. Java内存模型-JMM
### 11. 先行发生原则-happens-before
