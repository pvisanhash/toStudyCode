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

