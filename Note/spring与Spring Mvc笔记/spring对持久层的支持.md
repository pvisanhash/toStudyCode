# Spring对持久层支持（了解）

> Spring是javaEE开发 一站式解决方案 提供了对各个层的支持  我们也可以通过Spring结构图看到   对持久层的支持  分为jdbc模板和orm模块  
>
> jdbc template  = java database connection template
>
> orm = object relation model

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225489.png)

## 1 . 使用JDBC模板操作数据库

### 1.1. 建表建库

```mysql
create database spring;
user spring;
 
CREATE TABLE `employee`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `money` double(255, 0) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 10 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of employee
-- ----------------------------
INSERT INTO `employee` VALUES (1, '马云', 3000);
INSERT INTO `employee` VALUES (2, '马化腾', 3000);
```

### 1.2. 新建java项目

> 略

### 1.3. 导入jar包

可以自行添加个lombok

maven形式的：

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jcl</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.3.8</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.3.8</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.20</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

### 1.4. 编写实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(fluent = false,chain = true)
@Builder
public class Employee implements Serializable {

    private Integer id;

    private String name;

    private Double money;
}
```

### 1.4. 编写测试类 

```java
public class JdbcTest {

    /**
     * 使用Spring JDBCTemplate模板操作数据库
     */
    @Test
    public void test1() {
        //这里使用spring自带数据源,性能不好故只做测试
        DriverManagerDataSource driverManagerDataSource = new DriverManagerDataSource();
        driverManagerDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        driverManagerDataSource.setUrl("jdbc:mysql://localhost:3306/spring");
        driverManagerDataSource.setUsername("root");
        driverManagerDataSource.setPassword("root");
        //创建spring jdbcTemplate
        JdbcTemplate jdbcTemplate = new JdbcTemplate(driverManagerDataSource);

        //操作 注意 增删改 都使用update方法   和dbutils一样
        String sql = "insert into employee(name,money) values(?,?) ";
        int affect = jdbcTemplate.update(sql, "小明", 1000);
        if (affect == 1) {
            System.out.println("插入成功");
        }
    }
}
```

junit 4的依赖：

```xml
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
```

### 1.5. 测试

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225490.png)

### 1.6. 代码优化

* **创建对象交给Spring管理 数据库连接信息抽出**
* **使用Spring集成junit编写测试类**
* **引入DruidDataSource数据源**

#### 1.6.1. 创建数据库配置

Druid的依赖：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.6</version>
</dependency>
```

db.prpperties配置文件

```properties
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring?characterEncoding=utf8
jdbc.username=root
jdbc.password=root
```

#### 1.6.2. 编写Spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--属性占位符加载配置文件-->
    <context:property-placeholder location="classpath:db.properties"/>

    <!--生成Druid数据源对象-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!--生成jdbcTemplate对象-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

</beans>
```

**拓展：加载配置文件的另外一种方式**

用的是Spring框架中属性占位符配置器这个类（新版本已过时）：

```xml
  <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="db.properties"/>
  </bean>
```

#### 1.6.3. 编写Spring的测试类

这里，可以看到我们使用注解来配置测试类，并没有配置包扫描，所以包扫描的作用是发现需要添加到容器的类，并将其对象生成添加到容器中。

```java
@RunWith(value = SpringRunner.class)
@ContextConfiguration(locations = "classpath:application.xml")
public class SprintDemo {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void fun() {
        int updateRow = 
            jdbcTemplate.update("insert into employee(name,money) values(?,?)",
                "李彦宏", 4000);
        if (updateRow > 0) {
            System.out.println("添加成功");
        }
    }

}
```

**注意：**如果将测试类放置在test目录下（非main目录下）,虽然编译后的文件分别在classes,test-classes中，但也能读取到配置文件。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225491.png)

#### 1.6.4. 关于数据源的说明

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225492.png)

### 1.7.dao层的编写  

> jdbcTemplate只是一个模板工具   当然这个模板工具可以在dao中直接引入进行编写代码 但是我们开发有规范 我们应该统一管理  所以 使用jdbcDaoSupport这个类来编写dao层 
>
> 即可以直接用JdbcTemplate来操作，但更优的是用JdbcDaoSupport来操作

#### 1.7.1. 编写dao

```java
public interface EmployeeDao {

