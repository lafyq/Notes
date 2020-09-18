## MySql







### 事务（transaction）

##### 1. 事务的四大特性(ACID)

```java
* 事务的四大特性(ACID)
	1. 原子性（Atomicity）：一个事务是一个不可分割的工作单位，“要么不做，要么全做”。(Nothing or All) 
	2. 一致性（Consistency）：事务对数据库的作用是数据库从一个一致状态转变到另一个一致状态。所谓数据库的一致状态是指数据库中的数据满足完整性约束.(实体完整性, 参照完整性, 用户自定义完整性)
	3. 隔离性（Isolation） ：多个事务之间，相互独立。
	4. 持久性（Durability） ：当事务提交或回滚后，数据库会持久化的保存数据。 
```

```mysql
补充：
	完整性约束：
		1. 实体完整性规则：主码的值不能为空或部分为空.
		2. 参照完整性规则：指一个关系的外码取值必须是相关关系中主码的有效值或空值.
		3. 用户自定义完整性：域(约束条件)必须满足语义要求.
```

##### 1.2 对一致性的理解

```mysql
	事务里的原子性，隔离性，持久性都是依赖于数据库的具体实现的，而唯独一致性是依赖于数据库自动检查和我们开发者共同实现的. 
	事务的一致性就是：数据库从一个一致状态转变到另一个一致状态，这里的所谓的数据库一致状态的意思是：数据库中的数据满足完整性约束(也就是实体完整性，参照完整性，用户自定义完整性)，因为里边有用户自定义完整性，所以这就需要我们开发者自己来定义正确的约束条件来确保逻辑正确.
	所以说事务的一致性是依赖于数据库的原子性，隔离性，持久性和我们程序员定义正确的约束来实现的.
```

```mysql
补充：
	一致性与原子性的区别 与联系？
		1. 原子性的侧重点是：事务的提交与回滚(过程).
		2. 一致性的侧重点是：事务执行前后的状态(结果).
		3. 事务里的一致性是基于原子性，隔离性，持久性和我们程序员定义正确的约束来实现的.
```



##### 2. 并发导致不一致性的原因

```java
* 读操作：
	 脏读：一个事务读到了另一个事务还未提交的数据。
	 不可重读：一个事务读到了另一个事务提交的数据，导致前后多次查询结果不一样。
	 幻读：一个事务读到另一个事务已提交的插入的数据，导致前后多次查询结果不一样。

* 写操作：
	 丢失更新：指一个事务去修改数据库，另一个事务也去修改数据库，最后的那个事务，不管是提交还是回滚，都会造成前一个事务的数据更新失败。

```



##### 3. 设置隔离级别(针对读操作)

```java
1. READ UNCOMMITTED【读未提交】
	* 引发问题：脏读
	* 指的是：一个事务可以读取到另一个事务还未提交的数据。这就会引发 “脏读”。读取到的是数据库内存中的数据，而并非真正磁盘上的数据。

2. READ COMMITTED 【读已提交】	-- oracle 默认的隔离级别
	* 解决：脏读。	引发问题：不可重读
	* 与前面的读未提交刚好相反，这个隔离级别是，只能读取到其他事务已经提交的数据，那些没有提交的数据是读不出来的。但是这会造成一个问题是： 前后读取到的结果不一样。 发生了不可重复读!!!, 所谓的不可重复读，就是不能执行多次读取，否则出现结果不一 。

3. REPEATABLE READ 【重复读】	--MySql 默认的隔离级别
	* 解决：脏读，不可重读。	未解决：幻读
	* 该隔离级别， 可以让事务在自己的会话中重复读取数据，并且不会出现结果不一样的状况，即使其他事务已经提交了，也依然还是显示以前的数据。

4. SERIALIZABLE 【可串行化】 
	* 解决：脏读，不可重读，幻读
	* 如果有一个连接的隔离级别设置为了串行化 ，那么谁先打开了事务， 谁就有了先执行的权利， 谁后打开事务，谁就只能得着，等前面的那个事务，提交或者回滚后，才能执行。  但是这种隔离级别一般比较少用。 容易造成性能上的问题。 效率比较低。
```

```java
5. 注意：隔离级别从小到大安全性越来越高，但是效率越来越低
		* 数据库查询隔离级别：
			* select @@tx_isolation;
		* 数据库设置隔离级别：
			* set global transaction isolation level 隔离级别字符串;
```



