# HTML

hyper text markup language，超文本标识语言

B/S开发，结构(HTML)-表现(CSS)-行为(JavaScript)，参考[w3school](https://www.w3school.com.cn/)

## 常用标签

`<p>`

# CSS

Cascading Style Sheetes，层叠样式表。

# JavaScript

脚本语言，弱类型语言。

## 基本语法

### 声明变量

### 声明对象

### 声明方法

## 事件

用户和浏览器交互的行为。

# JQuery

JQuery是JS库

# XML

可扩展标记语言(EXtensible Markup Language)

# Tomcat

## Tomcat目录结构

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211202121869.png)

bin：可执行程序止录。startup.bat 启动；shutdown.bat 关闭
conf：配置文件目录。server.xml 为tomcat集中配置的地方，比如配置端口；web.xml：为tomcat所有web项目都需要遵循的配置。
lib：tomcat运行所需要的jar包
logs：运行日志
temp：临时文件
webapps：集合了tomcat中所有运行的项目，每个项目就是一个文件夹
work：存放运行期间编译的一些东西，比如jsp页面

# Servlet

用来接收请求，处理请求，完成响应的一段小程序。继承HttpServlet，重写doGet()，doPost()方法，后配置web.xml即可。

```java
<servlet>  
    <servlet-name>uploadServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.UploadServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>uploadServlet</servlet-name>  
    <url-pattern>/upload</url-pattern>  
</servlet-mapping>
```

## ServletConfig

作用：一个Servlet对应ServletConfig，封装Servlet的配置信息

## ServletContext

> 四大域对象：PageContext,ServletRequest,HttpSession,ServletContext
> 域对象： 在域中可保存数据，跨页面获取到保存的数据，多个资源之间共享数据的。

作用：
- 域对象
- 获取当前项目信息，一个项目对应一个ServletContext，只能是代表当前项目。

```java
servletContext.getRealPath("/");
```

## HttpServletRequest

代表请求，每次请求的详细信息会被tomcat自动封装成一个request对象，我们以后要获取当前请求的一些信息，我们使用request对象即可。

作用：
- 获取请求参数`req.getParameter("xyz")`；
- 作为域对象保存数据，同一次请求期间可以共享数据；
- 获取HttpSession对象`req.getSession()`
- 请求转发：`req.getRequestDispatcher("/xyz").forward(req,resp);`请求转发还是当前请求。

## HttpServletResponse

>请求数据 叫 请求报文
>响应数据 叫 响应报文
>
>请求报文格式：
>请求首行（Http协议等）
>请求头
>空行
>请求体
>
>响应报文格式：
>响应首行（Http协议等）
>响应头
>空行
>响应体

f12可查看请求的请求头，请求响应。

作用：
- 交给浏览器的数据；
- 重定向：`resp.sendRedirect(req.getContextPath() + "/index.jsp");`注意这里/是以前台的路径为准，重定向是发起新请求。

回归底层：服务器给浏览器输出字节流`resp.getOutputStream()`或字符流`resp.getWriter().write("abcde")`

## 乱码

