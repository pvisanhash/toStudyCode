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

# Json

JavaScript Object Notation，js对象表示法，是一种轻量级的数据交换格式。

## JS对象

我们首先看下JS对象的基本用法

```js
{
	key1:value1,
	key2:value2
}
```

其中，value可以有很多种类型：

- 基本类型（字符串、数字、布尔值）
- 对象
- 数组

比如定义一个学生：

```js
var student = 
{
	lastName:"张三",
	age:18,
	isGirl:false,
	car:{
		brand:"BMW",
		price:30000
	},
	books:[
		{
			bookName:"西游记",
			price:99.99
		},
		{
			bookName:"红楼梦",
			price:100
		}
	]
}
```

并且，JS对象的key可以是字符串类型，这样就变成JSON对象（也是JS对象）

```js
var student2 = 
{
	"lastName":"张三",
	"age":18,
	"car":{
		brand:"BMW",
		price:30000
	},
	"books":[
		{
			"bookName":"西游记",
			"price":99.99
		},
		{
			"bookName":"红楼梦",
			"price":100
		}
	],
	"isGirl":false
}
```

如果服务器返回给浏览器的数据是JS对象是这种样子的，浏览器使用JS解析就会很方便。JSON对象的要求与JS对象一样，只不过Key必须是字符串（JS对象的Key可以是字符串也可以不是）

因为JSON对象进行网络传输，HTTP只能传输文本，所以一般要转成JSON对象的字符串，所以JSON也被为JS对象字符串表示法。

```js
<script type="text/javascript">
	// 定义一个JSON对象（也是JS对象）
    var student = {  
        "lastName":"张三",  
        "age":18  
    }  
    alert(typeof student); // object类型
    // JSON.stringify()方法可以将json对象转化为json对象字符串  
    var strJson = JSON.stringify(student);  
    alert(typeof strJson); // string 类型
    // JSON.parse()方法可以将json字符串转为json对象
    var student2 = JSON.parse(strJson);  
    alert(typeof student2);// object类型  
</script>
```

# AJAX

> AJAX：Asynchronous JavaSrcipt And XML，异步的JS与XML

AJAX是一种无刷新页面与服务器的交互技术，页面不刷新就可以收到服务器响应的数据。

原来的交互：

1. 发送请求
2. 服务器收到请求，调到对应的Servlet进行处理请求， Servlet处理完成会有响应信息生成
3. 浏览器收到了服务器响应的数据，把之前的页面清除，展示新的数据。

现在的交互：（XmlHttpRequest请求对象，AJAX基于此对象）

1. XmlHttpRequest对象帮我们发送请求
2. 服务器收到请求，调到对应的Servlet进行处理请求， Servlet处理完成会有响应信息生成
3. XmlHttpRequest对象收数据（浏览器就感受不到这个数据了，XHR对象收到这个数据）

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211302356615.png)

浏览器中可以看到XHR

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211302352861.png)

原生AJAX的XMLHttpRequest异步请求方式：

```js
// 创建XMLHttpRequest对象  
var xhr = new XMLHttpRequest();  
// 建立连接，GET请求，URL为login，是否异步为true 
xhr.open("GET", "login", true);  
// 发送请求  
xhr.send();  
// 监听xhr的状态  
xhr.onreadystatechange = function () {  
    // readyState=4且status=200  
    if (xhr.readyState == 4 && xhr.status == 200) {  
        // responseText就是响应的内容  
        document.getElementById("myDiv").innerHTML = xhr.responseText;  
    }  
}
```

因为原生的AJAX请求方式非常麻烦，使用JQuery包装后的ajax请求：

这里使用`$.get()`方法演示

webapp/js下导入jquery.js文件（JQuery下载参考 [JQuery官网](https://releases.jquery.com/)）

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212010049316.png)

新建ajax.jsp页面

```jsp
<html>  
<head>  
    <title>AJAX</title>  
</head>
<!--可以移到head标签中-->
<script src="js/jquery.js"></script>  
<body>  
<a href="getInfo" id="aBtn">获取信息</a>  
</body>  
<script type="text/javascript">
	// 给a标签绑定点击事件并阻止自动跳转，用jquery中ajax的方式异步请求
    $("#aBtn").click(function () {  
        var params = {  
            lastname: "zhangsan"  
        }  
        // url,请求地址
        // data,请求发送的数据，可以是k=v&k=v,也可以是js对象
        // callback是回调函数
        // type指定返回的类型,jquery可以自动转为指定的类型
        $.get("getInfo", params, function (resp) {  
            // resp代表服务器给我们的数据，如果服务器转发一个页面的数据，resp就代表整个页面  
            alert(resp)  
        });  
        return false;    
    });  
</script>  
</html>
```