    //增
    int insertEmployee(Employee employee);

    //删
    int deleteEmployee(Integer id);

    //改
    int updateEmployee(Employee employee);

    // 查询一个具体的字段,这里是查名字，当然一般还都会有个查总数的接口
    String selectNameById(Integer id);

    // 查询一个记录
    Employee selectEmployeeById(Integer id);

    // 查询所有
    List<Employee> selectAll();
    
}
```

#### 1.7.2. 编写dao实现类

```java
/**
 * 使用dao规范 实现类继承 JdbcDaoSupport，注意jdbcTemlate中的方法名具有迷惑性，如要使用可看文档确认
 */
public class EmployeeDaoImpl extends JdbcDaoSupport implements EmployeeDao {
    @Override
    public int insertEmployee(Employee employee) {
        String sql = "insert into Employee (name,money) values(?,?)";
        int affect = 
            super.getJdbcTemplate().update(sql, employee.getName(), employee.getMoney());
        return affect;
    }

    @Override
    public int deleteEmployee(Integer id) {
        String sql = "delete from Employee where id = ?";
        int affect = super.getJdbcTemplate().update(sql, id);
        return affect;
    }

    @Override
    public int updateEmployee(Employee employee) {
        String sql = "update Employee set name = ?,money = ? where id = ?";
        int affect = super.getJdbcTemplate().update(sql, employee.getName(), employee.getMoney(),employee.getId());
        return affect;
    }

    /**
     * 查询方法是queryForObject()
     * 查询一个字段时用的时返回值类型.class
     */
    @Override
    public String selectNameById(Integer id) {
        String sql = "select name from employee where id = ?";
        String name = super.getJdbcTemplate().queryForObject(sql, String.class, id);
        return name;
    }

    /**
     * 当查询一个时 我们使用BeanPropertyRowMapper,查询方法是queryForObject()
     * 使用BeanPropertyRowMapper 要求实体类的属性和列名一致
     */
    @Override
    public Employee selectEmployeeById(Integer id) {
        String sql = "select * from employee where id = ?";
        Employee employee = getJdbcTemplate().queryForObject(sql, new BeanPropertyRowMapper<>(Employee.class), id);
        return employee;
    }

    /**
     * 当查询多个时 我们也要使用BeanPropertyRowMapper,并且查询方法是query()
     * 使用BeanPropertyRowMapper 要求实体类的属性和列名一致
     * 
     */
    @Override
    public List<Employee> selectAll() {
        String sql = "select * from employee";
        List<Employee> employees = getJdbcTemplate().query(sql, new BeanPropertyRowMapper<>(Employee.class));
        return employees;
    }
}
```

#### 1.7.3. 修改配置类

因为jdbcDaoSupport类中的方法new 了一个jdbcTemplate，故不需要在xml中生成对象放到容器中

```xml
    <!--属性占位符加载配置文件-->
    <context:property-placeholder location="classpath:db.properties"/>

    <!--生成Druid数据源对象-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!--这里dao层类继承jdbcDaoSupport，所以有dataSource属性，jdbcTemplate属性-->
    <!--且属性是以setXxx作判断依据,不是以成员变量来定的-->
    <bean id="employeeDao" class="com.xyz.code.dao.impl.EmployeeDaoImpl">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

#### 1.7.4. 测试类

这里只选择一个方法测试，如有需要可多试几个

```java
@RunWith(value = SpringRunner.class)
@ContextConfiguration(locations = "classpath:application.xml")
public class EmployeeDaoTest {

    @Autowired
    private EmployeeDao employeeDao;

    @Test
    public void test1(){
        Employee employee = new Employee().setMoney(100D).setName("测试1");
        employeeDao.insertEmployee(employee);
    }
} 
```

**注意：**如果想要批量新增、删除、修改，可以调用JdbcTemplate.batchUpdate()方法。

