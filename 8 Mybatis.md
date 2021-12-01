### 1. mybatis 中 #{}和 ${}的区别
* #{}是占位符，$是拼接符；  
* #{}的SQL是预编译的（先将参数用?代替再编译，编译完成后使用PreparedStatement的set方法来赋值参数），${}是先替换参数再编译（处理${}时，就是把${}替换成变量的值再去编译）；  
* #{}能防止SQL注入，而${}不能防止SQL注入；    
* 一般能用#{}的就别用${}。MyBatis排序式使用order by动态参数时需要注意，用${}而不是#{}。  
### 2. 什么是SQL注入 如何避免
SQL注入就是攻击者提交带有恶意的数据与SQL语句进行字符串方式的拼接，使后台的SQL的语义发生变化，最终产生数据泄露的现象。
### 3. mybatis 的一级缓存和二级缓存
* 一级缓存
在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SelSession第一次查询后，MyBatis会将其放在缓存中，以后再查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，SqlSession都会取出当前缓存的数据，而不会再次发送SQL到数据库。  

![Uploading image.png…](https://img-blog.csdnimg.cn/20201126194312471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JhbnpodWFuaHU=,size_16,color_FFFFFF,t_70#pic_center)

一级缓存时执行commit，close，增删改等操作，就会清空当前的一级缓存；当对SqlSession执行更新操作（update、delete、insert）后并执行commit时，不仅清空其自身的一级缓存（执行更新操作的效果），也清空二级缓存（执行commit()的效果）。  
* 二级缓存  
二级缓存是SqlSessionFactory级别的缓存，同一个SqlSessionFactory产生的SqlSession都共享一个二级缓存，二级缓存中存储的是数据，当命中二级缓存时，通过存储的数据构造对象返回。查询数据的时候，查询的流程是二级缓存>一级缓存>数据库。    

![Uploading image.png…](https://camo.githubusercontent.com/41fc9a7c3033627e868f657a93288bf19120c8c73bc380987bc004fc681da71e/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132363139343532383836392e706e673f782d6f73732d70726f636573733d696d6167652f77617465726d61726b2c747970655f5a6d46755a33706f5a57356e6147567064476b2c736861646f775f31302c746578745f6148523063484d364c7939696247396e4c6d4e7a5a473475626d56304c324a68626e706f645746756148553d2c73697a655f31362c636f6c6f725f4646464646462c745f3730237069635f63656e746572)

在配置文件中 开启二级缓存的总开关 ：  
在mapper映射文件中开启二级缓存 ：  
参数名属性eviction收回策略flushInterval刷新间隔size引用数目readOnly只读  
关于eviction的各个参数属性:

参数名属性eviction="LRU"最近最少使用的:移除最长时间不被使用的对象。 （默认）  
> eviction="FIFO"先进先出:按对象进入缓存的顺序来移除它们。  
> eviction="SOFT"软引用:移除基于垃圾回收器状态和软引用规则的对象。  
> eviction="WEAK"弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。   
注意实体类要实现Serializable    
### 4. mybatis延迟加载的原理
### 5. Mybatis是如何进行分页的 分页插件的原理是什么
### 6. 几种分页方式-SQL分页 拦截器分页 RowBounds分页
### 7. MyBatis框架的优点和缺点
### 8. 实体类中的属性名和表中的字段名不一样怎么办-项目中做法：XML文件中使用resultMap自定义映射规则