通常，浏览器的编码为UTF-8，Tomcat服务器默认编码格式为ISO_8859_1 。参考：[[spring对web层的支持#9. 乱码问题]]

### 请求乱码

浏览器发送给服务器的数据，服务器收到解析出来乱码

#### GET请求乱码：

原因：

所有的请求参数是带在URL地址上的：tomcat收到这个请求就会调用默认的编码格式ISO_8859_1 将其解码完成，并封装成reqeust对象。相当于明信片：数据与地址在一起。

解决：

通常可以在代码中加入以下的片断去处理：

```java
// 将文件流如何正确的交给浏览器？  
String username = req.getParameter("username");  
// 将字符串以utf-8解码  
username = new String(username.getBytes(StandardCharsets.ISO_8859_1), StandardCharsets.UTF_8);
```

但最佳处理方式是去改tomcat配置文件：

```java
/**
 修改 tomcat中的 conf/server.xml
 Connector标签中添加URIEncoding="UTF-8"
 utf-8 = Unicode Transformation Format

 注意点：如果有 useBodyEncodingForURI="true"  请删除

 例如：
     <Connector port="8080" protocol="HTTP/1.1"
        connectionTimeout="20000"
        redirectPort="8443"
        disableUploadTimeout="true"
        executor="tomcatThreadPool"
        URIEncoding="UTF-8"/>
 */
```

#### POST请求乱码

原因：

请求带来的数据都在请求体中放着，tomcat关不着急解析请求体，一旦调用`req.getParameter()`将整个请求体使用默认的编码格式全部解析完成。相当于信件：地址与内容分隔开。

解决：

通常，可以在调用获取参数前设置解码字符集

```java
// post请求需要在获取参数前设置解码字符集  
req.setCharacterEncoding(StandardCharsets.UTF_8.name());  
String xyz = req.getParameter("xyz");
```

更进阶的提供个MyCharacterEncodingFilter

```java
public class MyCharacterEncodingFilter implements Filter {  
    @Override  
    public void init(FilterConfig filterConfig) throws ServletException {  
  
    }  
  
    @Override  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {  
	    // 设置请求的编码字符集，解决post请求乱码
        request.setCharacterEncoding("UTF-8");  
        // 响应一般不设置，因为可能是图片格式，这样设置就有问题  
        // response.setContentType("text/html;charset=utf-8");
        chain.doFilter(request, response);  
    }  
  
    @Override  
    public void destroy() {  
  
    }  
}
```

### 响应乱码

服务器发送给浏览器的数据，浏览器收到解析出来乱码

原因：

`resp.getWriter().write("hello world")`直接写出去的数据，浏览器并不知道数据的内容类型及编码格式，浏览器用默认的格式打开（比如utf-8）

解决：

给浏览器的数据一定要说清楚这是什么格式的编码，告诉浏览器事情都放天响应头中。

```java
// 可以简写成这样，当然成可以放在响应头中
resp.setContentType("text/html;charset=utf-8");
```

## 路径

相对路径：
	不以/开始的路径就是相对路径，相对于当前资源所在的路径为标准。推荐以后都不要用相对路径。
	
绝对路径：
	以/开始的路径就是绝对路径

服务器解析的路径：
	`request.getRequestDispatcher("/abc").forward(request,response)`就是当前项目下的路径。
	注意：`resp.sendRedirect("/index.jsp");`是浏览器要访问在的，所以不会加上项目名。

浏览器解析的路径：
	在前面拼上服务器地址（url加端口）是最终要去的路径，所以页面要写以/开始的路径要加上项目名。
	只要是html标签（参考[w3school](https://www.w3school.com.cn/)）里面写的路径都是浏览器解析，除此之外都是服务器解析。
	`<jsp:forwar>`等自定义标签目前都是服务器解析。

# JSP

Java Servlet Page。

## 原理

> 静态资源：除java代码外都是静态资源
> 动态资源：Java代码（Jsp也是）

比如浏览器请求访问`index.jsp`

1 向服务发送请求`http://localhost:8080/MyWebApp/index.jsp`

2  org.apache.jasper.servlet.JspServlet拦截到这个请求

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211210052389.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211210052454.png)

3 JspServlet找到index.jsp将其翻译成index_jsp.java，编译成index_jsp.class。只有第一次请求才编译，后面请求直接找到这个class。

4 利用反射调用class中的方法

5 将jsp文件中的数据写出去。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211210058455.png)

## 九大隐含对象



## 常用标签



# 监听器 Listener

监听器：监听对象（ServletContext），监听事件（ServletContextEvent），触发行为（实现的方法体）

## 按监听对象进行分类

> 生命周期lifecycle：从创建到销毁的过程
> 
> 常用域对象pageContext，requst，session，application

- 监听ServletContext对象

ServletContextListener，作用监听ServletContext生命周期（服务器启动创建，服务器关闭销毁）

ServletContextAttributeListener，作用监听ServletContext域中属性变化

- 监听HttpSession对象

HttpSessionListener，作用监听HttpSession生命周期（session是第一次使用的时候创建，session只有过期销毁，手动销毁）
注意：session在浏览器关闭时并不销毁，在cookie不传时也不会销毁

HttpSessionAttributeListener，作用监听HttpSession域中属性变化

HttpSessionActivationListener，作用监听HttpSession中某个对象活化、钝化的过程

HttpSessionBindingListener，作用监听HttpSession中某个对象保存（绑定）到Session中和从Session中称除（解绑）的过程

- 监听ServletRequest对象

ServletRequestListener，作用监听ServletRequest生命周期（请求进来创建新的Request对象，请求完成销毁Request对象）

ServletRequestAttributeListener，作用监听ServletRequest域中属性变化的过程

## 按功能进行分类

- 监听生命周期

ServletContextListener，HttpSessionAttributeListener，ServletRequestListener

思考：为什么没有PageContext的监听器呢？因为PageContext只有当前页面使用，不涉及跨页面调用

- 监听域中属性变化

ServletContextAttributeListener，HttpSessionAttributeListener，ServletRequestAttributeListener

- session独有的临听器

HttpSessionActivationListener，HttpSessionBindingListener

## 常用方法 

> 三个生命周期的监听器，用于各域创建或销毁时调用

ServletContextListener：

```java
public interface ServletContextListener extends EventListener {  
	// ServletContext初始化（服务器启动）时调用
    public void contextInitialized(ServletContextEvent sce);  
 	// ServletContext销毁（服务器停止）时调用 
    public void contextDestroyed(ServletContextEvent sce);  
}
```

HttpSessionListener：

```java
public interface HttpSessionListener extends EventListener {  
    // session创建时（即第一次使用时）调用  
    public void sessionCreated(HttpSessionEvent se);  
    // session自动失效（超时timeout）,手动设置session失效（session.invalidate()方法)
    public void sessionDestroyed(HttpSessionEvent se);     
}
```

ServletRequestListener：

```java
public interface ServletRequestListener extends EventListener {  
	// request销毁调用（当次请求结束）
	public void requestDestroyed(ServletRequestEvent sre);  
	// reqeust初始化调用（发起一个新请求）
	public void requestInitialized(ServletRequestEvent sre);  
}
```

> 三个各域属性监听器用于监听域中属性变化

ServletContextAttributeListener：

```java
public interface ServletContextAttributeListener extends EventListener {  
	// 属性添加时调用
    public void attributeAdded(ServletContextAttributeEvent event);  
	// 属性移除时调用
    public void attributeRemoved(ServletContextAttributeEvent event);  
	// 属性被替换时调用
    public void attributeReplaced(ServletContextAttributeEvent event);  
}
```

HttpSessionAttributeListener：

```java
public interface HttpSessionAttributeListener extends EventListener {  
  	// 属性添加时调用
    public void attributeAdded(HttpSessionBindingEvent event);  
  	// 属性移除时调用
    public void attributeRemoved(HttpSessionBindingEvent event);  
  	// 属性被替换时调用
    public void attributeReplaced(HttpSessionBindingEvent event);  
  
}
```

ServletRequestAttributeListener：

```java
public interface ServletRequestAttributeListener extends EventListener {  
  	// 属性添加时调用
    public void attributeAdded(ServletRequestAttributeEvent srae);  
  	// 属性移除时调用
    public void attributeRemoved(ServletRequestAttributeEvent srae);  
  	// 属性被替换时调用  
    public void attributeReplaced(ServletRequestAttributeEvent srae);  
}
```

> session特有的活化、钝化、绑定、解绑 监听器

HttpSessionActivationListener：

```java
public interface HttpSessionActivationListener extends EventListener {   
	// 监听某个对象随着HttpSession将要钝化（钝化前调用）
    public void sessionWillPassivate(HttpSessionEvent se);   
    // 监听某个对象随着HttpSession已经活化（活化后调用）
    public void sessionDidActivate(HttpSessionEvent se);  
}
```

HttpSessionBindingListener：

```java
public interface HttpSessionBindingListener extends EventListener {  
	// 监听某个对象在session中绑定进来（保存进来）
    public void valueBound(HttpSessionBindingEvent event);  
	// 监听某个对象在session中解绑（移除）
    public void valueUnbound(HttpSessionBindingEvent event);  
          
}
```

## 如何使用监听器

- 实现相应的监听器接口中的方法
- web.xml中注册

### Demo

关于如何使用Maven创建Web工程并启动，请参考[[maven笔记#7.2. 创建web工程]]或[[spring对web层的支持#3.1. 创建web工程]]

/webapp/web-info/web.xml中配置：

```xml
<listener>  
    <listener-class>com.aitx.study.lisentner.MyServletContextListener</listener-class>  
</listener>
```

代码：

```java
public class MyServletContextListener implements ServletContextListener {  
    @Override  
    public void contextInitialized(ServletContextEvent sce) {  
        ServletContext servletContext = sce.getServletContext();  
        System.out.println("tomcat容器初始化了，容器为：" + servletContext);  
    }  
  
    @Override  
    public void contextDestroyed(ServletContextEvent sce) {  
        System.out.println("tomcat容器销毁了");  
  
    }  
}
```

启动本地tomcat测试监听器：

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211150203143.png)

停止tomcat：

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211150207190.png)

## Session的监听器

### Session的生命周期

创建：第一次使用时创建

销毁：
1 session自动超时失效
2 session手动设置失效

测试Session创建时机：

在webapp目录下创建index.jsp，index.html页面。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160237649.png)