## 2.Spring对事务的支持

### 2.1. 事务的回顾

#### 2.1.1. 什么是事务transaction

> 事务是数据库操作最基本单元，数据库的多个操作可以当做一个整体，这个整体要么同时成功，要么同时失败
>
> 事务分为 编程式事务管理 和 声明式事务管理，spring经常使用的是声明式事务管理，即xml配置或注解完成事务管理

#### 2.1.2. 事务的特性

* 原子性：事务不能再分割 atomicity
* 一致性：从某个一致性状态变成另一个一致性状态，比如转账前后 总金额不变 consistence
* 隔离性： 各个事务之间 相互隔离 互不影响 isolation
* 持久性： 一旦事务提交成功 数据将持久化硬盘上  duration

#### 2.1.3. 安全性问题

* 脏读：表示一个未提交的事务读到了另外一个未提交的事务中的数据 
* 不可重复读： 表示一个未提交事务读取到了另外一个提交事务中修改的数据(update)
* 幻读：  表示一个未提交事务读取到了另外一个提交事务中新增或删除的数据(insert,delete)

#### 2.1.4.  数据库隔离级别

* read UnCommitted ： 脏读  不可重复读  幻读 都可能发生
* read Committed : 脏读不会发生  不可重复读  幻读 有可能发生
* Repeatable Read：脏读 不可重复读 不会发生  幻读 有可能发生 
* SERIALIZABLE:    解决所有问题

### 2.2. 问题演示 

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225493.png)

### 2.3. Spring事务的一些概念

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225494.png)

* **PlatformTransactionManager** 

  > spring要管理事务，必须使用事务管理器进行事务配置，**必须配置事务管理器**。这是一个接口
  >
  > 我们使用 DataSourceTransactionManager这个实体类间接实现了接口

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225495.png)

* **TransactionStatus**

  > 这个接口表示事务的状态 例如：是否有保存点，事务是否完成等等

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225496.png)

* **TransactionDefinition**

  > 这个接口表示事务详情 事务详情定义（事务定义、事务属性），spring用于确定事务具体详情，
  >
  > 例如：隔离级别、是否只读、超时时间 等
  >
  > 进行事务配置时，**必须配置详情**。spring将配置项封装到该对象实例。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225497.png)

### 2.4. 传播行为（不要掌握）

> 表示2个或者多个业务之间如何共享事务  有七种取值分别对应的常量是(0,1,2,3,4,5,6) 
>
> 说白了 当`事务方法`被另一个`事务方法`调用时，必须指定事务应该如何传播 
>
> 比如 方法A有事务支持  方法B也有事务支持    在A方法中调用B方法时  B方法的事务 是用A的事务还是自己再弄一个新的事务      
>
> **传播行为不要掌握 会配置就行了**  
>
> **事务方法：** 对数据库进行变化操作的方法就是事务方法

```java
  /**
  			前提：A调用B，B有事务注解@Transactional，并且子(B)会决定父(A)的报错或回滚动作
  			有事务时当发生异常会回滚
  			
        PROPAGATION_REQUIRED , required , 必须
        支持当前事务，A如果有事务，B将使用该事务。
        如果A没有事务，B将创建一个新的事务。

        PROPAGATION_SUPPORTS ，supports ，支持
        支持当前事务，A如果有事务，B将使用该事务。
        如果A没有事务，B将以非事务执行。

        PROPAGATION_MANDATORY，mandatory ，强制
        支持当前事务，A如果有事务，B将使用该事务。
        如果A没有事务，B将抛异常。相当于B方法就是个异常抛出

        PROPAGATION_REQUIRES_NEW ， requires_new ，必须新的
        如果A有事务，将A的事务挂起，B创建一个新的事务
        如果A没有事务，B创建一个新的事务

        PROPAGATION_NOT_SUPPORTED ，not_supported ,不支持
        如果A有事务，将A的事务挂起，B将以非事务执行
        如果A没有事务，B将以非事务执行

        PROPAGATION_NEVER ，never，从不
        如果A有事务，B将抛异常
        如果A没有事务，B将以非事务执行
     
        PROPAGATION_NESTED ，nested ，嵌套   nested = 巢居，嵌套
        A和B底层采用保存点机制，形成嵌套事务。
        如果A有事务，则B会是嵌套事务。
        如果A没有事务，则B会创建一个新的事务。
        第一种情况：相当于A的大圆包含B的小圆，A如果不存在事务了B也就不存在事务了，就是外层方法因异常回滚则内层方法也会回滚。
     */

```

