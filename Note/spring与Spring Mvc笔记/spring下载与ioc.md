# spring下载与ioc

## Spring的下载

* **第一步**

  进入spring官网，https://spring.io/

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223681.png)

* **第二步**

  点击github小猫图标

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223682.png)

* **第三步**

  点击Access to Binaries中的链接

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223683.png)

* **第四步**

  点击https://repo.spring.io

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223684.png)

* **第五步**

  选择artifatcs

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223685.png)

* **第六步**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223686.png)

* **第七步**

 ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223687.png)



* **第八步**

distribution发布版，选择此项
  
>   document文档
>   schema 协议，逻辑表
  
  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223688.png)
  
## Spring的概述

### 什么是Spring

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223689.png)

```properties
EJB = enterprise java bean = 企业java bean
```

### Spring的好处

- 方便解耦，简化开发  （高内聚低耦合）
  AOP编程的支持	
   声明式事务的支持	
- 方便程序的测试
  方便集成各种优秀框架	
- 降低JavaEE API的使用难度

### Spring在三层中的哪一层

> Spring 对 web层 业务层  持久层  都有支持  是一个大管家 

- WEB层	：SpringMVC

- Service层：Spring的Bean管理，Spring声明式事务
  
- DAO层	：Spring的Jdbc模板，Spring的ORM模块
  
```properties
orm = object relation model = 对象关系模型
```

**总结：Spring是一个大管家 是java企业项目一站式解决方案**

### Spring的下载以及官网

- 官网: <https://spring.io/>
- 下载地址：<http://repo.spring.io/release/org/springframework/spring/>

### Spring的开发包介绍

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223690.png)

### Spring的体系结构图

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223691.png)

```properties
SpEL = spring expression language = spring 表达试语言
instrumentation = 工具
jdbc = java database connection
oxm = object xml model = 对象xml模型
jms = java message service
transaction = 事务
```

## Spring的控制反转

### 什么是控制反转

> 控制反转简称IOC , 表示之前我们创建一个对象 往往是通过new的方式创建 ,主动权控制权在我们手中,使用spring 就是让创建对象这个事 ,控制在spring手中,让我们控制权反转给了spring
> 
> 控制：就是资源的获取方式，分为主动式与被动式
> 主动式：要什么资源都是自己创建。
> 被动式：资源的获取不是我们自己创建，而是交给一个容器来创建和设置。
> 容器：管理所有的组件（有功能的类）；假设BookServlet受容器管理，BookService也受容器管理；容器可以自动的探查出哪些组件需要用到另一组件；容器帮我们创建BookService对象，并把BookService对象赋值过去；主动的new资源变为了被动的接受资源。
>    
> ioc = inversion  of control = 控制反转
>
> Bean管理 = Spring创建对象 + Spring属性(依赖)注入
>
> Bean管理有两种操作方式：XML配置方式 + 注解方式

### 控制反转的演示

1. 创建普通的java工程

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223692.png)

2. 导入jar包

在`src`或`resources`（需要右键标记为资源文件夹）下建`lib包`

方式一：右建相应的jar包`add to library`

方式二：除了上述方式导jar包，也可以在`project setting` 中 选`module` 后选择`dependencies`点击+添加jar包

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223693.png)

```properties
beans,context,core,expression属于核心容器部分
jcl= java common log = java 共公日志。一般导包的时候也要导公共日志包，也可以导其他日志包比如common-log包
```

3. 编写代码 

```java
// 在service包下写建立UserService接口
public interface UserService {
    void addUser();
}

// 在service.impl包下建立UserServiceImpl类
public class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("添加用户成功");
    }
}
```

4. 编写配置文件

在`src`或`resources`下创建配置文件，直接右键`new` > `xml configuration file` > `spring config`,配置文件名`applicationContext`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
		id是bean的唯一值，ioc容器通过id可以获取注册的对象。
		class代表创建的bean的类，底层利用反射
	-->
    <bean id="userService" class="com.xyz.java.impl.UserServiceImpl"></bean>
