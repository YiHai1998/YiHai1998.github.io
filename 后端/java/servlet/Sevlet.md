

# Servlet

## 概念

Servlet 是 Server 与 Applet 的缩写，是服务端小程序的意思。使用Java语言编写的服务器端程序，可以像生成动态的WEB页， Servlet 主要运行在服务器端，并由服务器调用执行，是一种按照Servlet标准来开发的类。是SUN公司提供的一门用于开发动态Web资源的技术。（言外之意：要实现web开发，需要实现Servlet标准）

`Servlet 本质上也是Java类`，但要遵循Servlet规范进行编写，没有main()方法，它的创建、使用、销毁都由Servlet容器进行管理（如 Tomcat ）。（言外之意：写自己的类，不用写main方法，别人自动调用）

`Servlet是和HTTP协议是紧密联系的`，其可以处理 HTTP 协议相关的所有内容。这也是Servlet应用广泛的原因之一。

提供了Servlet 功能的服务器，叫做Servlet容器，其常见容器有很多，如 Tomcat, Jetty , WebLogic Server , WebSphere, jBoss 等等。

## Servlet的实现

### 实现Servlet规范

实现Servlet规范，即继承`HttpServlet类`，并到如响应的包，该类中已经完成了通信的规则，我们只需要进行业务的实现即可。

### 重写Servlet方法

满足Servlet规范只是让我们的类能够满足接收请求的要求，接收到请求后需要对请求进行分析，以及进行业务逻辑处理，计算出结果，则需要添加代码，在规范中有一个叫做`service`的方法，专门用来做请求处理的操作，业务代码则可以写在该方法中。

### 设置注解

在完成好了一切代码的编写后，还需要向服务器说明，特定请求对应特定资源。
开发servlet项目，T使用@WebServlet将一个继承于javax.servlet.http.HttpServlet 的类定义为Servlet组件。在Servlet3.0中，可以使用`@WebServlet`注解将一个继承于javax.servlet.http.HttpServlet的类标注为可以处理用户请求的Servlet。



```java
/*
	实现Servlet
    1.创建普通Java类
    2.实现Servlet的规范，继承HttpServlet类
    3. 重写service方法，用来处理请求
    4.设置注解，指定访问的路径
*/

@WebServlet(" /ser01" )
public class Servlet01 extends HttpServlet {
	@Override
	protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
	//打印内容在控制台
	System.out.println("Hello Servlet!");
	//通过流输出数据到浏览器
	resp.getWriter().write("Hello Servlet!");
	}
}
```