### 2.5. Spring事务操作事务

#### 2.5.1. 添加Service层

> 由于事务是 执行操作之前开启 操作之后提交 或者 回滚 一般放在Service层处理

```java
// 编写Service接口 
public interface EmployeeService {
    /**
     * 转账
     */
    void transfer(Integer fromId, Integer toId, Double money);
}


//编写实现类
public class EmployeeServiceImpl implements EmployeeService {

    @Autowired // 这里注解与配置文件搭配，进行di
    private EmployeeDao employeeDao;

    @Override
    public void transfer(Integer fromId, Integer toId, Double money) {
        
        //修改前先查找 根据id 找到员工
        Employee employee = employeeDao.selectEmployeeById(fromId);
        Employee employee1 = employeeDao.selectEmployeeById(toId);
        
        //修改金额
        employee.setMoney(employee.getMoney()-money);
        employee1.setMoney(employee1.getMoney()+money);

        //修改数据库进行转账
        employeeDao.updateEmployee(employee);
        int i=10/0; //模拟异常
        employeeDao.updateEmployee(employee1);
    }
}
```

#### 2.5.2. 修改配置文件

```xml
<bean id="employeeService" class="com.xyz.code.service.impl.EmployeeServiceImpl"></bean>
```

#### 2.5.3. 事务模板(了解)

* **编写配置文件**

  ```xml
      <!--1配置事务管理器-->
      <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"></property>
      </bean>
      <!--2配置事务模板-->
      <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
          <property name="transactionManager" ref="transactionManager"></property>
      </bean>
  ```

* **修改UserServiceImpl**

  ```java
  public class EmployeeServiceImpl implements EmployeeService {
  
      @Autowired
      private EmployeeDao employeeDao;
      @Autowired
      private TransactionTemplate transactionTemplate;
  
      @Override
      public void transfer(Integer fromId, Integer toId, Double money) {
  
          //根据id 找到员工
          Employee employee = employeeDao.selectEmployeeById(fromId);
          Employee employee1 = employeeDao.selectEmployeeById(toId);
  
          //修改金额
          employee.setMoney(employee.getMoney()-money);
          employee1.setMoney(employee1.getMoney()+money);
  
          /**
           * 将下面代码有transactionTemplate进行事务处理
           */
          transactionTemplate.execute(new TransactionCallbackWithoutResult() {
              @Override
              protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                  //修改数据库进行转账
                  employeeDao.updateEmployee(employee);
                  int i=10/0; //模拟异常
                  employeeDao.updateEmployee(employee1);
              }
          });
      }
  }
  ```

* **测试类**

  ```java
  @RunWith(value = SpringRunner.class)
  @ContextConfiguration(locations = "classpath:application.xml")
  public class SpringServiceTest {
    @Autowired
      EmployeeService employeeService;
  
      @Test
      public void test1(){
          employeeService.transfer(1,2,300D); // Double类型的money
      }
  }
  ```
  

#### 2.5.4. aop声明式事务之xml的配置（重点）

* **引入aspectj.jar** 

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.7</version>
</dependency>
```

* **删除EmployeeServiceImpl中的模板信息**

```java
public class EmployeeServiceImpl implements EmployeeService {

    @Autowired
    private EmployeeDao employeeDao;

