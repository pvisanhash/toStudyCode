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

```xml
<listener>  
    <listener-class>com.aitx.study.lisentner.MyServletContextListener</listener-class>  
</listener>
```

## Demo

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

