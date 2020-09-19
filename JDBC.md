### JDBC

##### 1. 概念

```java
概念：Java DataBase Connectivity  Java 数据库连接， Java语言操作数据库
    
一般步骤：(使用prepareStatement)
    1. 加载驱动
    2. 获取连接conn
    3. 编写Sql语句
    4. 获取sql执行对象prepareStatement, 并传入sql
    5. 设置参数
    6. 执行sql
    7. 处理结果
    8. 释放资源
```





##### 2. 大概的一个例子(简便写)

```java
public static User login(String username, String password) throws Exception {
        // 1. 加载驱动
        Class.forName("com.mysql.jdbc.Driver");

        // 2. 通过DriverManager获取数据库连接
        Connection conn = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/db1", "root", "root");
        // 3. 编写SQL语句
        String sql = "select username, password from user where username = ? and password = ?";
        // 4. 通过conn创建SQL执行对象prepareStatement, 并传入SQL预编译
        PreparedStatement ps = conn.prepareStatement(sql);

        // 5. 为prepareStatement设置参数
        ps.setString(1, username);      // 占位符编号，传入的参数
        ps.setString(2, password);

        // 6. 使用prepareStatement执行SQL语句，得到一个结果集ResultSet
        ResultSet rs = ps.executeQuery();

        // 7. 创建一个JavaBean对象，将结果集ResultSet中的数据封装进对象，[装载到集合]
        User user = new User();
        if(rs.next() == true) {     // 必须要使用rs.next()移动游标才能够获取第一行的数据
            System.out.println("登录成功！！");
            user.setUsername(rs.getString("username"));
            user.setPassword(rs.getString("password"));
        } else {
            System.out.println("登录失败！！！");
        }
    
        // 8. 释放资源....一般要try..catch..finally 不用管了~~~
        if(rs != null)
            rs.close();
        if(ps != null)
            ps.close();
        if(conn != null)
            conn.close();
    
        // 返回一个JavaBean对象
        return user;
    }
```





##### 3. 一些坑点

```java
雷区：
    1. 加载驱动
    	* 在MySQL 5.x以上就可以不用写这个加载驱动的代码了，会自动帮我们加载驱动
    
    2. prepareStatement中传参编号是从1开始的, resultSet中取参也是从编号1开始的
    
    3. ResultSet rs = ps.executeQuery();	// 执行完这句时，cursor指向的是列名行(即: 表头！！)
	   rs.next();	// 调用next()方法才能使游标移动到下一行(也就是数据行)
					// boolean next(); // true表示取到当行数据，false表示已经到尽头了，没有数据了
	
	4. 关于数据库连接：当使用的是本地数据库时，localhost:3306可以用一条'/'来表示
        
    5. int executeUpdate();	 // 一般用于执行DML语句(insert, delete, update)
							// 返回受影响行数。

	6. ResultSet executeQuery();	// 一般用于执行DQL语句(select)
									// 返回一个结果集。需要用boolean next();方法调用获取数据.

	7. 当JavaBean中有Date日期数据类型时，使用的util包下的Date.  虽然数据库返回的时SQL包下的Date类型，但是SQL是util的子类，所以可以接收，没有问题！！！
```





##### 4. 面试题

```java
面试题：statement和prepareStatement的区别？
答：
    1. statement用于执行静态SQL语句，在执行之前必须准备一个正确的SQL语句，当我们需要给SQL语句传入参数时需要进行字符串的拼接，这样做会有SQL注入的风险！！！
    2. 而prepareStatement是预编译的SQL语句对象，SQL语句被预编译并保存在对象中，其中用'？'作为占位符，可以动态设置参数值(编号从1开始)，这样就不会有SQL注入的风险。
    3. 另外，使用PrepareStatement对象执行SQL时，SQL被数据库进行解析和编译，然后被放到命令缓冲区，每当执行同一个PrepareStatement对象时，它就会被解析一次，但不会被再次编译。在缓冲区可以发现预编译的命令，并且可以重用，这样就可以减少编译次数，提高数据库性能。
    4. 所以在使用jdbc开发中，一般都使用prepareStatement.
```

