</beans>
```

5. 编写测试类

```java
public class IOCTest {
    public static void main(String[] args) {
        // 加载配置文件
        ApplicationContext applicationContext  = 
                new ClassPathXmlApplicationContext("applicationContext.xml");
        // 从ioc容器中，通过xml中设置的id属性拿到创建的对象
        UserService userService = 	applicationContext.getBean("userService",UserService.class);
        userService.addUser();
    }
}
```

6. 提出问题

Q：这样写麻烦 直接new一个对象调用多简单，使用spring显得很麻烦  

例如：

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223694.png)

A：

- 我们直接new对象 调用方法 是可以的 但是这样写就形成了耦合  程序一般讲究高内聚 低耦合 
- spring的写法  就没有耦合  成功把编译器异常变成运行期异常 （底层采取反射+工厂模式）

> 耦合：表示代码中(程序中)的依赖关系 

### IOC的底层原理与接口实现

#### 底层原理

>	IOC实现主要是：
>		- xml 解析
>		- 工厂模式
>		- 反射
>		
>	IOC思想是基于IOC容器完成，IOC容器底层就是对象工厂	


![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223695.png)

#### 相关接口实现

Spring提供IOC容器实现的两种方式（两个接口）：

| 序号 | 接口              |
| ---- | ----------------- |
| 1    | BeanFactory       |
| 2    | ApplicatonContext |

- BeanFactory：IOC容器基本实现，是Spring内部的使用接口 ，不提供给开发人员使用。**加载配置文件时候不会创建对象，在获取对象（使用）才会创建对象。**

- ApplicatonContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员过行使用。**加载配置文件时就会把配置文件中的对象进行创建。**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223697.png)

## Spring的依赖注入

> 依赖注入简称DI，说白了就是通过set方法 给成员变量赋值，自动的赋值；容器知道哪个组件运行的时候，需要另外一个组件，容器通过反射的形式，将容器中准备好的对象注入（利用反射给属性赋值）到组件中；只要是容器管理的组件，都能使用容器的功能。
>
> di = dependency injection = 依赖注入

### 数据预置与文件配置

1. 新建2个User类、Wife类

```java
public class User {

    // 普通属性
    private String username;
    private int age;

    // 集合属性
    private int[] array;

    private List<String> list;

    private Set<String> set;

    private Map<String, String> map;

    private Properties properties;

    // 自定义引用类型
    private Wife wife;
}  
    
    
public class Wife {
    private String username;
  	private Integer age;
}

```

2. 编写配置文件

```xml
<!--在applicationContext.xml配置文件上编写如下内容-->  
<bean id="user" class="com.xyz.code.entity.User"></bean>
```

3. 测试类获取User对象

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext ioc =
                new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = ioc.getBean("user", User.class);
        System.out.println(user);
    }
}
```

输出：

```java
com.xyz.code.entity.User@f6c48ac
```

### 依赖注入的方式

#### 1.setXxx()方法

##### 1.给普通属性赋值

直接用`<property name=xxx value=xxxValue>`方式注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.xyz.code.entity.User">
        <!--设置属性的名与值，注意实体类要有setXxx()方法-->
        <property name="username" value="xiaoming"></property>
        <property name="age" value="18"></property>
    </bean>
</beans>
```

##### 2.给自定义的引用类型赋值

- 引用外部bean

```xml
    <bean id="wife" class="com.xyz.code.entity.Wife"></bean>
    <bean id="user" class="com.xyz.code.entity.User">
        <!--设置属性的名与值，注意实体类要有setXxx()方法-->
        <property name="username" value="xiaoming"></property>
        <property name="age" value="18"></property>
        <!--引用类型使用ref来注入-->
        <property name="wife" ref="wife"></property>
    </bean>
```

上面的`ref属性`可以写`ref标签`

```xml
<bean id="user" class="com.aitx.study.domain.User">
    <property name="wife">
        <ref bean="wife"></ref>
    </property>
</bean>
<bean id="wife" class="com.aitx.study.domain.Wife"></bean>
```

- 使用内部bean

**注意内部Bean无需加id属性，因为内部创建出的对象，ioc容器无法通过id获取**

```xml
<bean id="user" class="com.aitx.study.domain.User">
    <property name="wife">
	    <!--内部bean无法通过容器获取-->
        <bean class="com.aitx.study.domain.Wife"></bean>
    </property>
</bean>
```

#### 2.引用类型级联赋值

就是既给外部引用类型赋值，也给内部引用类型赋值

第一种方式：外部bean+set方法注入

```xml
    <bean id="user" class="com.aitx.study.domain.User">
        <property name="wife" ref="wife"></property>
    </bean>
    <bean id="wife" class="com.aitx.study.domain.Wife">
        <property name="username" value="xiaohong"></property>
        <property name="age" value="18"></property>
    </bean>