    @Override
    public void transfer(Integer fromId, Integer toId, Double money) {

        //根据id 找到员工
        Employee employee = employeeDao.selectEmployeeById(fromId);
        Employee employee1 = employeeDao.selectEmployeeById(toId);

        //修改金额
        employee.setMoney(employee.getMoney()-money);
        employee1.setMoney(employee1.getMoney()+money);

        //修改数据库进行转账
        employeeDao.updateEmployee(employee);
        int i=10/0; //模拟异常
        employeeDao.updateEmployee(employee1);

    }
}
```

* **编写配置文件**

```xml
        <!--1配置事务管理器-->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
            <property name="dataSource" ref="dataSource"></property>
        </bean>
        <!--2配置事务通知，注意约束是tx结尾的-->
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="transfer"/>
            </tx:attributes>
        </tx:advice>
        <!--3aop配置,advisor通知器经常用于实现方法拦截的接口或事务处-->
        <aop:config>
            <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.xyz.code.service.*.*(..))"/>
        </aop:config> 
```

* **测试**
没有问题

#### 2.5.5. aop声明式事务之注解（极重点）

* **修改配置文件**

```xml
        <!--1配置事务管理器-->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
            <property name="dataSource" ref="dataSource"></property>
        </bean>
        <!--2配置事务注解驱动，用注解解决事务问题-->
        <tx:annotation-driven transaction-manager="transactionManager"/>
```

* **再需要的事务地方添加注解**

   ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042225498.png)

### 2.6 @Transactioal注解的属性

```properties
propagation 设置传播行为 默认为Propagation.REQUIRED
isolation 隔离级别 默认为Isolation.DEFAULT，使用数据库默认的事物隔离级别，mysql是可重复读级别的
timout 超时时间 事务需要在时间内提交，否则进行回滚，默认为-1无限长
readonly 是否只读 设置true时，只能查询
rollbackFor 什么情况回滚 设置出现哪些异常时进行事务回滚,默认情况下是运行时异常才回滚，当设置为Exception时可回滚非运行时异常。
noRollBackFor 什么情况不回滚 设置出现哪些异常不进行回滚
```

### 2.7 完全注解声明式事务管理

```java
import javax.sql.DataSource;

Configuration // 代表XML配置文件
@ComponentScan(basePackages = {"com.aitx.study"}) // 代替context:component-scan标签
@EnableTransactionManagement // 代替tx:annotation-driven
public class MyConfig {

    /**
     * 代替bean标签，创建数据源对象
     */
    @Bean
    public DruidDataSource getDruidDataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/spring?characterEncoding=utf8&useSSL=false");
        dataSource.setUsername("root");
        dataSource.setPassword("admin123456");
        return dataSource;
    }

    /**
     * 创建transactionManager，注意形参位置可以引入容器中注册的bean
     */
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource) {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }

    /**
     * 创建EmployeeDao，这里没有在EmployeeDaoImpl类上加@Repository，是因为要给继承的属性注入值
     */
    @Bean
    public EmployeeDao getEmployeeDao(DataSource dataSource) {
        EmployeeDaoImpl employeeDao = new EmployeeDaoImpl();
        employeeDao.setDataSource(dataSource);
        return employeeDao;
    }

}
```

**注意：**测试类要将原配置文件修改成配置类;还要注意如果dao层继承JdbcDaoSupport还要注入dataSource属性

## 3.spring框架的新功能

### 3.1 spring使用log4j2

> Spring 5.0 框架自带了通用的日志封装,Spring5 已经移除 Log4jConfigListener，官方建议使用 Log4j2并且Spring5 框架整合 Log4j2

第一步：引入依赖

```xml
<!--注意当前log4j2版本有漏洞-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.11.2</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.11.2</version>
</dependency>
<!---log4j与slf-4j桥接-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.11.2</version>
</dependency>
<!--简单的日志门面simple log facade-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.30</version>
</dependency>
```

第二步：在resources文件夹下添加log4j2.xml配置文件

这里将日志打印到console控制台上：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration 后面的 status 用于设置 log4j2 自身内部的信息输出，可以不设置，
当设置成 trace 时，可以看到 log4j2 内部各种详细输出-->
<configuration status="INFO">
    <!--先定义所有的 appender,appender=附加器，输出源，输出目的地-->
    <appenders>
        <!--输出日志信息到控制台-->
        <console name="Console" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-
5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
    <!--然后定义 logger，只有定义 logger 并引入的 appender，appender 才会生效-->
    <!--root：用于指定项目的根日志，如果没有单独指定 Logger，则会使用 root 作为默认的日志输出-->
    <loggers>
        <root level="info">
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```