创建HttpSessionListener的实现类，并在web.xml中配置

```java
public class MySessionLifeCycleListener implements HttpSessionListener {  
    @Override  
    public void sessionCreated(HttpSessionEvent se) {  
        System.out.println("session对象创建了..." + se.getSession().getId());  
    }  
  
    @Override  
    public void sessionDestroyed(HttpSessionEvent se) {  
        System.out.println("session销毁了");  
    }  
}
```

```xml
<listener>  
    <listener-class>com.aitx.study.listener.MySessionLifeCycleListener</listener-class>  
</listener>
```

启动服务器后，会自动访问index.html，此时后台日志不会打印session创建时的日志

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160241013.png)

Cookie此时无值

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160319250.png)

访问index.jsp页面后台会打印日志（因类jsp页面会用到session域对象）并且Cookie此时有值

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160242963.png)

此时将tomcat容器关闭（浏览器不关），发现session并没有销毁（后台无销毁日志打印）。再将tomcat容器启动，浏览器cookie中的JSESSIONID还是上次的值，此时访问index.jsp页面，发现Session重新创建了一份（代表浏览器拿此JSESSIONID并未找到对应的Session)，这是非正常情况。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160325897.png)

正常情况下，关闭服务器时，Session会钝化到磁盘中，我们重启没有活化成功是因为我们Idea没有设置tomcat，按如下设置即可重启时获取的session的id是一致的。**代表服务器重启，只要tomcat开启了活化钝化功能，浏览器不关闭还是会用的上次的session。**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160446995.png)