###### 3.1 读未提交

###### 3.2 读已提交

###### 3.3 重复读

###### 3.4 可串行化







##### 4. 解决丢失更新(针对写操作)

###### 4.1 丢失更新是什么？

```java
 * 两个事务同时对一个数据进行修改，一个事务把另一个事物的修改结果给覆盖掉，导致查询不到更新之后的结果（“幻读”）
```



![](C:\Users\niefeng\Desktop\aaaa.png)





###### 4.2 如何解决丢失更新？

```java
1. 悲观锁：料定自己会丢失更新，所以不允许别人在自己执行更新的时候进行修改
		* 可以在查询的时候，加入 for update （数据库的排它锁机制）

2. 乐观锁：觉得丢失更新是程序员可控的。
		* 使用版本控制的机制（类似于git里边的那个）
```



###### 4.3 悲观锁

```java
1. 悲观锁：料定自己会丢失更新，所以不允许别人在自己执行更新的时候进行修改
		* 可以在查询的时候，加入 for update （数据库的排它锁机制）
```



![](C:\Users\niefeng\Desktop\bbbb.png)



###### 4.4 乐观锁

```java
2. 乐观锁：觉得丢失更新是程序员可控的。
		* 使用版本控制的机制（类似于git里边的那个）
```

![](C:\Users\niefeng\Desktop\cccc.png)







```mysql
1.什么是SQL？
	Structured Query Language：结构化查询语言
	其实就是定义了操作所有关系型数据库的规则。每一种数据库操作的方式存在不一样的地方，称为“方言”。
	
2.SQL通用语法
	1. SQL 语句不区分大小写，以分号结尾。
	2. 注释：# balabala
	
3. SQL分类
	1) DDL(Data Definition Language)数据定义语言
		用来定义数据库对象：数据库，表，列等。关键字：create, drop,alter 等
	2) DML(Data Manipulation Language)数据操作语言
		用来对数据库中表的数据进行增删改。关键字：insert, delete, update 等
	3) DQL(Data Query Language)数据查询语言
		用来查询数据库中表的记录(数据)。关键字：select, where 等
	4) DCL(Data Control Language)数据控制语言(了解)
		用来定义数据库的访问权限和安全级别，及创建用户。关键字：GRANT， REVOKE 等
```





#### DDL： 操作数据库、表

```java
1. 操作数据库：CRUD
    C(Create):创建
    R(Retrieve)：查询
    U(Update):修改
    D(Delete):删除
```



###### 1. 操作数据库

```mysql
* 创建数据库: create database db_name;
* 创建数据库，判断不存在，再创建: create database if not exists db_name;
* 创建数据库，并指定字符集: create database db_name character set character_name;

举例：
    # 创建db4数据库，判断是否存在，并制定字符集为utf8
    create database if not exists db4 character set utf8;

* 查询所有数据库的名称: show databases;
* 查询某个数据库的字符集: show create database db4;

* 查询当前正在使用的数据库名称: select database();
* 使用数据库: use db4;

* 修改数据库的字符集: alter database db4 character set gbk;

* 删除数据库: drop database db4;
* 判断数据库存在，存在再删除: drop database if exists db4;
```



###### 2. 操作表

```mysql
create table 表名(
    列名1 数据类型1,
    列名2 数据类型2,
    ....
    列名n 数据类型n
);
* 注意：最后一列，不需要加逗号（,）
* 数据库类型：
	1. int：整数类型    age int
	2. double:小数类型  
		* score double(5,2)  
		* 总共5位, 小数点后占用2位
	3. date:日期，只包含年月日   yyyy-MM-dd
	4. datetime:日期，包含年月日时分秒	   yyyy-MM-dd HH:mm:ss
	5. timestamp:时间错类型	包含年月日时分秒	 yyyy-MM-dd HH:mm:ss	
		* 如果将来不给这个字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值
	6. varchar：字符串
		* name varchar(20):姓名最大20个字符
		* zhangsan 8个字符  张三 2个字符
```