```

第二种方式：内部bean+set方法注入：

```xml
<bean id="user" class="com.aitx.study.domain.User">
    <property name="wife">
        <bean class="com.aitx.study.domain.Wife">
            <property name="username" value="xiaohong"></property>
            <property name="age" value="18"></property>
        </bean>
    </property>
</bean>
```

第三种方式：外部bean+get/set方法注入

```xml
<bean id="user" class="com.aitx.study.domain.User">
    <property name="wife" ref="wife"></property>
    <!--这种级联赋值的方式要求User类有getWife()方法-->
    <property name="wife.username" value="xiaohong"></property>
    <property name="wife.age" value="18"></property>
</bean>
<bean id="wife" class="com.aitx.study.domain.Wife"></bean>
```

#### 3.Constructor构造方法

1. 给`Wife类`添加有参构造方法

```java
public class Wife {

    private String username;
    private Integer age;

    // 无参构造方法
    public Wife() {
    }

    // 有参构造方法
    public Wife(String username, Integer age) {
        this.username = username;
        this.age = age;
    }
}
```

2. 编辑配置文件

```java
    <bean id="wife" class="com.xyz.code.entity.Wife">
        <!--使用构造器来注入属性值，注意实体类要有有参构造器-->
        <constructor-arg name="username" value="xiaohong"></constructor-arg>
        <constructor-arg name="age" value="19"></constructor-arg>
    </bean>
```

3. 测试

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext ioc =
                new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = ioc.getBean("user", User.class);
        System.out.println(user);
    }
}
```

当然我们也可以使用用index指定参数位置

```xml
<bean id="wife" class="com.aitx.study.bean.Wife">  
    <constructor-arg index="0" value="junli"/>  
    <constructor-arg index="1" value="18"/>  
</bean>
```

如果有重载的构造器，可以加type限定

```xml
<bean id="wife" class="com.aitx.study.bean.Wife">  
    <constructor-arg index="0" value="junli" type="java.lang.String"/>  
    <constructor-arg index="1" value="18" type="java.lang.Integer"/>  
</bean>
```

#### 4.P命名空间（了解）

> 名称空间：在XML中名称空间是用来防止标签重复的，相当于java中的包的概念，一个文件夹
> p = property = 属性

1. 在配置文件添加约束

只要在`bean标签`中使用`p:`就会有提示在`beans标签`中插入`p名称空间`，所以不用先加约束

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

2. 使用P空间赋值

```xml
<bean id="user2" class="com.xyz.code.entity.User"
       p:username="hello" p:age="20" p:wife-ref="wife">
</bean>
```

3. 测试

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext ioc =
                new ClassPathXmlApplicationContext("applicationContext.xml");
        User user = ioc.getBean("user2", User.class);
        System.out.println(user);
    }
}
```

#### 5.标签体内部赋值（了解）

属性赋值时value可以在标签全内部赋值，比如这里在`bean的标签体`内进行赋值：

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <!--设置属性的名与值，注意实体类要有setXxx()方法-->
        <property name="username" >
            <value>xiaoming</value>
        </property>
        <property name="age">
            <value>18</value>
        </property>
        <!--原来 引用类型使用ref来注入-->
        <property name="wife" >
            <!--这里用ref标签-->
           <ref bean="wife"/>
        </property>
     </bean>
```

#### 6.数组

- 无素是普通类型时：

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <property name="array">
            <array>
                <value>1</value>
                <value>2</value>
                <value>3</value>
            </array>
        </property>
    </bean>
```

- 元素是引用类型时：

<font color=red>注意：要在bean处填写引用对象的id</font>

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <property name="array">
            <array>
               <ref bean=""></ref>
               <ref bean=""></ref>
            </array>
        </property>
    </bean>
```

#### 7.list集合

`list标签`相当于创建ArrayList对象

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <property name="list">
	       <!--list标签相当于创建ArrayList对象-->
           <list>
               <value>a</value>
               <value>b</value>
               <value>c</value>
           </list>
        </property>
    </bean>