此时，将浏览器完全退出，再重新打开，发现Cookie已清空。再重新访问index.html，session并未创建。访问index.jsp，session重新创建。代表退出浏览器，session会话关闭失效。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160328184.png)

在index.html中新建 `a标签`

```html
<!--注意，这里使用相对路径-->  
<a href="session?sessionget=true">sessionGet</a>  
<br/>  
<a href="session?sessionget=false">sessionInvalidate</a>
```

新建Sevlet并配置web.xml

```java
public class SessionServlet extends HttpServlet {  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        String sessionGet = req.getParameter("sessionget");  
        if ("true".equalsIgnoreCase(sessionGet)) {  
            // 打印出sessionID  
            System.out.println("sessionId = " + req.getSession().getId());  
            resp.getWriter().write(req.getSession().getId());  
        }else{  
            // 销毁session  
            HttpSession session = req.getSession();  
            session.invalidate();  
            resp.getWriter().write("session invalidate");  
        }  
    }  
}
```

```xml
<servlet>  
    <servlet-name>SessionServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.SessionServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>SessionServlet</servlet-name>  
    <url-pattern>/session</url-pattern>  
</servlet-mapping>
```

测试可得，当有session时，不会新创建session。当主动销毁session时，会清空session。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160335646.png)

当点击sessionInvalidate链接时，会主动销毁session

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160335905.png)

### Session的属性变化

实现HttpSessionAttributeListener即可完成对Session中属性变化的监听

