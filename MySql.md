## MySql







### 事务（transaction）

##### 1. 事务的四大特性(ACID)

```java
* 事务的四大特性(ACID)
	1. 原子性（Atomicity）：一个事务是一个不可分割的工作单位，“要么不做，要么全做”。 
	2. 一致性（Consistency）：事务操作前后，数据总量不变（例子：转账总额）。 
	3. 隔离性（Isolation） ：多个事务之间，相互独立。
	4. 持久性（Durability） ：当事务提交或回滚后，数据库会持久化的保存数据。 
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
1. Read Uncommited【读未提交】
	* 引发问题：脏读
	* 指的是：一个事务可以读取到另一个事务还未提交的数据。这就会引发 “脏读”。读取到的是数据库内存中的数据，而并非真正磁盘上的数据。

2. Read Commited 【读已提交】
	* 解决：脏读。	引发问题：不可重读
	* 与前面的读未提交刚好相反，这个隔离级别是，只能读取到其他事务已经提交的数据，那些没有提交的数据是读不出来的。但是这会造成一个问题是： 前后读取到的结果不一样。 发生了不可重复读!!!, 所谓的不可重复读，就是不能执行多次读取，否则出现结果不一 。

3. Repeatable Read 【重复读】	--MySql 默认的隔离级别
	* 解决：脏读，不可重读。	未解决：幻读
	* 该隔离级别， 可以让事务在自己的会话中重复读取数据，并且不会出现结果不一样的状况，即使其他事务已经提交了，也依然还是显示以前的数据。

4. Serializable 【可串行化】 
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





























































































































































































