```mysql
* 创建表
	create table student(
		id int,
		name varchar(32),
		age int ,
		score double(4,1),
		birthday date,
		insert_time timestamp
	);
	
* 复制表: create table 表名 like 被复制的表名;

* 查询某个数据库中所有的表名称: show tables;
* 查询表结构: desc 表名;

* 修改表名: alter table 表名 rename to 新的表名;
* 修改表的字符集: alter table 表名 character set 字符集名称;

* 添加一列: alter table 表名 add 列名 数据类型;
* 修改列名称: alter table 表名 change 列名 新列别 新数据类型;
* 修改列的类型: alter table 表名 modify 列名 新数据类型;
* 删除列: alter table 表名 drop 列名;

* 删除表: drop table 表名;
* 先判断存在，再删除表: drop table if exists 表名;
```





#### DML：增删改表中数据

```mysql
1. 添加数据: insert into 表名(列名1,列名2,...列名n) values(值1,值2,...值n);
	
2. 删除数据: delete from 表名 [where 条件]		# 不加条件会删除表中所有数据
3. 删除表中所有记录: TRUNCATE TABLE 表名; # 删除所有时推荐使用，效率更高。先删除表，然后再创建一张一样的表。

3. 修改数据: update 表名 set 列名1 = 值1, 列名2 = 值2,... [where 条件];  # 不加条件会改全部
```



#### DQL：查询表中的记录

```mysql
* select * from 表名;

* 结构: select ... from ... where ... group by ... having ... order by ... limit ... ;

* 语法：
	select
		字段列表
	from
		表名列表
	where
		条件列表
	group by
		分组字段
	having
		分组之后的条件
	order by
		排序
	limit
		分页限定
```



```mysql
基础查询
	* as : 起别名
	* distinct : 去除重复


条件查询
	1. where子句后跟条件	 # 这些都是where子句后面跟的条件
	2. 运算符
		* > 、< 、<= 、>= 、= 、<>
		* BETWEEN...AND   注意：这里between 60 and 100 相当于[60, 100]
		* IN( 集合) 
		* LIKE：模糊查询
			* 占位符：
				* _:单个任意字符
				* %：多个任意字符
		* IS NULL  (IS NOT NULL)
		* and  或 &&
		* or  或 || 
		* not  或 !
```

```mysql
1. 查询年龄大于等于18岁的同学
select * from student where age >= 18

2. 查询年龄再[20, 30]岁之间的同学
select * from student where age between 20 and 30;

3. 查询年龄为18, 28, 38的同学
select * from student where age in (18, 28, 38);

4. 查询英语成绩不为空的同学
select * from student where english is not null;

5. 查询第二个字为"化"的姓名
select * from student where name like '_化%';

6. 查询数学成绩大于等于60并且姓马的同学
select * from student where math >= 60 and name like '马%';
```





##### 排序查询

```mysql
排序查询：
	* 语法: order by 排序字段1 排序方式1, 排序字段2 排序方式2;
	* 排序方式：
		* ASC：升序，默认的。
		* DESC：降序。
	
```



```mysql
-- 按照数学成绩降序查询，同分则按英语成绩降序查询
select * from student order by math desc, english desc;
```



##### 聚合函数

```mysql
聚合函数：将一列数据作为一个整体，进行纵向的计算。
count：计算个数
max：最大值
min：最小值
sum：总数
avg：平均数

举例: select count(id) from student3;		-- 查询学生总人数

注意：
	1. 聚合函数会进行空值的排除(也就是不计算 NULL值)
	2. 聚合函数查询出来的结果都是 单行单列的
```



##### 分组查询

```mysql
-- 查询男女生人数
select sex, count(id) from student3 group by sex;

-- 查询男女生的数学平均成绩
select sex, avg(math) from student3 group by sex;

-- 查询男女生的数学平均成绩，大于70分才能参与计算
select sex, avg(math) from student3 where math > 70 group by sex;

-- 查询男女生的数学平均成绩，大于70分才能参与计算，若大于70分的人数不足3人则不进行计算
select sex, avg(math) from student3 where math > 70 group by sex having count(id) > 2;
```



```mysql
注意：
为什么？分组之后查询的字段：分组字段 or 聚合函数
答： 因为查询字段写(*, 具体的id或者姓名)都是没有任何意义的，我们已经把男同学和女同学分别当作一个整体来看了，那么这个整体就不应该出现某个记录的具体信息，而应该是这个整体的共性内容。


面试题: where 和 having有什么区别？
答：都是限定查询，一个是限定条件，一个是限定结果。
1. where是在分组之前进行限定，如果不满足条件，则不参与分组; having是在分组之后进行限定，如果不满足结果，则不会被查询出来。
2. where后不可以跟聚合函数; having后面可以进行聚合函数的判定。
```