```java
public class MySessionLifeCycleListener implements HttpSessionListener, HttpSessionAttributeListener {  
    @Override  
    public void sessionCreated(HttpSessionEvent se) {  
        System.out.println("session对象创建了..." + se.getSession().getId());  
    }  
  
    @Override  
    public void sessionDestroyed(HttpSessionEvent se) {  
        System.out.println("session销毁了");  
    }  
  
    @Override  
    public void attributeAdded(HttpSessionBindingEvent event) {  
        System.out.println("session添加属性了");  
    }  
  
    @Override  
    public void attributeRemoved(HttpSessionBindingEvent event) {  
        System.out.println("session移除属性了");  
    }  
  
    @Override  
    public void attributeReplaced(HttpSessionBindingEvent event) {  
        // 注意：属性替换时，event.getValue()获取的是旧值  
        System.out.println("session替换属性了,旧值为：" + event.getValue() + "新值为：" + event.getSession().getAttribute(event.getName()));  
    }  
}
```

index.jsp中加入以下代码：

```jsp
<%  
    session.setAttribute("username", "admin");  
    Thread.sleep(3000);  
    session.setAttribute("username", "admin2");  
    Thread.sleep(3000);  
    session.removeAttribute("username");  
%>
```

启动服务，访问index.jsp可以看到

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160528546.png)

### Session的活化钝化

#### tomcat设置

进入tomcat安装目录下的conf目录，查看配置：下面的Manager标签如果解开注释就会关闭Tomcat重启session持久化功能

```xml
<Context allowCasualMultipartParsing="true">

<!-- Uncomment this to disable session persistence across Tomcat restarts -->

<!--

<Manager pathname="" />

-->

</Context>
```

对于IDEA默认不会活化session，需要配置tomcat，如下

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160446995.png)

#### Demo

要监听某个对象的活化钝化需要注意如下几点：

 - 实体类实现Serializable接口 
 - 不需要在web.xml中配置监听器 
 - 要在某实体类上实现接口（因为活化钝化、绑定解绑都是某个对象的监听）

```java
public class User implements Serializable, HttpSessionActivationListener {  
  
    @Override  
    public void sessionWillPassivate(HttpSessionEvent se) {  
        System.out.println("user" + this + "将要与session一起钝化");  
    }  
  
    @Override  
    public void sessionDidActivate(HttpSessionEvent se) {  
        System.out.println("user随session活化了");  
    }  
}
```

在index.jsp中加入以下代码：

```jsp
 <%  
    User user = new User();  
    session.setAttribute("loginUser",user);  
%>
```

关闭服务器时，可以看到session钝化

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160506186.png)

重启服务器时，session活化

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160507778.png)

### Session的绑定解绑

实体类实现HttpSessionBindingListener即可监听某对象的session绑定解绑事件

```java
public class User implements Serializable, HttpSessionActivationListener, HttpSessionBindingListener {  
  
    @Override  
    public void sessionWillPassivate(HttpSessionEvent se) {  
        System.out.println("user" + this + "将要与session一起钝化");  
    }  
  
    @Override  
    public void sessionDidActivate(HttpSessionEvent se) {  
        System.out.println("user随session活化了");  
    }  
  
    @Override  
    public void valueBound(HttpSessionBindingEvent event) {  
        System.out.println("user绑定进来了" + event.getValue());  
    }  
  
    @Override  
    public void valueUnbound(HttpSessionBindingEvent event) {  
        System.out.println("user解绑了");  
    }  
}
```

index.jsp中修改如下：

```jsp
<%  
    User user = new User();  
    session.setAttribute("loginUser",user);  
    Thread.sleep(3000);  
    session.removeAttribute("loginUser");  
%>
```

启动服务，访问index.jsp

可以看到，属性变化，绑定解绑都监听到了，但绑定解绑只针对于某个对象，属性变化针对的是session中所有的属性。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211160538900.png)

## 监听器的使用场景

- ServletContextListener：监听服务器启动停止

比如，在服务启动的时候，将服务用到的所有数据初始化，比如前台用到的ContextPath。服务器停止做一些清理工作。

- HttpSessionBindingListener：监听某个对象绑定到session域中

比如用户登录信息，在线人数信息记录。

# 国际化

国际化主要使用Locale，ResourceBundle类。通常国际化问题都是创建多个服务，而不是一个服务来完成国际化。