```

`list标签体`里面可以有value，bean ，ref

```xml
<list>  
    <value>1</value>  
    <bean class="com.aitx.study.bean.User"></bean>  
    <ref bean="user"></ref>  
</list>
```

#### 8.set集合

`set标签`相当于创建LinkedHashSet对象

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <property name="set">
	       <!--set标签相当于创建LinkedHashSet对象-->
           <set>
               <value>a</value>
               <value>b</value>
               <value>c</value>
           </set>
        </property>
    </bean>
```

同样地，`Set标签体`里面可以有value，bean ，ref

```xml
<set>  
    <value>1</value>  
    <bean class="com.aitx.study.bean.User"></bean>  
    <ref bean="user"></ref>  
</set>
```

#### 9.Map集合

`map标签`相当于创建LinkedHashMap对象

```xml
    <bean id="user" class="com.xyz.code.entity.User">
    	<!--map标签相当于创建LinkedHashMap对象-->
        <property name="map">
            <map>
                <entry key="key1" value="value1"></entry>
                <entry key="key2" value="value2"></entry>
            </map>
        </property>
    </bean>
```

同样地，`map标签体`里面可以有value，bean ，ref

```xml
<map>  
    <value>1</value>  
    <bean class="com.aitx.study.bean.User"></bean>  
    <ref bean="user"></ref>  
</map>
```

#### 10.properties

`props标签`相当于创建Porperties对象

```xml
    <bean id="user" class="com.xyz.code.entity.User">
        <property name="properties">
          	<!--注意这里是props,props标签相当于创建Porperties对象-->
            <props>
                <prop key="pro1">aaa</prop>
                <prop key="pro2">bbb</prop>
                <prop key="pro3">ccc</prop>
            </props>
        </property>
    </bean>
```

#### 11.集合提取

这里使用`util名称空间`（spring会自动导入相关的约束）来将公共的集合提取出来。

比如下面将List集合提取出来：

```xml
<bean id="user" class="com.aitx.study.domain.User">
    <property name="list" ref="myCommonList"></property>
</bean>
<!--util:list相当于创建了List，id也可以被ioc容器认识-->
<util:list id="myCommonList">
	<!--添加元素-->
    <value>a</value>
    <value>b</value>
    <value>c</value>
</util:list>
```

#### 12. 注入字面量（null与特殊符号）

> 字面量：比如 `String username = "xiaoming";` 右边的就是字面量

`property标签`普通属性赋值后会将字符串自动类型转换成相应的java类型，像这种复杂赋值都是在`property标签体`中进行或通过ref引用。

##### null值

```xml
<property name="username">
    <null/>
</property>
```

##### 含特殊符号

有两种方式处理：

- 把特殊符号进行转义，如 `<` 转义成`&lt;`  ， `>`转义成`&gt;`

- 把特殊符号内容写到CDATA

这里有特殊符号 `<` 小于号

```xml
<property name="username">
    <value><![CDATA[<<南京>>]]></value>
</property>
```

## Spring创建对象的方式

> 使用spring之后  创建对象的事情 交给了Spring  那么Spring创建对象有哪几种方式
>
> spring有两种类型bean,一种普通bean,另外一种工厂bean
>
> 普通bean: 在配置文件中定义bean类型就是返回类型
>
> 工厂bean: 在配置文件中定义bean类型与返回类型不一样

### 1.无参构造方法(默认情况下) 

> 可以在无参构造方法中打印一句话，即可查看

```xml
	<!--使用无参构造方法构建对象-->
	<bean id="user" class="com.xyz.code.entity.User"></bean>
```

### 2.静态工厂的方式

> 顾名思义：一个带有 静态方法 的工厂类

1. 创建一个工厂类，带静态方法

```java
public class UserStaticFactory {
    public static User getUser(){
        return new User();
    }
}
```

2. 编写配置文件

```xml
<!--指定了工厂方法，则ioc中注册的是User对象-->
<bean id="userStaticFactory" 
          class="com.xyz.code.entity.UserStaticFactory" factory-method="getUser"></bean>
```

### 3. 实例工厂的方式

> 一个带有 实例方法 的工厂类
>
> 要想获取生成的对象，必须先有工厂实例对象，通过实例对象创建对象。提供所有的方法都是“非静态”的。

1. 实例工厂与方法

```java
// 相对于静态工厂，只是少了个static修饰符
public class UserInstanceFactory {
    public User getUser(){
        return new User();
    }
}
```