新建GetInfoServlet.java

```java
public class GetInfoServlet extends HttpServlet {  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        String lastname = req.getParameter("lastname");  
        System.out.println("lastname = " + lastname);  
        req.getRequestDispatcher("/ajax.jsp").forward(req, resp);  
    }  
}
```

可以看到因为Svelte中又将请求转发到ajax.jsp页面，所以ajax请求得到的响应为html页面数据，如果我们返回json数据，ajax得到的响应也就为json格式。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212010038896.png)


AJAX改变了我们传统的交互方式：
1. 发请求
2. 服务器收到请求，处理请求经常要给页面携带数据。request.setAttribute("map",map)转发到页面。
3. 浏览器收到页面数据，在页面上使用el表达式获取数据
导致页面整个刷新，造成很大的服务器负担。

只让服务器返回我们需要的部分数据即可，不用返回整个页面；xhr替定浏览器发送请求、接受响应，利用dom增删改的方式来改变页面效果。

什么是ajax?
xhr对象向服器发送请求，并收到响应数据，利用dom增删改的方式改变页面效果。异步无刷新的技术。

>异步：不会阻塞浏览器。ajax请求相当于新开一个线程去处理。
>同步：会阻塞浏览器，因为需要等到服务器整个处理完请求，完成响应以后才能做其他事情。

相同的，我们也可以发`$.post`请求

```js
<script type="text/javascript">
	// 给a标签绑定点击事件并阻止自动跳转，用jquery中ajax的方式异步请求
    $("#aBtn").click(function () {  
        var params = {  
            lastname: "zhangsan"  
        }  
        // url,请求地址
        // data,post请求发送的数据，可以是k=v&k=v,也可以是js对象
        // callback是回调函数
        // type指定返回的类型,jquery可以自动转为指定的类型
        $.post("getInfo", params, function (resp) {  
            // resp代表服务器给我们的数据，如果服务器转发一个页面的数据，resp就代表整个页面  
            alert(resp)  
        });  
        return false;    
    });  
</script>  
```

也可以发`$.ajax`请求：

```js
// ajax请求的参数可以是一个对象  
var options = {  
    url: "getInfo", // 请求地址  
    type: "GET", // 请求方式  
    data: "lastname=zhangshan", // 请求数据  
    success: function (resp) {  
        alert("成功")  
    }, // 成功后的回调函数  
    dataType: "json", // 返回值类型  
    async: true, // 是否异步  
    error: function (xhr, textStatus) {  
        alert(textStatus);  
    }, // 失败后的回调函数  
}  
// 发送ajax请求,所有请求的属性都是可以通过个js对象定义的  
$.ajax(options);
```

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

# Servlet 服务器小程序

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

# JSP页面

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

## JSTL

Jsp Standard Tag Library，Jsp标准标签库。

## EL

Expression Laguage，表达式语言，用于简化Jsp页面取值操作的。EL只能取出11个对象中的值

> 4个域对象：
> 	pageScope
> 	requestScope：${reqeuestScope.username} == request.getAttribute("username")，请求中包含请求域。
> 	sessionScope：
> 	applicationScope
> 7个其他对象：
> param：获取请求参数 ${param.usernmae} == request.getParameter("username")	 	
> paramValues：获取请求参数(多选框) ${paramValues.hobby} == request.getParameters("hobby")	 	
> header：请求头
> headerValues：请求头对应的多个值
> cookie：获取cookie
> initParam：获取web.xml中配置的初始化参数


其中，initParam获取web.xml中配置的初始化参数，比如：

```xml
<context-param>  
    <param-name>username</param-name>  
    <param-value>xiaoming</param-value>  
</context-param>
```

# Cookie与Session

## Cookie

用cookie浏览器端保存少量数据的一种技术。

特点：

1. 少量
2. 都是纯文本
3. 保存的当前网站cookie，每次访问这个网站都会携带

