# JavaEE

## 1.入门（课程的概况）

![20190203](.\img\20190203.jpg)

## 2.在eclipse中将web项目部署到Tomcat服务器上

1. 流程：

   1）安装Tomcat服务并进行配置，

   2）eclipse环境下配置tomcat

   3）建立一个web应用启动服务

   4）让Tomcat服务器显示在控制台上，将Web应用部署到Tomcat中

2. 再启动的服务时候可能会出现端口被占用的情况，在命令行中输入netstat -ano查看被占用端口的PID值，然后在任务管理器中结束该进程。

3. 项目提交到服务器之后的默认保存目录（部署目录）（eclipse的工作目录）：G:\JavaCode\.metadata\.plugins\org.eclipse.wst.server.core\tmp1\wtpwebapps
4. 博客地址：https://www.cnblogs.com/zcxhaha/p/10354648.html
5. 常用的服务器：Tomcat（不支持EJB），JBOSS（不支持JSP），WebSphere，weblogic等

## 3.获取当前服务器端和客户端的时间并显示

1. jsp = Java Server Pages
2. Jsp是一种动态的网页技术（还有ASP，PHP等），相对的有HTML（静态网页技术）
3. 动态网页是指：动态生成网页数据，而不是网页中的动态特效
4. JSP是在HTML中嵌入java的脚本代码
5. JSP是由应用服务器来编译和执行嵌入的java脚本代码，然后将生成的整个页面返回客户端。
6. jsp获取的当前时间为服务器端的时间，JavaScript和jQuery获取的是客户端的时间。 
7. 代码示例（服务器端的时间和客户端的时间间隔3秒）：

~~~jsp
<%@page import="java.util.Date"%>
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
	<!-- 使用js获取客户端的时间 -->
	<script type="text/javascript">
		window.onload = function() {
			var now = new Date();
			
			alert("客户端的时间是：" + now.toLocaleString());
		}
	</script>

</head>

<body>
	<%--使用jsp获取服务器端的时间 --%>
	<%
		//小脚本scriptlet,在这里可以写jsp代码
		
		Date now = new Date();
		//写到控制台
		System.out.println("服务器端的时间（控制台）：" + now.toLocaleString());
		
		//写到网页，out是jsp的内建对象，不需要new，可以直接使用
		out.println("服务器端的时间是（网页）：" + now.toLocaleString() + "</br>");
		
		Thread.sleep(3000);
	%>
		
	<%--在这里边只可以写表达式，不可以写语句（即表达式后边不加分号） --%>
	服务器端的时间是（网页）:<%= now.toLocaleString() %>
	
</body>
</html>
~~~

3. 执行的过程：

![201902071](F:\JavaEE\Note\img\201902071.jpg)

4. **jsp表面上是一个网页（HTML文件），实际上是一个java类：**

   jsp程序的执行过程（在服务器端）：

   * 翻译：.jsp——>.java(Servlet)
   * 编译：.java——>.class
   * 执行：.class——>

## 4.获取某一个页面的访问次数

1. 代码：

~~~jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Insert title here</title>
</head>
<body>
	<%--声明 --%>
	<%!
		int count1 = 0;
	%>
	
	<%--小脚本 --%>
	<%
		int count2 = 0;
		count1++;
		count2++;
	%>
	
	<%--表达式 --%>
	<p>该页面的访问次数是（声明）：<%= count1%></p>
	<p>该页面的访问次数是（小脚本）：<%= count2%></p>
	
	<%--声明的可以实现，小脚本的无法实现 --%>
</body>
</html>
~~~

2. **上述结果的原因：**

* 声明：
  * 声明中定义的变量会变为Servlet类的成员变量
  * 声明中定义的方法会变为Servlet类的成员方法
* 小脚本：
  * 小脚本中定义的变量会变为Servlet类中Service()方法的局部变量
* Servlet是一种单实例多线程的技术：
  * 即不管有多少个用户来访问，不管访问多少次，一个Servlet类只会实例化一次，所以成员变量是共享的。
  * 每次访问都会产生一个新的线程，新的线程会调用Service（）方法，该方法中的变量为局部变量，每一个用户都会有一份。
* 在声明中不可以使用内建对象，在小脚本和表达式中可以使用内建对象
* jsp中的三种语法格式：声明，小脚本，表达式
* jsp中的注释：
  * HTML的注释：只能注释HTML标签，而JSP注释可以注释JSP的语法内容
  * Java的注释
  * JSP的注释：不会传送到客户端，节省网络的流量，并且安全性高

## 5.包含头文件和底部文件（静态包含和动态包含）

1. 文件的包含，实现了代码的复用，相同的部分可以直接引用（而不是手工的复制）

2. 静态包含和动态包含的区别：

   * 语句不同：

     <%@include file="header.jsp" %>                       **静态包含**
     <jsp:include page="footer.jsp"> \</jsp:include>	                 **动态包含**

   * 包含的时机不同：

     静态包含：翻译的时候       jsp——>java

     动态包含：运行的时候       class。。。

   * 被包含的文件（JSP）是否需要进行翻译和编译：

     静态包含：不需要，**引用的.jsp文件**在翻译的时候直接将代码copy至**目标的jsp文件**，引用的jsp文件不需要翻译成.java文件

     动态包含：需要，**引用的.jsp文件**直接翻译成.java文件，然后编译成.class文件，然后运行的时候引用.class文件

   * 是否允许存在同名变量：

     静态包含：不允许，代码的直接拷贝，同名变量会发生冲突

     动态包含：允许，两个.jsp文件独自进行翻译和编译，翻译完成之后运行的时候进行引用

代码示例：

**index2.jsp：**