2. 编写配置文件

注意：一个bean只产生一个对象

```xml
    <!--要想获取生成的对象，必须先有工厂实例对象，通过实例工厂对象创建所需对象。提供所有的方法都是“非静态”的。-->
    <bean id="userInstanceFactory" 
          class="com.xyz.code.entity.UserInstanceFactory"></bean>
    <bean id="getUser" 
          factory-bean="userInstanceFactory" factory-method="getUser"></bean>
```

### 4. 实现FactoryBean接口的方式

1. 创建工厂类实现FactoryBean接口并实现方法

```java
public class UserFactory implements FactoryBean<User> {

    /**
     * 这个方法就是工厂类生成对象的方法
     *
     * @return
     * @throws Exception
     */
    @Override
    public User getObject() throws Exception {
        // 这里可以以单例模式创建对象与isSingleton()对应
        return new User();
    }

    /**
     * 返回对象类型的方法
     *
     * @return
     */
    @Override
    public Class<?> getObjectType() {
        return null;
    }

    /**
     * 是否单例的方法
     *
     * @return
     */
    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }
}
```

2. 修改配置文件

```xml
<!--这里定义类型为UserFactory类型，但生成对象是User类型->
<bean id="user" class="com.aitx.study.domain.UserFactory"></bean>
```

## Bean的生命周期(lifecycle)

> 首先要了解：Spring默认启动的时候就创建Bean，并且是个单实例的Bean
>
> 如果其他组件需要使用这个Bean的时候直接从容器中取 
>
> 我们可以通过设置懒加载让bean在使用的时候 再创建

### 环境搭建

新建类并提供初始化方法与销毁方法

```java
public class Car {

    private String carName;

    public Car() {
        System.out.println("调用构造器了");
    }

    public void setCarName(String carName) {
        this.carName = carName;
        System.out.println("调用setCarName()方法");
    }

    public void init() {
        System.out.println("调用初始化方法了");
    }

    public void destroy() {
        System.out.println("调用销毁方法了");
    }
}
```

```xml
<bean id="car" class="com.aitx.study.domain.Car"></bean>
```

测试代码：

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext ioc =
                new ClassPathXmlApplicationContext("applicationContext.xml");
    }
}
```

```java
//输出:
调用构造器了
```

即ioc容器启动时就生成了对象并注册到容器中，并且可以看到自定义的方法并没有加载

### 懒加载

> 默认情况下 spring一启动对象则会被实例化。我们配置懒加载 ，让对象使用（获取）的时候再实例化

```xml
<bean id="car" class="com.aitx.study.domain.Car" lazy-init="true"></bean>
```

开如懒加载，则容器启动时不会实例化对象，只有在使用的时候才会实例化对象

### 生命周期属性（初始化方法与销毁方法）

```xml
    <bean id="car" class="com.aitx.study.domain.Car" init-method="init" destroy-method="destroy">
        <property name="carName" value="littleAnt"></property>
    </bean>
```

测试代码：

```java
    public static void main(String[] args) {
        ApplicationContext ioc =
                new ClassPathXmlApplicationContext("applicationContext.xml");
    }
```

```java
//输出：
调用构造器了
调用setCarName()方法
调用初始化方法了
```

可以看到先调用构造器再调用set()方法后调用初始化方法，只有当容器关闭时才会调用销毁方法

```java
@Test
public void testLifeCycle() {
    ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
  	// 注意close()方法并不在ApplicationContext接口中定义
    // 关闭容器时才会调用销毁方法
    ioc.close();
}
```

```java
// 输出：
调用构造器了
调用setCarName()方法
调用初始化方法了
调用销毁方法了  
```

### Bean后置处理器接口

Bean后置处理器BeanPostProcessor注册到容器中对于所有的Bean都生效，postProcessBeforeInitialization()在初始化方法之前执行，postProcessAfterInitialization()在初始化方法之后执行。

后置处理器注册到容器中对所有对象都生效(包括自身)。

创建BeanPostProcessor接口实现类

```java
public class MyBeanPost implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之前执行的后置处理");
        return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之后执行的后置处理");
        return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
    }
}
```

将接口实现类对象交给Spring管理

```xml
<bean id="myBeanPost" class="com.aitx.study.domain.MyBeanPost"></bean>
```

进行测试：

```java
public class IocTest {