使用：

 服务器如何给浏览器发送保存Cookie

```java
public class CookieServlet extends HttpServlet {  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        // 创建Cookie对象  
        Cookie cookie = new Cookie("username", "xiaoming");  
        // 响应添加cookie  
        resp.addCookie(cookie);  
  
        // 页面输出内容  
        resp.getWriter().write("set cookie");  
    }  
}
```

web.xml中配置Servlet

```xml
<servlet>  
    <servlet-name>cookieServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.CookieServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>cookieServlet</servlet-name>  
    <url-pattern>/cookie</url-pattern>  
</servlet-mapping>
```

访问CookieServlet,可以看到响应头中有设置Cookie

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240340053.png)


然后，在Application-Cookiek中可以看到Cookie已经有了

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240340629.png)


上面看到响应头中有`Set-Cookie`，所以我们可以直接在代码中设置响应头

```java
public class CookieServlet extends HttpServlet {  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        // 响应头添加cookie  
        resp.setHeader("Set-Cookie","username=xiaoming");  
        // 页面输出内容  
        resp.getWriter().write("set cookie");  
    }  
}
```

一旦保存Cookie后，访问网站的各个请求的请求头中都会带上Cookie 

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240348076.png)


Cookie默认有效时间：

默认是会话期间有效（浏览器只要不关，cookie就在；cookie存在浏览器的进程中，关闭浏览器会清空Cookie，但Session不会清空，Session只会过期失效或主动失效。）

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240359603.png)

Cookie手动设置的有效时间：

```java
// 设置最大有效时间，单位秒 
// 正数是多少秒后失效
// 负数表示Cookie就是会话Cookie，随浏览器同生共死
// 0表示立即失效
cookie.setMaxAge(100);
```

修改或删除Cookie

```java
@Override  
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
    // 修改或删除cookie，都是同名Cookie进行覆盖  
    Cookie cookie = new Cookie("username", "newValue");  
    // 这里删除，设置0秒过期  
    cookie.setMaxAge(0);  
    resp.addCookie(cookie);  
    // 页面输出内容  
    resp.getWriter().write("set cookie");  
}
```

可以看到响应头中设置了新cookie值与过期时间为1970年

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240410834.png)


后台获取Cookie：

```java
// 只能获取所有cookie；可以遍历，注意判断cookies不能为null  
Cookie[] cookies = req.getCookies();
```

## Session

服务器端保存当前会话大量数据的一种技术（Cookie是浏览器端保存少量数据的一种技术），Seesion保存的数据是可以在同一个会话期间共享。

使用：

```java
// 获取session  
HttpSession session = req.getSession();  
// session保存值  
session.setAttribute("username","xiaoming");  
// session获取值  
session.getAttribute("username");
```

### 会话控制

为什么在别的地方给session中保存的数据，在另外一个地方可以获取出来：

> 会话控制：
> 
> 我们和服务器进行交互期间，可能需要何存一些数据，服务器就为每个会话专门创建一个map；这个 map用来保存数据；这个map我们就叫session；
> 
> 100个会话就有100个map；每次创建map的时候，这个map就有一个唯一标识：（JSESSIONID，会话ID）；
> 
> 利用浏览器每次访问都会带上所有的cookie ；
> 
> 服务器只需要创建一块能保存数据的map，给这个map一个唯一标识（JSESSIONID），创建好以后浏览器保存这个map的标识（Set-Cookie:JSESSIONID=xyz），以后浏览器访问就会带上这个map标识，服务器就按照标识找到这个map，取出这个map中的数据

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211240438576.png)

特别地：

Cookie失效：会话关闭（浏览器关闭），默认是Cookie清空；通过Cookie持久化技术可以继续找到之前的Session。

Session失效：自动超时，手动失效。

### 令牌机制

F5刷新会将之前的请求再次发出去，表单会重复提交。

原理：

用户在访问页面时，服务器会给用户的请求中分配一个令牌（一组值），并且将此令牌拷贝一份放在Session中，在用户提交后，会将用户提交时所携带的令牌和session中的令牌进行比对，通过之后会将服务器中的令牌销毁(为session覆盖原来的令牌值)，到用户再次点击时（表单重复提交，传的值相同），再次比对令牌就会找不到服务器中的备份令牌，从而判断出此次请求为二次或多次提交，防止系统紊乱崩溃。