##### 分页查询

```mysql
分页查询
	1. 语法：limit 开始的索引,每页查询的条数;
	2. 公式：开始的索引 = （当前的页码 - 1） * 每页显示的条数
		-- 每页显示3条记录 

		SELECT * FROM student LIMIT 0,3; -- 第1页
		
		SELECT * FROM student LIMIT 3,3; -- 第2页
		
		SELECT * FROM student LIMIT 6,3; -- 第3页

	3. limit 是一个MySQL"方言"
```







### 约束

##### 1. 非空约束

```mysql
非空约束：not null，值不能为null
	1. 创建表时添加约束
		CREATE TABLE stu(
			id INT,
			NAME VARCHAR(20) NOT NULL   -- 约束name为非空
		);
	2. 创建表完后，添加非空约束
		ALTER TABLE stu MODIFY NAME VARCHAR(20) NOT NULL;

	3. 删除name的非空约束
		ALTER TABLE stu MODIFY NAME VARCHAR(20);
```



##### 2. 唯一约束

```mysql
* 唯一约束：unique，值不能重复
	1. 创建表时，添加唯一约束
		CREATE TABLE stu(
			id INT,
			phone_number VARCHAR(20) UNIQUE -- 添加了唯一约束
		
		);
		* 注意mysql中，唯一约束限定的列的值可以有多个null (MySQL中null表示不确定的值)
		
 	2. 删除唯一约束
		ALTER TABLE stu DROP INDEX phone_number;	-- 注意：这个跟别的约束删除方式不太一样
	
	3. 在创建表后，添加唯一约束
		ALTER TABLE stu MODIFY phone_number VARCHAR(20) UNIQUE;

```





##### 3. 主键约束

```mysql
主键约束：primary key。
	1. 注意：
		1. 含义：非空且唯一
		2. 一张表只能有一个字段为主键
		3. 主键就是表中记录的唯一标识

	2. 在创建表时，添加主键约束
		create table stu(
			id int primary key,-- 给id添加主键约束
			name varchar(20)
		);

	3. 删除主键
		-- 错误 alter table stu modify id int ;
		ALTER TABLE stu DROP PRIMARY KEY;  -- （这里也跟删除unique有点不一样！！！）

	4. 创建完表后，添加主键
		ALTER TABLE stu MODIFY id INT PRIMARY KEY;
```

```mysql
主键的自动增长：auto_increment
	1.  概念：如果某一列是数值类型的，使用 auto_increment 可以来完成值得自动增长

	2. 在创建表时，添加主键约束，并且完成主键自增长
		create table stu(
			id int primary key auto_increment,-- 给id添加主键约束
			name varchar(20)
		);
		
	3. 删除自动增长
		ALTER TABLE stu MODIFY id INT;
	4. 添加自动增长
		ALTER TABLE stu MODIFY id INT AUTO_INCREMENT;
```



##### 4. 外键约束

```mysql
* 外键约束：foreign key,让表于表产生关系，从而保证数据的正确性。
	0. 补充：外键是在从表上的.  *(也就是在1:n中 n的那一方)
	1. 在创建表时，可以添加外键
		* 语法：
			create table 表名(
				....
				外键列
				constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)
			);

	2. 删除外键
		ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;

	3. 创建表之后，添加外键
		ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称);

	4. 举例：
	alter table emp add constraint emp_dept_fk foreign key (dept_id) references dept(id);
```

```mysql
补充：级联操作（弊 > 利....但是要知道！！！）
		1. 添加级联操作
			语法: ALTER TABLE 表名 ADD CONSTRAINT 外键名称 
			举例: FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称) ON UPDATE CASCADE;
		2. 分类：
			1. 级联更新: ON UPDATE CASCADE 
			2. 级联删除: ON DELETE CASCADE 
			
	3. 在实际开发中，一般不使用级联操作！！！
```





### 数据库设计