第三步：如果项目引入了lombok可以在组件上加上@Slf4j注解,使用log对象打印日志

```java
@RunWith(value = SpringRunner.class)
@ContextConfiguration(classes = {MyConfig.class})
@Slf4j
public class JdbcTest {

    @Autowired
    private EmployeeService employeeService;

    @Test
    public void test3() {
        log.info("123");
        employeeService.transfer(1, 2, 1000D);
    }

}
```

### 3.2 @Nullable可空注解

@Nullable 注解可以使用在方法上面，属性上面，参数上面，表示方法返回可以为空，属性值可以为空，参数值可以为空

注意：import org.springframework.lang.Nullable;

### 3.3 GenericApplicationContext函数式注册对象

```java
@RunWith(value = SpringRunner.class)
@ContextConfiguration(classes = {MyConfig.class})
@Slf4j
public class JdbcTest {

    @Test
    public void test4() {
        // 1 创建GenericApplicationContext对象
        GenericApplicationContext applicationContext = new GenericApplicationContext();
        // 2 调用genericApplicationContext注册对象
        applicationContext.refresh();
        applicationContext.registerBean("user", User.class, User::new);
        // 3 获取ioc容器中的对象
        User user = applicationContext.getBean("user", User.class);
        System.out.println("user = " + user);
    }

}
```

相当于，我们可能使用这个对象来创建任意要注册到ioc容器的对象。其中对于refresh()方法有以下解释

```properties
Refresh(): 加载或刷新配置的持久表示，它可能来自基于 Java 的配置、XML 文件、属性文件、关系数据库模式或其他格式。由于这是一种启动方法，它应该在失败时销毁已经创建的单例，以避免悬空资源。换句话说，在调用此方法之后，应该实例化所有或根本不实例化单例。
```

### 3.4 整合Junit5

第一步：添加依赖

```xml
<!--并不需要J-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>RELEASE</version>
    <scope>test</scope>
</dependency>
```

第二步：编写测试类

```java
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;

@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = {MyConfig.class})
@Slf4j
public class Junit5Test {

    @Autowired
    private EmployeeService employeeService;

    @Test
    public void testTransaction(){
        log.info("testTransaction");
        employeeService.transfer(1,2,1000D);
    }
}
```

当然上面的@ExtendWith注解与@ContextConfiguration注解可以合并成一个复合注解@SpringJUnitConfig

```java
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

@SpringJUnitConfig(classes = {MyConfig.class})
@Slf4j
public class Junit5Test {

    @Autowired
    private EmployeeService employeeService;

    @Test
    public void testTransaction(){
        log.info("testTransaction");
        employeeService.transfer(1,2,1000D);
    }
}
```

## 4.常见异常：

4.1数据库修改数据时中文字符为？号，这里要将数据源的连接的url设置为

```properties
jdbc:mysql://localhost:3306/spring?characterEncoding=utf8
```

4.2单元测试引入SpringRunner失败问题

删掉spring-test的scope作用域（即变成默认的compile作用域），然后项目右键Maven》update projects。

4.3使用jdbcDaoSupport时出现下面异常 ,说dao层实现类需要jdbcTemplate或dataSource;只需在该类注入属性就行

```properties
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'employeeDao' defined in class path resource [application.xml]: Invocation of init method failed; nested exception is java.lang.IllegalArgumentException: 'dataSource' or 'jdbcTemplate' is required
```

4.4 连接数据库时可能报下面的异常

```properties
Java报错javax.net.ssl.SSLException MESSAGE: closing inbound before receiving peer‘s close_notify
```

解决办法：在配置文件中数据库连接的**url属性**中加入`useSSL=false`即可解,注意SSL需要大写

```properties
jdbc:mysql://localhost:3306/spring?useSSL=false
```