> Locale，区域信息，比如zh_CN、en_US，即 语言代码_国家代码 形式
> ResouceBundle，资源束，资源绑定

## Locale

Locale，区域信息

```java
@Test  
public void testLocale() {  
    // Locale默认取的是操作系统的区域信息  
    Locale aDefault = Locale.getDefault();  
    System.out.println("aDefault = " + aDefault);  
}
```

浏览器也可以查看当前区域信息，其中q=quality value代表质量值，权重，越大优先级越高

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211151301242.png)

### 日期时间国际化 DateFormat

```java
@Test  
public void testLocaleUse() {  
    Locale china = Locale.CHINA;  
  
    // 日期时间格式化  
    DateFormat dateInstance = DateFormat.getDateInstance(DateFormat.FULL, china);  
    String formatDate = dateInstance.format(new Date());  
    System.out.println("formatDate = " + formatDate);  
    }
```

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211151331059.png)

### 数字与货币国际化 NumberFormat

```java
@Test  
public void testLocaleUse() {  
    Locale china = Locale.CHINA;  
  
    // 数字格式化  
    double salary = 10000.123;  
    NumberFormat numberFormat = NumberFormat.getInstance(china);  
    String salaryStr1 = numberFormat.format(salary);  
    System.out.println("salaryStr1 = " + salaryStr1);  
  
    // 货币格式化  
    NumberFormat currencyInstance = NumberFormat.getCurrencyInstance(china);  
    String salaryStr2 = currencyInstance.format(salary);  
    System.out.println("salaryStr2 = " + salaryStr2);  
 
    }
```

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211151331298.png)

### 消息国际化 MessageFormat

普通的国际化文件，ResourceBuncle只能根据Locale获取不同区域对应的国际化展示，但占位符无法解析，需要通过消息格式化进行处理。

在properties文件中加入以下键值对

```properties
bookstore.buyBook={0} buy {1}
```

```properties
bookstore.buyBook={0} 买 {1}
```

通过以下代码测试：

```java
@Test  
public void testLocaleUse() {  
    Locale china = Locale.CHINA;  
  
    // 消息格式化  
    ResourceBundle bundle = ResourceBundle.getBundle("bookstore", china);  
    // 如果国际化文件中使用占位符，则通过resourceBundle获取的字符串会显示占位符  
    String message = bundle.getString("bookstore.buyBook");  
    System.out.println("message = " + message);  
    // 带有占位符的字符串通过 MessageFormat进行格式化，传入参数即可  
    String format = MessageFormat.format(message, "小白", "计算机书");  
    System.out.println("format = " + format);  
  
}
```

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211151339933.png)

## ResouceBundle

创建properties文件，一般名称规则是基础名_语言代码_国家代码，如bookstore_en_US.properties，其中语言代码、国家代码严格校验。

ResouceBundle就是来绑定所有的国际化资源文件

创建bookStore_en_US.properties、bookstore_zh_CN.properties国际化配置文件，并保存动态显示的信息

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211151252836.png)


```properties
bookstore.username=Little White
```

```properties
bookstore.username=小白
```

测试：

```java
public class I18nTest {  
  
    @Test  
    public void testGetBookStoreI18n() {  
        // 区域（包括语言代码与国家代码） 
        Locale us = Locale.US;  
        Locale china = Locale.CHINA;  
  
        // 1根据 基础名、区域信息 获取 资源束  
        ResourceBundle usBookStoreResourceBundle = ResourceBundle.getBundle("bookstore", us);  
        // 2资源束获取key对应的value字符串  
        String usUsername = usBookStoreResourceBundle.getString("bookstore.username");  
        System.out.println("usUsername = " + usUsername);  
  
        ResourceBundle cnBookStoreResourceBundle = ResourceBundle.getBundle("bookstore", china);  
        String cnUsername = cnBookStoreResourceBundle.getString("bookstore.username");  
        System.out.println("cnUsername = " + cnUsername);  
    }  
}
```

##  JSTL对国际化的支持

> JSTL = JSP standard tag libarary = JSP标准标签库

其中fmt前缀就是用来完成国际化的功能。

1 准备区域信息
2 使用ResourceBundle绑定国际化资源文件
3 从国际化资源文件获取值

