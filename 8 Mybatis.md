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
MyBatis 实现一对一有几种方式?具体怎么操作的？ 有联合查询和嵌套查询 联合查询是几个表联合查询,只查询一次, 通过在resultMap 里面配置 association 节点配置一对一的类就可以完成； 嵌套查询是先查一个表，根据这个表里面的结果的 外键 id，去再另外一个表里面查询数据,也是通过 association 配置，但另外一个表的查询通过 select 属性配置。

select * from class c,teacher t where c.teacher_id=t.t_id and c.c_id=#{id}

MyBatis 实现一对多有几种方式,怎么操作的？ 有联合查询和嵌套查询。 联合查询是几个表联合查询,只查询一次,通过在resultMap 里面的 collection 节点配置一对多的类就可以完成； 嵌套查询是先查一个表,根据这个表里面的 结果的外键 id,再去另外一个表里面查询数据,也是通过配置 collection,但另外一个表的查询通过 select 节点配置 select * from class c,teacher t,student s where c.teacher_id=t.t_id and c.c_id=s.class_id and c.c_id=#{id} Mybatis 是否支持延迟加载？如果支持，它的实现原理是什么？ Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。它的原理是，使用 CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName()，拦截器 invoke()方法发现 a.getB()是null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName()方法的调用。这就是延迟加载的基本原理。 当然了，不光是 Mybatis，几乎所有的包括 Hibernate，支持延迟加载的原理都 是一样的。  
### 5. Mybatis是如何进行分页的 分页插件的原理是什么
Mybatis 使用 RowBounds 对象进行分页，也可以直接编写 sql 实现分页，也可以使用Mybatis 的分页插件。 分页插件的原理：实现 Mybatis 提供的接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql。 举例：select * from student，拦截 sql 后重写为：select t.* from （select * from student）tlimit 0，10
### 6. 几种分页方式-SQL分页 拦截器分页 RowBounds分页
* sql分页
<select id="queryStudentsBySql" parameterType="map" resultMap="studentmapper">
        select * from student limit #{currIndex} , #{pageSize}
</select>
* 拦截器分页

* RowBouds分页
数据量小时，RowBounds不失为一种好办法。但是数据量大时，实现拦截器就很有必要了。  

mybatis接口加入RowBounds参数  
  public List<UserBean> queryUsersByPage(String userName, RowBounds rowBounds);
  
  @Override
  @Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.SUPPORTS)
    public List<RoleBean> queryRolesByPage(String roleName, int start, int limit) {
        return roleDao.queryRolesByPage(roleName, new RowBounds(start, limit));
    }
### 7. MyBatis框架的优点和缺点
  一、MyBatis框架的优点：

与JDBC相比，减少了50%以上的代码量。

MyBatis是最简单的持久化框架，小巧并且简单易学。

MyBatis相当灵活，不会对应用程序或者数据库的现有设计强加任何影响，SQL写在XML里，从程序代码中彻底分离，降低耦合度，便于统一管理和优化，并可重用。

提供XML标签，支持编写动态SQL语句。

提供映射标签，支持对象与数据库的ORM字段关系映射。

二、MyBatis框架的缺点：

SQL语句的编写工作量较大，尤其是字段多、关联表多时，更是如此，对开发人员编写SQL语句的功底有一定要求。

SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

三、MyBatis框架适用场合：

MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。

对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。
### 8. 实体类中的属性名和表中的字段名不一样怎么办-项目中做法：XML文件中使用resultMap自定义映射规则
  <select id="getEmployeeById" resultMap="myMap">
		select * from employees where id = #{id}
	</select>
	
	<!-- 自定义高级映射 -->
    <resultMap type="com.atguigu.mybatis.entities.Employee" id="myMap">
    	<!-- 映射主键 -->
    	<id column="id" property="id"/>
    	<!-- 映射其他列 -->
    	<result column="last_name" property="lastName"/>
    	<result column="email" property="email"/>
    	<result column="salary" property="salary"/>
    	<result column="dept_id" property="deptId"/>
    </resultMap>
### 9. Mybatis执行批量插入，能返回数据库主键列表吗？