应用场景：

- 防止表单重复提交
- 验证码验证

新建TokenServlet.java

```java
public class TokenServlet extends HttpServlet {  
  
    @Override  
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
        HttpSession session = req.getSession();  
        // 获取session中的token  
        String sessionToken = (String) session.getAttribute("token");  
        // 获取请求中的token  
        String reqToken = req.getParameter("token");  
        // 如果session的token与请求的token一致，则处理请求  
        if (sessionToken.equals(reqToken)) {  
            // 因为刷新(f5)是按原表单数据提交，所以页面带来的令牌值不会更新
            // 注意：刷新页面是会更新令牌的  
            // 处理请求前，先替换session中的token值            session.setAttribute("token", UUID.randomUUID().toString().replace("-", ""));  
            // 处理请求  
            resp.getWriter().write("success");  
        } else {  
            // 令牌校验不过  
            resp.getWriter().write("fail");  
        }  
  
    }  
}
```

配置web.xml

```xml
<servlet>  
    <servlet-name>tokenServlet</servlet-name>  
    <servlet-class>com.aitx.study.servlet.TokenServlet</servlet-class>  
</servlet>  
<servlet-mapping>  
    <servlet-name>tokenServlet</servlet-name>  
    <url-pattern>/token</url-pattern>  
</servlet-mapping>
```

新建token.jsp

```jsp
<%  
    String token = UUID.randomUUID().toString().replace("-","");  
    session.setAttribute("token",token);  
%>  
  
<form action="token" method="get">  
    <input type="hidden" name="token" value="${sessionScope.token}">  
    <input type="text" name="username" value="">  
    <input type="submit">  
</form>
```

# Listener 监听器

监听器：监听对象（ServletContext），监听事件（ServletContextEvent），触发行为（实现的方法体）

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211301316547.png)

## 按监听对象进行分类

> 生命周期lifecycle：从创建到销毁的过程
> 
> 常用域对象pageContext，requst，session，application
> 
> ServletContext：
> 1. 一个web项目对应一个ServletContext，它代表当前web项目的信息
> 2. 还可以作为最大的域对象在整个项目运行期间共享数据

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
- web.xml中注册（session活化钝化监听器、session绑定解绑监听无需配置）

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

# Filter 过滤器

## 使用步骤

1. 实现Filter接口
2. 去web.xml进行配置

可以看到，三大组件基本都需要在web.xml中进行注册（除过Listener中session极活化钝化，session绑定解绑需要javaBean实现接口，不注册）

实现接口：

```java
public class MyFirstFilter implements Filter {  
      
    @Override  
    public void init(FilterConfig filterConfig) throws ServletException {  
  
    }  
  
    @Override  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {  
        chain.doFilter(request, response);  
    }  
  
    @Override  
    public void destroy() {  
  
    }  
}
```

WEB-INF/web.xml配置：

```xml
<filter>  
    <filter-name>myFirstFilter</filter-name>  
    <filter-class>com.aitx.study.filter.MyFirstFilter</filter-class>  
</filter>  
<filter-mapping>  
    <filter-name>myFirstFilter</filter-name>  
    <url-pattern>/</url-pattern>  
</filter-mapping>
```

其中`<url-pattern>`有以下几种写法：

1. 精确匹配

```url
/pics/haha.img，/hello/login
```

2. 路径匹配（模糊匹配）

```url
/pics/* 拦截pics下的所有请求
```

3. 后缀匹配 （模糊匹配）

```url
*.jsp 拦截所有以.jsp结尾的请求
```

 注意：`/pics/*.jsp`不能使用，即不是路径匹配，也不是后缀匹配，总之，不能两两组合使用

## Filter原理

Servlet前进行请求过滤。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202211301312402.png)

# 国际化

国际化主要使用Locale，ResourceBundle类。通常国际化问题都是创建多个服务，而不是一个服务来完成国际化。

> Locale，区域信息，比如zh_CN、en_US，即 语言代码_国家代码 形式
> ResouceBundle，资源束，资源绑定
> 
> 通常，我们可以通过 请求头传参 或 请求数据传参 确定国际化

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
            // 设置响应头，告诉浏览器是下载而不是打开文件  
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


