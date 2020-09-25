### Tomcat & Servlet



### 写在前面

### 实际开发当中

```java
1. 直接使用注解开发: @WebServlet

2. 继承 HttpServlet抽象类，复写里边的doGet/doPost方法

3. 上面这两部就足够完成一个servlet了...不推荐使用xml文档的开发方式!!!!!!
```















#### Tomcat部署的三种方式

```java
* 部署项目的方式：
    1. 将项目打成一个war包扔到webapps目录下即可(ps.直接把文件夹扔过去也行！！！)   // 常用！！
    	* war包会自动解压缩, 删除后也会自动删除解压出来的文件.

    2. 在conf\Catalina\localhost创建任意名称的xml文件.		// 推荐！！！
    	* 比如说我创建aaa.xml
    	* 文件中写: <Context docBase="D:\hello" />
    	* 那么我就可以访问: localhost:8080/aaa/test.html （也就是说xml文件的名字就是虚拟路径）
    
            
    3. 修改conf/server.xml文件 		// 了解即可，不建议使用！！！
    在<Host>标签体中配置
    <Context docBase="D:\hello" path="/hehe" />
    * docBase:项目存放的路径
        * path：虚拟目录
```







#### Servlet

###### 什么是Servlet

```java
1. 概念：Servlet = Server + applet (两个单词各取一部分)
   Servlet其实就是运行在web服务器的一个Java程序, 用于接收和响应客户端的http请求.

2. 从代码层面来讲：
    	* Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。
    	* 我们就需要定义一个类去实现Servlet接口, 复写里边的方法
```



###### 举个例子(伪代码)

```java
// 伪代码
public class ServletDemo implements Servlet {
    @Override
    public void init() {				// 1. 初始化方法
    }

    @Override
    public ServletConfig getServletConfig() {		// 获取Servlet配置的方法(了解)
        return null;
    }

    @Override
    public void service() {				// 2. 提供服务的方法
    }

    @Override
    public String getServletInfo() {		// 获取Servlet一些信息的方法(了解)
        return null;
    }

    @Override
    public void destroy() {				// 3. 销毁的方法
    }
}
```

```xml
<servlet>
    <servlet-name>demo</servlet-name>
    <servlet-class>day0920.ServletDemo</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/testServlet</url-pattern>
</servlet-mapping>
```

```java
然后就访问: http://localhost:8080/虚拟路径/testServlet
```



###### 执行原理

```java
* 简述：
url --> web.xml --> servlet-mapping --> url-pattern --> servlet-name --> servlet --> servlet-name --> servlet-class --> Class.forName() --> cls.newInstance() --> 调用service()

* 文字描述过程：
    通过url访问到web.xml，然年找到servlet-mapping中的url-pattern，再根据它上面的servlet-name对应到servlet中的servlet-class，然后通过反射创建对象执行里边的service()方法。
```



###### 生命周期

```java
1. 初始化阶段：在创建servlet的实例时，执行init()方法。（一个servlet只会初始化一次，init方法只会执行一次，默认情况下是: 初次访问该servlet，才会创建实例。（单实例对象））

2. 服务阶段：根据Http请求方式，调用service()方法中对应的doGet()或doPost()方法，并将响应结果返回；

3. 销毁阶段：关闭服务器或servlet容器重载servlet时，调用destroy()方法释放servlet所占用的资源
```



###### Servlet实例创建的时机

```xml
* servlet实例默认是在初次被访问的时候才会被创建的.
* 但是可以让它创建的时机提前: 在web.xml配置的时候, 使用load-on-startup元素来指定, 给定的数字越小，启动的时机就越早。一般不写负数，从2开始即可。

<servlet>
    <servlet-name>HelloServlet04</servlet-name>
    <servlet-class>com.itheima.servlet.HelloServlet04</servlet-class>
    <load-on-startup>2</load-on-startup>
</servlet>

<!-- 3.0之后的版本默认没有web.xml文件，需要我们在servlet类上加注解 -->
<!-- @WebServlet(urlPatterns = "/testServlet",loadOnStartup = 2)   -->
```





#### 使用@WebServlet注解

```java
@WebServlet(urlPatterns = "/hello")
public class Demo implements Servlet {
    ...
}

// 加上注解之后就不用web.xml文件了(servlet 3.0之后的版本默认不创建web.xml文件)
// 如果只是单单需要url-pattern这个路径的话，可以简写成: @WebServlet("/hello")
```







#### Servlet的体系结构

```java
Servlet的体系结构	
	Servlet -- 接口
		|
	GenericServlet -- 抽象类
		|
	HttpServlet  -- 抽象类

	* GenericServlet：将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象
		* 将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可

	* HttpServlet：对http协议的一种封装，简化操作		// 我们常用
		1. 定义类继承HttpServlet
		2. 复写doGet/doPost方法
```



#### Servlet访问路径

```java
Servlet访问路径
	1. urlpartten:Servlet访问路径
		1. 一个Servlet可以定义多个访问路径 ： 
        		@WebServlet({"/d4","/dd4","/ddd4"})
		2. 路径定义规则：
			1. /xxx：路径匹配
			2. /xxx/xxx:多层路径，目录结构
			3. *.do：扩展名匹配
```





