~~~jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Insert title here</title>
</head>
<body>
	<%--指令元素 指令包含
	三种指令标签：
		<%@include ...%>
		<%@page ... %>
		<%@taglib ... %>
	 --%>
	<%@include file="header.jsp" %>
	
	<div style="width:100%; height:500px">haha</div>
	
	<%--动作元素 动态包含 --%>
	<jsp:include page="footer.jsp"></jsp:include>
</body>
</html>
~~~

**header.jsp:**

~~~jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<link rel="stylesheet" type="text/css" href="./css/homepage.css">
<title>Insert title here</title>
</head>
<body>
    ...
</body>
</html>
~~~

**footer.jsp:**

~~~jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<link rel="stylesheet" type="text/css" href="./css/homepage.css">
<title>Insert title here</title>
</head>
<body>
    ...
</body>
</html>
~~~

3.总结（JSP的页面构成）：

* 静态内容：
* 动态内容：
  * JSp标签：指令标签，动作标签
  * Java脚本：小脚本，表达式，声明
* 注释：
  * HTML注释
  * JSP注释
  * Java注释

## 6.手动部署项目

1. 配置环境变量：
   * JAVA_HOME：jdk的安装路径
   * CATALINA_HOME：Tomcat的安装目录
2. 启动和关闭Tomcat，在命令行执行Tomcat/bin下的startup.bat和shutdown.bat两个文件（注：.bat是dos下的批处理文件）
3. 手动部署项目到服务器
   * 在Tomcat中的webapps下创建一个文件夹，将webcontent文件夹下的内容均复制到该文件夹（繁琐，程序每次修改都得重新复制一遍）
   * 打压成war包：发送到服务器端，也放在webapps文件夹下，每次使用的时候自动解压（项目完成之后可以使用）
   * 修改server.xml文件的内容（加入以下代码，在项目的开发中使用较方便）：

~~~xml
<Context path="/zcx" docBase="G:\JavaCode\MyJsp1\WebContent" reloadable="true"></Context>
~~~

注：path：自定义一个文件夹的名称，记住必须加 **/**

​	docBase：项目的开发路径

​	reloadable：项目修改之后是否进行重新加载

## 7.实现登录功能

1. 实例：

login.html

~~~html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>login</title>
</head>
<body>
    <!--action 指明接受数据的页面-->
	<form action="doLogin.jsp" method="post">
	<!-- name用于在jsp文件中获取相应标签的内容 -->
		<h1>用户名：</h1> <input type="text" name="username" id="username"> <br>
		<h1>密码：</h1> <input type="password" name="pwd" id="pwd"> <br>
		<input type="submit">
	</form>

</body>
</html>
~~~

doLogin.jsp

~~~jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Insert title here</title>
</head>
<body>
	 <%
	 	//通过相应标签的name属性获取标签的内容
	 	String userName = request.getParameter("username");
	 	String passward = request.getParameter("pwd");
	 	
	 	if(userName.equals("zhangchenxuhaha") && passward.equals("123456")){
	 		out.println("yes");
	 	} else {
	 		out.println("no");
	 	}
	 %>
</body>
</html>
~~~

## 8.HTTP协议（可以通过浏览器提供的开发者工具进行观察）

1. HTTP协议：超文本传输协议

2. 工作原理：

   * 遵循请求（request）/应答（response）模型
   * 无法实现在客户端未发起请求的情况下服务器将消息推送给客户端
   * 请求之前需要建立连接，响应之后会断开连接，连接不会存在
   * 一个页面如果有多个图片，js，css等，就会发送多个请求和响应
   * HTTP/1.0版本，每次请求都会建立新的TCP连接，连接不能复用。HTTP/1.1版本新的请求可以在上次请求建立的TCP连接之上发送，连接可以复用。
   * HTTP/2.0版本，具有的新的功能，二进制协议和服务器推送的功能。

3. HTTP请求：

   **请求的方式的格式为：**

   * 请求行：统一资源定位符（URL），协议版本号

     ==GET /zcx/img/admin.png HTTP/1.1==

     GET：请求方式

     /zcx/img/admin.png：路径

     HTTP/1.1：协议版本

   * 消息报头：请求修饰符，客户机的信息

   * 可能得内容：POST请求的内容

   **请求方法：**

   - Get
   - Post

4. HTTP响应：

   **响应信息格式：**

   - 状态行：包括信息的协议版本号，一个成功的或错误的代码

     ==HTTP/1.1 200==

     HTTP/1.1：协议的版本号

     200：状态码

   - 消息报头：包括服务器的信息，字符编码等

   - 响应正文：

   **状态代码由三位数字组成，第一个数字定义响应类别，有五种取值：**

   * 1xx：指示信息，表示请求已接收，继续处理
   * 2xx：成功，表示请求已被成功接收，理解，接受
   * 3xx：重定向，表示完成请求要进行更进一步的操作
   * 4xx：客户端错误，请求有语法错误或者请求无法实现
   * 5xx：服务器端错误，服务器未能实现合法的请求

   **常见的状态码，状态描述，说明：**

   * 200        OK，表示客户端请求成功
   * 302           REDIRECT，重定向
   * 403            Forbidden，表示服务器接收到请求，但是拒绝提供服务
   * 404            Not Found，请求的资源不存在，可能是输入了错误的URL
   * 500            Internal Server Error，服务器发生了不可预期的错误

5. 服务器端对于请求的处理：

   - 对于每次请求服务器端都会生成一个request对象，该对象包含了请求的内容，（表单数据，客户端ip等），服务器端的jsp可以从request对象中获取数据

   * 同时服务器端还会生成response对象，用来存储响应给客户端的信息，服务器端的jsp可以向response对象中添加数据