    @Test
    public void test2() {
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        applicationContext.close();
    }
    
}
```

```java
// 输出：
在初始化之前执行的后置处理
在初始化之后执行的后置处理
调用构造器了
调用setCarName()方法
在初始化之前执行的后置处理
调用初始化方法了
在初始化之后执行的后置处理
调用销毁方法了
```

第1，2行显示的是MyBeanPost对象的生命周期

从中可以知道Spring中Bean的生命周期分为以下7步：

```properties
1.无参构造器

2.set()方法进行依赖注入

3.把bean实例传递给bean后置处理器的postProcessBeforeInitialization()方法

4.初始化方法(需要进行配置)

5.把bean实例传递给bean后置处理器的postProcessAfterInitialization()方法

6.可以从IOC容器获取

7.当容器关闭时，调用bean的销毁方法(需要配置)
```

## Bean的作用域 scope

- 单例模式（默认值）

**单例(默认值 )  全局只有一个对象 **

 ```xml
// 加不加scope="singleton"一样
<bean id="user" class="com.xyz.code.entity.User"></bean>
<bean id="user" class="com.xyz.code.entity.User" scope="singleton"></bean>
 ```

- 多例模式（原型 = 原来的类型）

**多例：用一次创建一个新对象 本身实现懒加载  就算取消懒加载也无效**

>懒加载就是容器启动时未生成对象并注册到容器中

```xml
<bean id="user" class="com.xyz.code.entity.User" scope="prototype"></bean>
```

## Bean管理XML自动装配

> 什么是自动装配？
>
> 根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入

```java
public class Employee {

    private Dept dept;

    public Dept getDept() {
        return dept;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }
    
}

public class Dept {
    
}
```

解释不同方式的自动装配 ：

有五种自动装配的方式。

- 默认：

默认的方式是不进行自动装配，通过显式设置ref 属性来进行装配。

```xml
<bean id="employee" class="com.aitx.study.domain.Employee">
    <property name="dept" ref="dept"></property>
</bean>
<bean id="dept" class="com.aitx.study.domain.Dept"></bean>
```

当然也可显式地注明注入方式为no：不自动注入。

```xml
<bean id="employee" class="com.aitx.study.domain.Employee" autowire="no"></bean>
<bean id="dept" class="com.aitx.study.domain.Dept"></bean>
```

- byName：

通过参数名自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byname，之后容器试图匹配、装配和该bean的属性具有相同名字的bean。

```xml
<bean id="employee" class="com.aitx.study.domain.Employee" autowire="byName"></bean>
<bean id="dept" class="com.aitx.study.domain.Dept"></bean>
```

- byType：

通过参数类型自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byType，之后容器试图匹配、装配和该bean的属性具有相同类型的bean。如果有多个bean符合条件，则抛出错误。

```xml
<bean id="employee" class="com.aitx.study.domain.Employee" autowire="byType"></bean>
<bean id="dept" class="com.aitx.study.domain.Dept"></bean>
```

- constructor：

这个方式类似于byType。测试只有无参构造器则不会自动注入，不抛异常。如下图提供有参构造器，则成功注入，并且没有无参构造器XML也不会报错。

```java
public class Employee {

    private Dept dept;

    public Employee() {
    }

    public Employee(Dept dept) {
        this.dept = dept;
    }

    public Dept getDept() {
        return dept;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }

}
```

```xml
<bean id="employee" class="com.aitx.study.domain.Employee" autowire="constructor"></bean>
<bean id="dept" class="com.aitx.study.domain.Dept"></bean>
```

## 控制反转包扫描的方式(注解方式)

> 我们创建对象的任务交给spring，只需在配置文件中配置bean标签就可以了，但是如果实际开发中配置的bean比较多，比较麻烦，我们可以通过包(组件)扫描+注解的方式实现统一配置  

### 1.添加aop.jar

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223698.png)

### 2.配置包扫描

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223699.png)

```xml
<!--context名称空间会自动导入的-->
<!--包扫描会自动扫描指定的包及其子包，后面加上注解就能生成对象并注册--> 
<!--配置多个包，每个包之间以逗号间隔-->
<context:component-scan base-package="com.xyz.code"/>