```jsp
// 设置区域信息，从请求参数获取
<fmt:setLocale value="${param.loc}"/>
// ResourceBundle绑定
<fmt:setBundle basename="bookstore"/
<h1>
	<fmt:message key="bookstore.welcomeinfo">
		# 如果国际化文件有占位符，则通过如下的方式传参
		<fmt:param>张三</fmt:param>
		<fmt:param>
			<fmt:formateDate value="<%=new Date()%>"/>
		</fmt:param>
	</fmt:message/>
</h1>
```

# 文件上传下载

> 上传：浏览器将本地的文件上传到服务器上，交给服务器保存
> 下载：把服务器保存的内容下载来本地

##  上传

> 1 上传必须是post类型的表单提交，get不能提交大量数据，即mehthod=post
> 2 文件上传的表单要求enctype="multipart/form-data"，而不是默认的application/x-www-form-urlencoded

### 多部件 Multipart

什么是多部件？其实就是表单文件上传时每个表单项都是一个部件

配置pom.xml

```xml
<dependency>  
    <groupId>commons-fileupload</groupId>  
    <artifactId>commons-fileupload</artifactId>  
    <version>1.2.1</version>  
</dependency>  
  
<dependency>  
    <groupId>commons-io</groupId>  
    <artifactId>commons-io</artifactId>  
    <version>2.2</version>  
</dependency>
```

新建upload.jsp

```jsp
<form action="upload" method="post" enctype="multipart/form-data">  
    用户名： <input type="text" name="username" value=""><br/>  
    头像： <input type="file" name="headerimg"><br/>  
    <input type="submit" value="上传">  
</form>
```

新建UploadServlet.java

```java
public class UploadServlet extends HttpServlet {  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        // 文件是以流的形式交给服务器的  
        ServletInputStream inputStream = req.getInputStream();  
        String s = IOUtils.toString(inputStream);  
        System.out.println(s);  
        resp.getWriter().write("ok!");  
    }  
}
```

配置web.xml

```xml
<servlet>  
    <servlet-name>uploadServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.UploadServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>uploadServlet</servlet-name>  
    <url-pattern>/upload</url-pattern>  
</servlet-mapping>
```

界面显示为：

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211170154281.png)

上传后，后台打印为：

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211170130344.png)

注意：如果后台报java.lang.NoClassDefFoundError: org/apache/commons/io/IOUtils，则需要将依赖加入WEB-INF/lib目录下

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211170157664.png)

通过以上测试可以得知：

普通的表单上传来的数据：
key=value&key=value
代表`req.getParameter()`可以使用

表单文件上传的数据：
分隔符
部件1头信息
空行
部件1的内容
分隔符
部件2头信息
空行
部件2的内容
...
`req.getParameter()`不可以使用
所以，我们需要第三方jar包

### 上传文件步骤

- 导包
- 配置
- 调方法

配置pom.xml：

```java
<dependency>  
    <groupId>commons-fileupload</groupId>  
    <artifactId>commons-fileupload</artifactId>  
    <version>1.2.1</version>  
</dependency>  
  
<dependency>  
    <groupId>commons-io</groupId>  
    <artifactId>commons-io</artifactId>  
    <version>2.2</version>  
</dependency>
```

新建UploadServlet2.java