```mysql
1. 多表之间的关系
	1. 分类：
		1. 一对一(了解)：
			* 如：人和身份证
			* 分析：一个人只有一个身份证，一个身份证只能对应一个人
			* 实现方式：一对一关系实现，可以在任意一方添加唯一外键指向另一方的主键。
			
		2. 一对多(多对一)：
			* 如：部门和员工
			* 分析：一个部门有多个员工，一个员工只能对应一个部门
			* 实现方式：在多的一方建立外键，指向一的一方的主键。
			
		3. 多对多：
			* 如：学生和课程
			* 分析：一个学生可以选择很多门课程，一个课程也可以被很多学生选择
			* 实现方式：多对多关系实现需要借助第三张中间表。中间表至少包含两个字段，这两个字段作为第三张表的外键，分别指向两张表的主键。
```

```mysql
3. 案例  
	# day0917 温馨提示：建议动手实现一下！！！

		-- 创建旅游线路分类表 tab_category
		-- cid 旅游线路分类主键，自动增长
		-- cname 旅游线路分类名称非空，唯一，字符串 100
		CREATE TABLE tab_category (
			cid INT PRIMARY KEY AUTO_INCREMENT,
			cname VARCHAR(100) NOT NULL UNIQUE
		);
		
		-- 创建旅游线路表 tab_route
		/*
		rid 旅游线路主键，自动增长
		rname 旅游线路名称非空，唯一，字符串 100
		price 价格
		rdate 上架时间，日期类型
		cid 外键，所属分类
		*/
		CREATE TABLE tab_route(
			rid INT PRIMARY KEY AUTO_INCREMENT,
			rname VARCHAR(100) NOT NULL UNIQUE,
			price DOUBLE,
			rdate DATE,
			cid INT,
			FOREIGN KEY (cid) REFERENCES tab_category(cid)
		);
		
		-- 创建用户表 tab_user
		/* 
		uid 用户主键，自增长
		username 用户名长度 100，唯一，非空
		password 密码长度 30，非空
		name 真实姓名长度 100
		birthday 生日
		sex 性别，定长字符串 1
		telephone 手机号，字符串 11
		email 邮箱，字符串长度 100
		*/
		CREATE TABLE tab_user (
			uid INT PRIMARY KEY AUTO_INCREMENT,
			username VARCHAR(100) UNIQUE NOT NULL,
			PASSWORD VARCHAR(30) NOT NULL,
			NAME VARCHAR(100),
			birthday DATE,
			sex CHAR(1) DEFAULT '男',
			telephone VARCHAR(11),
			email VARCHAR(100)
		);
		
		
		-- 创建收藏表 tab_favorite
		/*
		rid 旅游线路 id，外键
		date 收藏时间
		uid 用户 id，外键
		rid 和 uid 不能重复，设置复合主键，同一个用户不能收藏同一个线路两次
		*/
		CREATE TABLE tab_favorite (
			rid INT, -- 线路id
			DATE DATETIME,
			uid INT, -- 用户id
			-- 创建复合主键
			PRIMARY KEY(rid,uid), -- 联合主键
			FOREIGN KEY (rid) REFERENCES tab_route(rid),
			FOREIGN KEY(uid) REFERENCES tab_user(uid)
		);
```



### 数据库设计范式

```java
1. 数据库范式：
    * 概念：设计数据库时，需要遵循的一些规范。要遵循后边的范式要求，必须先遵循前边的所有范式要求，设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。
    * 目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。
    // 掌握三大范式即可！！！

2. 三大范式(掌握)：
	1. 第一范式（1NF）：每一列都是不可分割的原子数据项
	2. 第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主码的部分函数依赖）
    3. 第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）

                                                                    
3. 几个概念：
                                                                                                ** 函数依赖：A-->B,如果通过A属性(属性组)的值，可以确定唯一B属性的值。则称B依赖于A
		  例如：学号-->姓名。  （学号，课程名称） --> 分数
                                                                                                1. 完全函数依赖：A-->B， 如果A是一个属性组，则B属性值得确定需要依赖于A属性组中所有的属性值。
		   例如：（学号，课程名称） --> 分数
                                                                                                2. 部分函数依赖：A-->B， 如果A是一个属性组，则B属性值得确定只需要依赖于A属性组中某一些值即可。              例如：（学号，课程名称） -- > 姓名
                                                                                                3. 传递函数依赖：A-->B, B-->C . 如果通过A属性(属性组)的值，可以确定唯一B属性的值，在通过B属性（属性组）的值可以确定唯一C属性的值，则称 C 传递函数依赖于A
			例如：学号-->系名，系名-->系主任
                                                                                                5. 码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性(属性组)为该表的码
			例如：该表中码为：（学号，课程名称）
		
        * 主属性：码属性组中的所有属性
		* 非主属性：除了码属性组的属性
```