<!--可以配置过滤器-->
<!--如下代表：不使用默认过滤器，指定过滤为：带有@Controller注解的进行扫描-->
<context:component-scan base-package="com.aitx.study" use-default-filters="false">
	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
<!--如下代表：排除带有@Controller注解的-->
<context:component-scan base-package="com.aitx.study" >
	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

### 3.在类上添加注解

> 组件就是具有功能的类

@Component注解 ，一个类加了注解表示这个类成为了Spring的一个组件，就是加入到IOC容器中
；并且针对java的三层架构，有延伸注解 @Controller @Service @Respository

- `@Component`
- `@Controller`
- `@Service`
- `@Repository`

```java
// 可以指定id,默认是类名首字母小写
@Component 
public class Student {

}
```

### 4.基于注解方式实现属性注入

- `@Autowire`：

根据属性类型进行自动装配 ,不需要set()方法

```java
public class UserServiceImpl implements UserService {
    
    @Autowired
    private UserDao userDao;
    
    @Override
    public void addUser() {
        System.out.println("add user success");
    }
}
```

- `@Qualifier`：

根据名称过行注入，要和@Autowire一起使用，使@Autowire可以根据名称进行注行,不需要set()方法

```java
public class UserServiceImpl implements UserService {

    @Qualifier(value = "userDaoImpl")
    @Autowired
    private UserDao userDao;

    @Override
    public void addUser() {
        System.out.println("add user success");
    }
}
```

- `Resource`：

可以根据类型注入，也可以根据名称进行注入。

```java
public class UserServiceImpl implements UserService {


//    @Resource // 根据类型注入
    @Resource(name = "userDaoImpl") // 根据名称注入
    private UserDao userDao;

    @Override
    public void addUser() {
        System.out.println("add user success");
    }
}
```

- `@Value`: 

注入普通类型属性

```java
@Component
public class Dog {

    @Value("小狗")
    private String name;
}
```

## 完全注解方式开发初入门

1. 创建配置类，替代XML配置文件

注解方式还保存有配置文件，使用配置类将完全以注解方式进行开发 。

```java
@Configuration // 代表配置类，替代XML配置文件
@ComponentScan(basePackages = {"com.aitx.study"})
public class MyConfig {
}
```

2. 编写测试类

```java
public class IocTest {

    @Test
    public void test2() {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig.class);
        Dog dog = applicationContext.getBean("dog", Dog.class);
        System.out.println(dog);
    }

}
```

## 加载外部配置文件和取值

### 1. 使用xml配置的方式来取值

1. 定义properties文件

在resources或类路径src下创建test.properties文件，内容是：

```properties
//注意：编码格式一定要utf-8,一般习惯加前缀
xyz.username=xiaoming
xyz.age=18
```

2. 编写xml配置文件

通过${}方式取值

```xml
    <!--通过属性占位符引入属性文件，位置是类路径下-->
<context:property-placeholder
               location="classpath:test.properties"></context:property-placeholder>
    
<bean id="user3" class="com.xyz.code.entity.User">
    <!--通过${}方式取值-->
    <property name="username" value="${xyz.username}"></property>
    <property name="age" value="${xyz.age}"></property>
</bean>
```

### 2. 包扫描的方式(注解方式)

1. 配置包扫描

要使用注解就要加上包扫描

```xml
<!--包扫描会自动扫描指定的包及其子包，后面加上注解就能生成对象并注册--> 
<context:component-scan base-package="com.xyz.code"/>
```

2. 修改Employee类

通过`@Value`注解取值，${}方式

```java
@Component
// 指定属性源在哪，这指定是类路径下
@PropertySource(value = "classpath:test.properties")
public class Employee {
    // 通过Value注解取值，${}方式
    @Value(value = "${xyz.username}")
    private String employeeName;
    @Value(value = "${xyz.age}")
    private Integer employeeAge;
    // 自行提供getter与setter
}
```

3. @Value的其他用法

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042223700.png)

## 异常分析

用了注解但没有引入aop包，NoClassDefFoundError

```properties
// 无此类定义发现 错误
nested exception is java.lang.NoClassDefFoundError: org/springframework/aop/TargetSource
```

类路径英文名是classpath，全小写，如果写错会出现以下错误，FileNotFoundException

```properties
// 文件找不到 异常
nested exception is java.io.FileNotFoundException: class path resource [classPath:test.properties] cannot be opened because it does not exist
```

