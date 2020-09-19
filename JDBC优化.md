### JDBC优化



#### 数据库连接池

```java
1. 概念：数据库连接池其实就是一个容器(集合)，存放数据库连接的容器。系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。避免了频繁创建/销毁连接，节约了资源，提高系统效率。
    
2. 实现：
    1. 标准接口：DataSource   javax.sql包下的
		* 方法：
			* 获取连接：getConnection()
			* 归还连接：Connection.close() 如果是从池子中获取，不是关闭连接的意思，而是归还连接
    
    2. 一般我们不去实现它，有数据库厂商来实现
		* C3P0：数据库连接池技术
		* Druid：数据库连接池实现技术，由阿里巴巴提供的
```



##### C3P0数据库连接池

```java
C3P0：数据库连接池技术
	* 步骤：
		1. 导入jar包 (两个) c3p0-0.9.5.2.jar 和 mchange-commons-java-0.2.12.jar ，
			* 不要忘记导入数据库驱动jar包：mysql-connector-java-5.1.37-bin.jar
		2. 定义配置文件：
			* 名称： c3p0.properties 或者 c3p0-config.xml
			* 路径：直接将文件放在src目录下即可。

		3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
		4. 获取连接： getConnection
	
    * 代码：
		 // 创建数据库连接池对象
        DataSource ds  = new ComboPooledDataSource();
        // 获取连接对象
        Connection conn = ds.getConnection();
```



##### Druid数据库连接池(重点)

```java
Druid：数据库连接池实现技术，由阿里巴巴提供的
	* 具体步骤：
		1. 导入jar包 druid-1.0.9.jar
    		* 不要忘记导入数据库驱动jar包：mysql-connector-java-5.1.37-bin.jar
		
    	2. 定义配置文件：
			* 是properties形式的
			* 可以叫任意名称，可以放在任意目录下
		
    	3. 加载配置文件。Properties
    
		4. 获取数据库连接池对象：通过工厂来来获取  DruidDataSourceFactory
    
		5. 获取连接：getConnection
    
	* 代码：
		 // 3.加载配置文件
        Properties pro = new Properties();
        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        // 4.获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        // 5.获取连接
        Connection conn = ds.getConnection();
```













#### jdbcTemplate

```java
* Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
* 具体实现：
    1. 导入jar包
    
	2. 创建JdbcTemplate对象。依赖于数据源DataSource
		* JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);

	3. 调用JdbcTemplate的方法来完成CRUD的操作（重点掌握下面5个方法即可！）
        update()
        queryForMap()
        queryForList()
        query()
        queryForObject()
      
     4. 具体讲解：
        * 执行DML语句。(insert, delete, update)
        	* int update()	
        
        * 执行DQL语句。(select)
        	1. Map<String, Object> queryForMap 查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合. 查询出来的结果只能是1条. (0条或2条都会报错！！！)
        	
        	2. List<Map<String, Object>> queryForList 查询出来的结果集 将每一条记录封装为一个Map集合，再将Map集合装载到List集合中然后返回
        	
        	3. List<T> query(sql, RowMapper) 查询出来的结果集 将每一条记录封装为一个自定义的JavaBean对象，然后装载到List集合当中返回
        		注意：query的参数：RowMapper
					* 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装
					* new BeanPropertyRowMapper<类型>(类型.class)
        				* 举例：new BeanPropertyRowMapper<User>(User.class)
        	
        	4. T queryForObject(sql, Class<T> requiredType)  queryForObject其实支持的是标量子查询,只能传入一个基本类型的包装类的class,并返回一个基本类型对应包装类型的对象.
        		* 一般用于聚合函数的查询
				* 举个栗子: queryForObject(sql, Integer.class)
```







#### 具体例子

##### 1. 创建JdbcTemplate

```java
// 使用druid数据库连接池创建dataSource, 然后当作参数创建JdbcTemplate
@Test
public void testJdbcTemplate() throws Exception {
    Properties properties = new Properties();
    InputStream is = Demo3.class.getClassLoader().getResourceAsStream("druid.properties");
    properties.load(is);
    DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
    JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
    String sql = "select * from user";
    List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
    for (int i = 0; i < list.size(); i++) {
        System.out.println(list.get(i));
    }
}
```



###### 1. 2 抽取JdbcTemplate出来方便下面操作

```java
public JdbcTemplate getJdbcTemplate(){
        Properties properties = new Properties();
        InputStream is = Demo01.class.getClassLoader().getResourceAsStream("druid.properties");
        try {
            properties.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }

        DataSource dataSource = null;
        try {
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        return jdbcTemplate;
    }
```





##### 2. DML操作

```java
@Test
public void testInsert() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "insert into user values(?, ?)";
    int row = jdbcTemplate.update(sql, "lafyq", "helloworld");
    Assert.assertEquals(1, row);
}
```

```java
@Test
public void testDelete() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "delete from user where username = ?";
    int row = jdbcTemplate.update(sql, "lafyq");
    Assert.assertEquals(1, row);
}
```

```java
@Test
public void testUpdate() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "update user set password = ? where username = ?";
    int row = jdbcTemplate.update(sql, "admin", "admin");
    Assert.assertEquals(1, row);
}
```





##### 3. DQL操作

###### 3.1 queryForMap

```java
// queryForMap 返回值只能是一条，多于一条或者少于一条都会报错
@Test
public void testQueryForMap() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "select * from user where username = ?";
    Map<String, Object> map = jdbcTemplate.queryForMap(sql, "admin");
    System.out.println(map);
}
```



###### 3.2 queryForList

```java
// 返回值是一个封装了上面的那个Map的List集合，如果需要返回List<JavaBean>的话就用下面的query()方法
@Test
public void testQueryForList() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "select * from user";
    List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
    System.out.println(list);
}
```





###### 3.3 query

```java
// 使用query()方法将结果集封装成我们想要的List<JavaBean>然后返回
@Test
public void testQuery() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "select * from user";
    // 这里JdbcTemplate会帮我们自动映射封装为JavaBean对象然后装载到List集合当中
    List<User> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(User.class));
    for (User user : list)
        System.out.println(user);
}
```



其底层代码如下：

```java
// 使用query()方法将结果集封装成我们想要的List<JavaBean>然后返回
@Test
public void testQuery2() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "select * from user";
    List<User> list = jdbcTemplate.query(sql, new RowMapper<User>() {
        @Override
        public User mapRow(ResultSet rs, int i) throws SQLException {
            User user = new User();
            String username = rs.getString("username");
            String password = rs.getString("password");
            user.setUsername(username);
            user.setPassword(password);
            return user;
        }
    });

    for (int i = 0; i < list.size(); i++) {
        System.out.println(list.get(i));
    }
}
```



###### 3. 4 queryForObject

```java
// queryForObject其实支持的是标量子查询,只能传入一个基本类型的包装类的class,并返回一个基本类型对应包装类型的对象. 注意：第二个参数只能是基本类型的包装类型的class
@Test
public void testQueryForObject() {
    JdbcTemplate jdbcTemplate = getJdbcTemplate();
    String sql = "select count(*) from user";
    Integer row = jdbcTemplate.queryForObject(sql, Integer.class);
    System.out.println(row);
}
```