### 数据库的备份和还原（了解）

```mysql
1. 命令行：
	* 语法：
		* 备份： mysqldump -u用户名 -p密码 数据库名称 > 保存的路径
		* 还原：
			1. 登录数据库: mysql -uroot -p
			2. 创建数据库: create database db1;
			3. 使用数据库: use db1;
			4. 执行文件。source 文件路径
2. 图形化工具：
```





### 多表查询

##### 0. 直接多表查询

```mysql
select * from emp, dept;

# 查询出来的两个表的笛卡尔积
# 笛卡尔积：有两个集合A,B. 取这两个集合的所有组成情况。
```



##### 1. 内连接

```mysql
1. 语法: select 字段列表 from 表1 inner join 表2 on 条件;

2. 查询出来的是'两个表数据的交集'.

3. 隐式内连接: 
		select * from emp, dept where emp.dept_id = dept.id;

4. 显式内连接: 
		select * from emp inner join dept on emp.dept_id = dept.id;

```



##### 2. 外连接

```mysql
1. 左外连接: 
	* 语法: select 字段列表 from 表1 left [outer] join 表2 on 条件;
	* 查询的是'左表所有数据以及其交集部分'.
	* 举例：
		-- 查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
		select emp.*, dept.name from emp left join dept on emp.dept_id = dept.id;

2. 使用左外连接时，左表 left join 右表 on 条件; -->查询左表全部, 右表的交集部分

3. 右外连接同理，一般我们就掌握左外连接即可.
```



##### 3. 子查询

```mysql
1. 概念：查询中嵌套查询，称嵌套查询为子查询。

2. 子查询为单行单列时，用运算符(<, >, =, >=...)
   子查询为多行单列时，用运算符( in )
   子查询为多行多列时，直接用内连接即可，没必要用子查询

3. 举例：
	-- 查询工资高于平均工资的人
	select * from emp where salary > (select avg(salary) from emp);

	-- 查询市场部和财务部的人的信息
	select * from emp where dept_id in (select id from dept where name='市场部' or name='财务部');
```



### 多表查询练习(大练习)

```mysql
-- 部门表
CREATE TABLE dept (
  id INT PRIMARY KEY PRIMARY KEY, -- 部门id
  dname VARCHAR(50), -- 部门名称
  loc VARCHAR(50) -- 部门所在地
);

-- 添加4个部门
INSERT INTO dept(id,dname,loc) VALUES 
(10,'教研部','北京'),
(20,'学工部','上海'),
(30,'销售部','广州'),
(40,'财务部','深圳');

-- 职务表，职务名称，职务描述
CREATE TABLE job (
  id INT PRIMARY KEY,
  jname VARCHAR(20),
  description VARCHAR(50)
);

-- 添加4个职务
INSERT INTO job (id, jname, description) VALUES
(1, '董事长', '管理整个公司，接单'),
(2, '经理', '管理部门员工'),
(3, '销售员', '向客人推销产品'),
(4, '文员', '使用办公软件');

-- 员工表
CREATE TABLE emp (
  id INT PRIMARY KEY, -- 员工id
  ename VARCHAR(50), -- 员工姓名
  job_id INT, -- 职务id
  mgr INT , -- 上级领导
  joindate DATE, -- 入职日期
  salary DECIMAL(7,2), -- 工资
  bonus DECIMAL(7,2), -- 奖金
  dept_id INT, -- 所在部门编号
  CONSTRAINT emp_jobid_ref_job_id_fk FOREIGN KEY (job_id) REFERENCES job (id),
  CONSTRAINT emp_deptid_ref_dept_id_fk FOREIGN KEY (dept_id) REFERENCES dept (id)
);

-- 添加员工
INSERT INTO emp(id,ename,job_id,mgr,joindate,salary,bonus,dept_id) VALUES 
(1001,'孙悟空',4,1004,'2000-12-17','8000.00',NULL,20),
(1002,'卢俊义',3,1006,'2001-02-20','16000.00','3000.00',30),
(1003,'林冲',3,1006,'2001-02-22','12500.00','5000.00',30),
(1004,'唐僧',2,1009,'2001-04-02','29750.00',NULL,20),
(1005,'李逵',4,1006,'2001-09-28','12500.00','14000.00',30),
(1006,'宋江',2,1009,'2001-05-01','28500.00',NULL,30),
(1007,'刘备',2,1009,'2001-09-01','24500.00',NULL,10),
(1008,'猪八戒',4,1004,'2007-04-19','30000.00',NULL,20),
(1009,'罗贯中',1,NULL,'2001-11-17','50000.00',NULL,10),
(1010,'吴用',3,1006,'2001-09-08','15000.00','0.00',30),
(1011,'沙僧',4,1004,'2007-05-23','11000.00',NULL,20),
(1012,'李逵',4,1006,'2001-12-03','9500.00',NULL,30),
(1013,'小白龙',4,1004,'2001-12-03','30000.00',NULL,20),


-- 工资等级表
CREATE TABLE salarygrade (
  grade INT PRIMARY KEY,   -- 级别
  losalary INT,  -- 最低工资
  hisalary INT -- 最高工资
);

-- 添加5个工资等级
INSERT INTO salarygrade(grade,losalary,hisalary) VALUES 
(1,7000,12000),
(2,12010,14000),
(3,14010,20000),
(4,20010,30000),
(5,30010,99990);
```