```java
public class UploadServlet2 extends HttpServlet {  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        // 是否是多部件内容  
        boolean isMultipart = ServletFileUpload.isMultipartContent(req);  
        if (isMultipart) {  
            // 创建文件工厂  
            DiskFileItemFactory diskFileItemFactory = new DiskFileItemFactory();  
            // 创建ServletFileUpload  
            ServletFileUpload servletFileUpload = new ServletFileUpload(diskFileItemFactory);  
            // 用创建ServletFileUpload解析请求，形式FileItem的list  
            // 一个FileItem就是代表一个表单项的详细信息            try {  
                List<FileItem> list = servletFileUpload.parseRequest(req);  
                readFileItems(list, req);  
                resp.getWriter().write("ok!!!");  
            } catch (FileUploadException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    public void readFileItems(List<FileItem> items, HttpServletRequest req) throws IOException {  
        // 获取tomcat服务器servletContext  
        ServletContext servletContext = req.getServletContext();  
        // 获取真实路径  
        String realPath = servletContext.getRealPath("/text");  
        for (FileItem item : items) {  
            // 判断当前是普通表单项还是文件项，true代表就是普通的表单项  
            // 比如type=text为普通项，type=file为文件项            if (item.isFormField()) {  
                System.out.println("解析普通项");  
                System.out.println("表单项key：" + item.getFieldName());  
                System.out.println("表单项value：" + item.getString());  
            } else {  
                System.out.println("解析文件项");  
                System.out.println("表单项key：" + item.getFieldName());  
                System.out.println("上传的文件名：" + item.getName());  
                System.out.println("上传的文件大小：" + item.getSize());  
                InputStream inputStream = item.getInputStream();  
                // 获取文件名,IE获取的名称是带路径的，其他浏览器就是文件名  
                String name = item.getName();  
                // 防止同名  
                name = UUID.randomUUID().toString().replace("-", "") + name;  
                File file = new File(realPath + File.separator + name);  
                FileOutputStream outputStream = new FileOutputStream(file);  
  
                IOUtils.copy(inputStream, outputStream);  
                // 关闭流  
                outputStream.close();  
                inputStream.close();  
            }  
        }  
    }  
}
```

配置web.xml：

```xml
<servlet>  
    <servlet-name>uploadServlet2</servlet-name>  
    <servlet-class>com.aitx.study.servlet.UploadServlet2</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>uploadServlet2</servlet-name>  
    <url-pattern>/upload2</url-pattern>  
</servlet-mapping>
```

新建upload2.jsp：

```jsp
<form action="upload2" method="post" enctype="multipart/form-data">  
    用户名： <input type="text" name="username" value=""><br/>  
    头像： <input type="file" name="headerimg"><br/>  
    <input type="submit" value="上传">  
</form>
```

## 下载

通常的下载方式，可以在前台代码中直接ref文件地址，这样用户点击文件，浏览器会尝试打开文件，不行的话才是下载，或者用户右键另存为也是下载。如果想要是下载，则可以通过Servlet处理请求的方式进行下载。

pom.xml导包

```xml
<dependency>  
    <groupId>commons-fileupload</groupId>  
    <artifactId>commons-fileupload</artifactId>  
    <version>1.2.1</version>  
</dependency>  
  
<dependency>  
    <groupId>commons-io</groupId>  
    <artifactId>commons-io</artifactId>  
    <version>2.2</version>  
</dependency>
```

新建download.jsp

```jsp
<a href="/download?file=test.jpg">下载图片</a>
```

新建DownloadServlet.java

```java
public class DownloadServlet extends HttpServlet {  
  
    @Override  
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        String file = req.getParameter("file");  
        if ("zip".equals(file)) {  
            // 将文件流如何正确的交给浏览器？  
  
        } else if ("jpg".equals(file)) {  
            // 1 找到图片在服务中的真实位置  
            ServletContext servletContext = getServletContext();  
            String realPath = servletContext.getRealPath("/pics/test.jpg");  
            // 2 获取要下载的文件的流  
            FileInputStream fileInputStream = new FileInputStream(realPath);  
            // 3 将这个输入流写给浏览器  
            ServletOutputStream outputStream = resp.getOutputStream();  
  
            // 输入流写给输出流  
            // 如果将流交给浏览器，浏览器认识的会直接打开，不认识的下载            
            // IOUtils.copy(fileInputStream,outputStream);


            // 所以在响应头中告诉浏览器是下载行为            
            // 解决中文名乱码            
            String filename = "test.jpg";  
            filename = new String(filename.getBytes("GBK"), "ISO-8859-1");  
            // 设置响应头  
            resp.setHeader("Content-Disposition", "attachment;filename=" + filename);  
            IOUtils.copy(fileInputStream, outputStream);  
  
            // 关闭流  
            outputStream.close();  
            fileInputStream.close();  
  
        } else {  
            resp.getWriter().write("do not have");  
        }  
    }  
}
```

配置web.xml

```xml
<servlet>  
    <servlet-name>downloadServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.DownloadServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>downloadServlet</servlet-name>  
    <url-pattern>/download</url-pattern>  
</servlet-mapping>
```