```mysql
-- 需求：

-- 1.查询所有员工信息。查询员工编号，员工姓名，工资，职务名称，职务描述
/*
	分析：
		1.员工编号，员工姓名，工资，需要查询emp表  职务名称，职务描述 需要查询job表
		2.查询条件 emp.job_id = job.id
*/
SELECT 
	t1.`id`, -- 员工编号
	t1.`ename`, -- 员工姓名
	t1.`salary`,-- 工资
	t2.`jname`, -- 职务名称
	t2.`description` -- 职务描述
FROM 
	emp t1, job t2
WHERE 
	t1.`job_id` = t2.`id



-- 2.查询员工编号，员工姓名，工资，职务名称，职务描述，部门名称，部门位置
/*
	分析：
		1. 员工编号，员工姓名，工资 emp  职务名称，职务描述 job  部门名称，部门位置 dept
		2. 条件： emp.job_id = job.id and emp.dept_id = dept.id
*/

SELECT 
	t1.`id`, -- 员工编号
	t1.`ename`, -- 员工姓名
	t1.`salary`,-- 工资
	t2.`jname`, -- 职务名称
	t2.`description`, -- 职务描述
	t3.`dname`, -- 部门名称
	t3.`loc` -- 部门位置
FROM 
	emp t1, job t2,dept t3
WHERE 
	t1.`job_id` = t2.`id` AND t1.`dept_id` = t3.`id`;
 
 
 
-- 3.查询员工姓名，工资，工资等级
/*
	分析：
		1.员工姓名，工资 emp  工资等级 salarygrade
		2.条件 emp.salary >= salarygrade.losalary and emp.salary <= salarygrade.hisalary
			emp.salary BETWEEN salarygrade.losalary and salarygrade.hisalary
*/
SELECT 
	t1.ename ,
	t1.`salary`,
	t2.*
FROM 
	emp t1, salarygrade t2
WHERE 
	t1.`salary` BETWEEN t2.`losalary` AND t2.`hisalary`;



-- 4.查询员工姓名，工资，职务名称，职务描述，部门名称，部门位置，工资等级
/*
	分析：
		1. 员工姓名，工资 emp ， 职务名称，职务描述 job 部门名称，部门位置，dept  工资等级 salarygrade
		2. 条件： emp.job_id = job.id and emp.dept_id = dept.id and emp.salary BETWEEN salarygrade.losalary and salarygrade.hisalary
			
*/
SELECT 
	t1.`ename`,
	t1.`salary`,
	t2.`jname`,
	t2.`description`,
	t3.`dname`,
	t3.`loc`,
	t4.`grade`
FROM 
	emp t1,job t2,dept t3,salarygrade t4
WHERE 
	t1.`job_id` = t2.`id` 
	AND t1.`dept_id` = t3.`id`
	AND t1.`salary` BETWEEN t4.`losalary` AND t4.`hisalary`;
	
	
	
-- 5.查询出部门编号、部门名称、部门位置、部门人数
/*
	分析：
		1.部门编号、部门名称、部门位置 dept 表。 部门人数 emp表
		2.使用分组查询。按照emp.dept_id完成分组，查询count(id)
		3.使用子查询将第2步的查询结果和dept表进行关联查询
		
*/

-- 子查询写法
SELECT 
	t1.`id`,t1.`dname`,t1.`loc` , t2.total
FROM 
	dept t1,
	(SELECT
		dept_id,COUNT(id) total		-- total 取个别名，方便上面查询字段
	FROM 
		emp
	GROUP BY dept_id) t2  -- 将查询出来的dept_id(多行多列)作为一张虚拟表t2
WHERE 
	t1.`id` = t2.dept_id;

-- 内连接的写法
SELECT
	t1.id, t1.dname, t1.loc, count(t2.dept_id)
FROM
	dept t1, emp t2
WHERE
	t1.id = t2.dept_id
GROUP BY
	t2.dept_id


-- 6.查询所有员工的姓名及其直接上级的姓名,没有领导的员工也需要查询
/*
	分析：
		1.姓名 emp， 直接上级的姓名 emp
			* emp表的id 和 mgr 是自关联
		2.条件 emp.id = emp.mgr
		3.查询左表的所有数据，和 交集数据
			* 使用左外连接查询
	
*/

/*
select
	t1.ename,
	t1.mgr,
	t2.`id`,
	t2.ename
from emp t1, emp t2
where t1.mgr = t2.`id`;

*/

SELECT 
	t1.ename,
	t1.mgr,
	t2.`id`,
	t2.`ename`
FROM 
	emp t1
LEFT JOIN 
	emp t2
ON 
	t1.`mgr` = t2.`id`;
```



### 事务的提交方式

```mysql
1. 概念：
	* 如果一个包含多个步骤的业务操作，被事务管理，那么这些操作要么同时成功，要么同时失败。

2. 操作：
		1. 开启事务: start transaction;
		2. 回滚: rollback;
		3. 提交: commit;
		
3. MySQL数据库中事务默认自动提交
		
		* 事务提交的两种方式：
			* 自动提交：
				* mysql就是自动提交的				-- 记住！！！
				* 一条DML(增删改)语句会自动提交一次事务。
			* 手动提交：
				* Oracle 数据库默认是手动提交事务		-- 剧透！！！
				* 需要先开启事务，再提交
		* 修改事务的默认提交方式：(了解)
			* 查看事务的默认提交方式：SELECT @@autocommit; -- 1 代表自动提交  0 代表手动提交
			* 修改默认提交方式： set @@autocommit = 0;
```





### DCL: 数据库权限相关(了解)

```mysql
* SQL分类：
	1. DDL：操作数据库和表
	2. DML：增删改表中数据
	3. DQL：查询表中数据
	4. DCL：管理用户，授权

* DBA：数据库管理员

* DCL：管理用户，授权
	1. 管理用户
		1. 添加用户：
			* 语法：CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
		2. 删除用户：
			* 语法：DROP USER '用户名'@'主机名';
		3. 修改用户密码：
			
			UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';
			UPDATE USER SET PASSWORD = PASSWORD('abc') WHERE USER = 'lisi';
			
			SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');
			SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123');

			* mysql中忘记了root用户的密码？
				1. cmd -- > net stop mysql 停止mysql服务
					* 需要管理员运行该cmd

				2. 使用无验证方式启动mysql服务： mysqld --skip-grant-tables
				3. 打开新的cmd窗口,直接输入mysql命令，敲回车。就可以登录成功
				4. use mysql;
				5. update user set password = password('你的新密码') where user = 'root';
				6. 关闭两个窗口
				7. 打开任务管理器，手动结束mysqld.exe 的进程
				8. 启动mysql服务
				9. 使用新密码登录。
		4. 查询用户：
			-- 1. 切换到mysql数据库
			USE myql;
			-- 2. 查询user表
			SELECT * FROM USER;
			
			* 通配符： % 表示可以在任意主机使用用户登录数据库

	2. 权限管理：
		1. 查询权限：
			-- 查询权限
			SHOW GRANTS FOR '用户名'@'主机名';
			SHOW GRANTS FOR 'lisi'@'%';

		2. 授予权限：
			-- 授予权限
			grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
			-- 给张三用户授予所有权限，在任意数据库任意表上
			
			GRANT ALL ON *.* TO 'zhangsan'@'localhost';
		3. 撤销权限：
			-- 撤销权限：
			revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
			REVOKE UPDATE ON db3.`account` FROM 'lisi'@'%';
```





















































































































































































































