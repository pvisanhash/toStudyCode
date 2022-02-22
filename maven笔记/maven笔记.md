# Maven

* **问题描述**

  * **我们项目中用的jar包，需要先下载再导入 再依赖** 
  * **jar包之间的依赖关系 程序员需要明确 否则报错 比如 jar包A 依赖于jar包B 只导入jar包A 程序报错**
  * **我们的项目都在一个大的工程中 所有的代码都写在了一个工程中 这样功能做不到抽取  更做不到分模块开发**
  * **在实际开发中 手动导入jar包 可能存在jar包之间的版本兼容问题  特别是依赖包 ** 

  ![](images/QQ图片20200212124917.png)

  

## 1. 什么是Maven

> Maven是一个采用纯Java编写的开源项目管理工具, Maven采用了一种被称之为Project Object Model (POM)概念来管理项目，所有的项目配置信息都被定义在一个叫做POM.xml的文件中, 通过该文件Maven可以管理项目的整个生命周期，包括清除、编译，测试，报告、打包、部署等等。目前Apache下绝大多数项目都已经采用Maven进行管理. 而Maven本身还支持多种插件, 可以方便更灵活的控制项目, 开发人员的主要任务应该是关注商业逻辑并去实现它, 而不是把时间浪费在学习如何在不同的环境中去依赖jar包,项目部署等。Maven正是为了将开发人员从这些任务中解脱出来而诞生的。i

**总结: Maven是一个采用纯Java编写的开源项目管理工具 **

## 2. 为什么使用Maven

- 解决导入jar包的问题  版本冲突等问题
- 解决项目构建问题 

## 3. Maven的作用

* 依赖管理
* 一键构建
* 管理项目信息

### 3.1. 依赖管理

> 依赖管理 分为依赖和管理     
>
> 依赖： 就是我们导入jar
>
> 管理：一般是jar包的版本管理

* **直接依赖**

* **间接依赖**

  ![](images/QQ图片20200212130848.png)
  
  Maven 可以把 jar 包所依赖的其它 jar 包自动下载并引入项目。

### 3.2. 一键构建

> 一键构建 表示项目从开发到部署 都是通过maven命令完成   依赖声明周期

### 3.3.管理项目信息

管理项目信息其实就是生成一个站点文档，一个命令就可以解决

## 4. Maven的下载安装

#### 4.1下载与安装

1、 确保安装了java 环境:maven 本身就是 java 写的，所以要求必须安装 JDK。 查看 java 环境变量：echo %JAVA_HOME% 或java -version

2、 下载并解压 maven 安装程序： 

官网：https://maven.apache.org/

![](./images/mavendowm.png)

​	**注意点：下载下来之后 是个压缩包  解压时 路径不要有中文** 

3、 配置 Maven 的环境变量： 

MAVEN_HOME=d:/apache-maven-3.3.9 或者 M2_HOME=d:/apache-maven-3.3.9 

path=%MAVEN_HOME%/bin; 或者%M2_HOME%/bin; 

新版本用Maven_HOME

4、验证是否安装成功: 

```bash
mvn –v 
```

如果不生效，需要将绝对路径添加到Path中

#### 4.2. maven自身文件夹介绍

- bin  ： 命令文件夹
- boot ：  启动器 （引导程序）
- conf :  maven配置文件
- lib: maven需要的jar包 

![](./images/maveninfo.png)

## 5. Maven的核心概念

Maven能够实现自动化构建是和它的内部原理分不开的，这里我们从 Maven的九个核心概念入手， 看看Maven是如何实现自动化构建的

①POM  

②约定的目录结构 

③坐标 

④依赖管理 

⑤仓库管理 

⑥生命周期 

⑦插件和目标 

⑧继承 

⑨聚合 

### 5.1 POM

POM 即 Project Object Model 项目对象模型。Maven 把一个项目的结构和内容抽象成一个模型，在 xml 文件进行声明，以方便进行构建和描述，pom.xml 是 Maven 的灵魂。所以，maven 环境搭建好之后，所有的学习和操作都是关于 pom.xml 的。 

**pom.xml 初识：**

| 基本信息：   |                                                              |
| ------------ | ------------------------------------------------------------ |
| modelVersion | Maven模型版本。对于 Maven2 和 Maven3 来说，它只能是 4.0.0，一般不修改 |
| groupId      | 组织 id，一般是公司域名的倒写。 格式可以为： <br />1. 域名倒写。 例如 com.baidu<br />2. 域名倒写+项目名。例如 com.baidu.appolo |
| artifactId   | 制品id，项目id，也是模块id，对应 groupId中项目中的子项目。   |
| version      | 版本号。如果项目还在开发中，是不稳定版本， 通常在版本后带-SNAPSHOT。<br />version 使用三位数字标识，例如 1.1.0 |
| packaging    | 项目打包的类型，可以使 jar、war、rar、ear、pom，默认是 jar   |

groupId 、 artifactId 、 version 三个元素生成了一个 Maven 项目的基 本坐标，在众多的 maven 项目中可以唯一定位到某一个项目。坐标也决定着将来项目在仓库中的路径 及 名称。 

比如groupId是com.xyz.code，artifactId是myFisrtProject，version是1.0.0-SNAPSHOT，则在本地仓库中的文件夹是/com/xyz/code/myFirstProject/1.0.0-SNAPSHOT。

| 依赖：                     |                                                              |
| -------------------------- | ------------------------------------------------------------ |
| dependencies 和 dependency | Maven 的一个重要作用就是管理 jar 包，为了一个项目可以构建或运行，项目中不可避免的，会依赖很多其他的 jar 包，在 Maven 中，这些 jar 就被称为依赖，使用标dependency 来配置。而这种依赖的配置正是通过坐标来，由此我们也不难看出，maven 把所有的 jar 包也都视为项目存在了。 |

| 配置属性： |                                                              |
| ---------- | ------------------------------------------------------------ |
| properties | properties 是用来定义一些配置属性的，例如project.build.sourceEncoding（项目构建源码编码方式），可以设置为UTF-8，防止中文乱码，也可定义相关构建版本号，便于日后统一升级。 |

| 构建： |                                                         |
| ------ | ------------------------------------------------------- |
| build  | build 表示与构建相关的配置，例如设置编译插件的 jdk 版本 |

| 继承： |                                                              |
| ------ | ------------------------------------------------------------ |
| parent | 指定当前pom的父是什么。在 Maven 中，如果多个模块都需要声明相同的配置，例如：groupId、artifactId、version、有相同的依赖、或者相同的组件配置等，也有类似 Java 的继承机制，用 parent 声明要继承的父工程的 pom 配置。 |

| 聚合：  |                                                              |
| ------- | ------------------------------------------------------------ |
| modules | 在 Maven 的多模块开发中，为了统一构建整个项目的所有模块，可以提供一个额外的模块，该模块打包方式为 pom，并且在其中使用 modules 聚合的，其它模块，这样通过本模块就可以一键自动识别模块间的依赖关系来构建所有模块，叫 Maven 的聚合。 |

### 5.2 约定的目录结构 

#### 5.2.1 概念

maven 中约定的目录结构： 

src与pom.xml是同级，main与test是同级，java与resources是同级

```html
Hello 

	|---src 

	|---|---main 

	|---|---|---java 

	|---|---|---resources 

	|---|---test 

	|---|---|---java 

	|---|---|---resources 

	|---pom.xml 
```

说明：Hello:工程根目录，也就是工程名 

 src：源代码 

 	main：主程序 

​		 java：主程序的 java 源码 

​		resources：主程序的配置文件 test：测试程序 

​	test : 测试程序

​		 java：测试程序的 java 源码 

​		 resources：测试程序的配置文件 

 pom.xml：Maven 工程的核心配置文件。 

一般情况下，我们习惯上采取的措施是：约定>配置>编码 

maven 的 pom.xml 记录的关于构建项目的各个方面的设置，maven 从 pom.xml 文件开始，按照助约定的 工程目录编译，测试，打包，部署，发布项目。

#### 5.2.2 手动创建java的maven工程

按照如下步骤，实现第一个 maven 项目，以 maven 推荐的约定方式创建目录，类文件。 

-1.某个目录中创建文件夹 Hello 

-2.在 Hello 中创建子目录 src 

-3. 新建pom.xml 到 Hello 目录和 src 是同级放置的。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.xyz.code</groupId>
  <artifactId>Hello</artifactId>
  <version>1.0-SNAPSHOT</version>
</project>
```

-4.进入 src 目录，创建 main， test 目录

-5.进入 main 目录，创建 java，resources 目录。 

-6.进入 java 目录，创建目录 com/xyz/code

-7.在 com/xyz/code目录下创建 HelloMaven.java 文件，定义方法; 

```java
package com.xyz.code;
public class HelloMaven {
    public static void main(String args[]) {
        System.out.println("Hello Manven"); //也可以调用 addNumber()方法 
    }

    int addNumber(int n1, int n2) {
        return n1 + n2;
    }
}
```

7.进入到 `Hello目录下`，执行 `mvn compile`,会生成如下的目录结构  

![](./images/Snipaste_2021-11-06_14-17-41.png)

8.进入到 `target/classes目录下` 执行 `java com.xyz.HelloMaven`

### 5.3 坐标（gav）

Maven 把任何一个插件（构件，jar包）都作为仓库中的一个项目进行管理，用一组(三个)向量组成的坐标来表示。坐标在仓库 中可以唯一定位一个 Maven 项目。 

groupId：组织名，通常是公司或组织域名倒序+项目名 

artifactId：模块名，通常是工程名 

version：版本号 

需要特别指出的是，项目在仓库中的位置是由坐标来决定的：groupId、artifactId 和 version 决定项目在仓库中 

的路径，artifactId 和 version 决定 jar 包的名称。 

![](images/Snipaste_2021-11-06_15-17-48.png)

### 5.4 依赖管理

一个 Maven 项目正常运行需要其它项目的支持，Maven 会根据坐标自动到本地仓库中进行查找。 对于程序员自己的 Maven 项目需要进行安装，才能保存到仓库中。

不用 maven 的时候所有的 jar 都不是你的，需要去各个地方下载拷贝，用了 maven 所有的 jar 包都是你的，想 

要谁，叫谁的名字就行。maven 帮你下载。 

pom.xml 加入依赖的方式：

```xml
   <dependencies>
        <!--log4j 日志依赖-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <!--junit 单元测试依赖-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
    </dependencies>
```

### 5.5 仓库管理

#### 5.5.1 仓库的概念

现在我们对maven工程有一个大概的认识了，那现在思考一个问题，maven怎么就这么神奇，我们写完的工 程交给他之后，他就能够自动帮我们管理，我们依赖的jar包它从哪儿获取呢？有同学说已经安装了，在它的安装包里啊，大家可以看一下maven下载下来才8M，我们需要的jar包有时候都几百兆甚至几个G，它从哪儿弄去呢？其实，maven有仓库的概念。在Maven中，任何一个依赖、插件或者项目构建的输出，都可以称之为`构件`。Maven核心程序仅仅定义了`自动化构建`项目的`生命周期`，但具体的构建工作是由特定的构件完成的。而且为了提高构建的效率和构件复用，maven把所有的构件统一存储在某一个位置，这个位置就叫做`仓库`。 

#### 5.5.2 仓库存什么

仓库是存放东西的，Maven 仓库的是：

1. Maven 的插件，插件也是一些 jar，这些 jar 可以完成一定的功能。
2. 我们自己开发项目的模块 
3.  第三方框架或工具的 jar 包

#### 5.5.3 仓库的类别

根据仓库存储的位置，把仓库分为本地仓库和远程仓库。 

**本地仓库**，存在于当前电脑上,默认存放在~ .m2\repository中,为本机上所有的Maven工程服务。你也可以通过Maven的配置文件Maven_home/conf/settings.xml中修改本地仓库所在的目录。 

~ 是用户的主目录，windows系统中是 c：/user/登录系统的用户名 

**远程仓库**，分为 为全世界范围内的开发人员提供服务的中央仓库、为全世界范围内某些特定的用户提供服务的 

中央仓库镜像、为本公司提供服务自己架设的私服。



中央仓库是maven默认的远程仓库，其地址 是:https://repo.maven.apache.org/maven2/

中央仓库，包含了绝大多数流行的开源Java构件，以及源码、作者信息、许可证信息等。一般来说，简单的 Java项目依赖的构件都可以在这里下载得到。 

中央仓库镜像是中央仓的镜像仓库。

私服是一种特殊的远程仓库，它是架设在局域网内的仓库服务，私服代理广域网上的远程仓库，供局域网内的 Maven用户使用。当Maven需要下载构件的时候，它从私服请求，如果私服上不存在该构件，则从外部的远程仓库下载，缓存在私服上之后，再为Maven的下载请求提供服务。我们还可以把一些无法从外部仓库下载到的构件上传到私服上。 



分类说明： 

1）本地仓库：本机当前电脑上的资源存储位置，为本机上所有 Maven工程提供服务 

2）远程仓库：不在本机上， 通过网络才能使用。多电脑共享使用的。 

①：中央仓库：通过Internet访问，为全世界所有 Maven工程服务。 最权威的。 

②：中央仓库的镜像：架设在不同位置，欧洲，美洲，亚洲等每个洲都有若干的服务器，为中央仓库分担流量。减轻中央仓库的访问，下载的压力。所在洲的用户首先访问的是本洲的镜像服务器。 

③：私服：在局域网环境中部署的服务器，为当前局域网范围内的所有 Maven工程服务。公司中常常使用这种方式。

![](images/Snipaste_2021-11-06_13-13-58.png)

#### 5.5.4 maven对仓库的使用

在 Maven 构建项目的过程中如果需要某些插件，首先会到 Maven 的本地仓库中查找，如果找到则可以直接使用；如果找不到，它会自动连接外网，到远程中央仓库中查找；如果远程仓库中能找到，则先把所需要的插件下载到本地仓库，然后再使用，并且下次再用到相同的插件也可以直接使用本地仓库的；如果没有外网或者远程仓库中也找不到，则构建失败。

> 1->    jar包 先从本地仓库找    如果本地仓库有  则不再往下找  
>
> 2->   jar包 如果本地仓库没有 则从远程仓库（私服）找   如果有 则不会再往下找 
>
> 3->    jar包 如果远程仓库（私服）没有  则会从中央仓库镜像找  
>
> 4->  jar包 如果中央仓库镜像没有，则从中央仓库找（百分之99的都能找到 ）

资源搜索地址：https://mvnrepository.com/

#### 5.5.6 配置本地仓库

![](./images/1.png)

### 5.6 生命周期

#### 5.6.1 介绍

对项目的构建是建立在生命周期模型上的，它明确定义项目生命周期各个阶段，并且对于每一个阶段提供相对应的命令，对开发者而言仅仅需要掌握一小堆的命令就可以完成项目各个阶段的构建工作。 

构建项目时按照生命周期顺序构建，每一个阶段都有特定的插件来完成。不论现在要执行生命周期中的哪个阶段，都是从这个生命周期的最初阶段开始的。对于我们程序员而言，无论我们要进行哪个阶段的构建，直接执行相应的命令即可，无需担心它前边阶段是 否构建，Maven 都会自动构建。这也就是 Maven 这种自动化构建工具给我们带来的好处。 

>  一个项目从编写代码开始->[清理clean]->编译[包括compile,test-compile]->测试->运行->打包->部署的过程

- 编码时期：程序员写代码

  > 无

- 清理： 清除已经编译好的class文件会删除原来编译和测试的目录，即 target 目录，但是已经 install 到仓库里的包不会删除

  > 命令是：clean

- 编译： 代码编写完成之后想变成class文件 ，针对于/src/main下的代码，会在当前项目目录下生成一个 target,里边存放编译主程序之后生成的字节码文件

  > 命令是：compile

- 测试编译：测试代码编写好，将测试代码编译，针对于/src/test下的代码，会在当前目录下生成一个 target,里边存放编译测试程序之后生成的字节码文件

  > 命令是：test-compile

- 测试： 上线之前想进行功能测试，主要是将编写好的单元测试跑一遍，会生成一个目录surefire-reports，保存测试结果

  > 命令是：test

- 运行： 项目运行 没啥说的

  > 命令是  run  

- 打包： java项目打成 jar ，web项目打成war   

  > 命令是：package

- 安装：在Maven环境下特指将打包的结果（生成java的包）安装到本地仓库 

  > 命令是：install 

- 部署： 会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中。 还会自动把项目部署到 web 容器中（上线到生产环境）

  > 命令是：deploy

**注意：执行以上命令必须在命令行进入 pom.xml 所在目录！**

![](images/QQ图片20200212132534.png)

![](images/Snipaste_2021-11-06_16-48-54.png)

#### 5.6.2 手动测试命令

以第一个 maven 项目为例，进入到 Hello 目录中执行 maven 各种命令 

-1.准备工作，pom.xml 添加依赖 

```xml
<dependencies> 
 <!-- 单元测试 --> 
	<dependency> 
		<groupId>junit</groupId> 
		<artifactId>junit</artifactId> 
		<version>4.11</version> 
	</dependency> 
</dependencies> 
```

-2.在 Hello/src/test/目录下创建 java ， resources 目录 

-3.在 Hello/src/test/java 目录下，创建 com/xyz/code目录 

-4.在 com/xyz/code/目录中创建 HelloMavenTest.java 文件导入类： 

```java
package com.xyz.code;

import org.junit.Assert;
import org.junit.Test;

public class HelloMavenTest {

    @Test
    public void testAddNumber() {
        System.out.println("执行 HelloMaven 类 addNumber()方法");
        int n1 = 10;
        int n2 = 20;
        int res = 0;
        HelloMaven helloMaven = new HelloMaven();
        res = helloMaven.addNumber(n1, n2);
        // 期望值，实际值
        Assert.assertEquals(30, res);
    }
}
```

-5.执行先执行 mvn compile ,观察目录结构的变化，生成 target 目录 ,发现并没有生成测试java程序的class

![](./images/Snipaste_2021-11-06_14-17-41.png)

-6.在执行 mvn clean， 观察 taget 目录被清除

 ![](images/Snipaste_2021-11-06_14-19-58.png)

-7.执行 mvn compile 

-8.进入 taget/classes 目录执行 java com.xyz.code.HelloMaven 。

-9.进入 Hello 目录，执行 mvn test-compile 生成 test-target 测试编译后的目录 。发现执行test-compile，也执行了comile命令，并多了generated-test-sources与test-classes目录

![](images/Snipaste_2021-11-06_14-21-20.png)

-10. 进入 Hello 目录，执行 mvn test 执行 HelloMavenTest 类中方法，生成测试报告 

![](images/Snipaste_2021-11-06_14-24-00.png)

-11.进入目录 surefire-reports，查看测试报告 

![](images/Snipaste_2021-11-06_14-25-00.png)

-12.修改 MyTest.java, 增加测试方法 

```java
    @Test
    public void testAddNumber2() {
        System.out.println("执行 HelloMaven 类 addNumber()方法");
        int n1 = 20;
        int n2 = 30;
        int res = 0;
        HelloMaven hello = new HelloMaven();
        res = hello.addNumber(n1, n2);
        // 期望值，实际值
        Assert.assertEquals(60, res);
    }
```

-13. 进入 Hello 目录，执行 mvn test-compile 

![](images/Snipaste_2021-11-06_14-29-36.png)

-14. 进入 Hello 目录，执行 mvn test ,可以看到测试不通过

![](images/Snipaste_2021-11-06_14-30-53.png)

-15. 修改 testAddNumber2()方法中 60 为 50 

-16. 进入 Hello 目录，执行 mvn package ，生成 xxx.jar 文件，这就是所说的打包 ,target目录下多了两个文件结构

![](images/Snipaste_2021-11-06_14-33-06.png)

-17. 进入 Hello 目录，执行 mvn install ， 把 xxx.jar 文件安装到本地 maven 仓库，安装成功后查看本地仓库中的 jar 文件 

![](images/Snipaste_2021-11-06_14-35-12.png)

![](images/Snipaste_2021-11-06_14-36-20.png)

![](images/Snipaste_2021-11-06_14-37-09.png)

### 5.7 插件和目标

#### 5.7.1 介绍

maven 构建生命周期，由 maven 的插件 plugin 来执行完成。目标指的是target，作用于哪部分，生成那部分文件，如compile作用于src/main下的文件，生成target下的classes等文件结构。

官网插件说明：http://maven.apache.org/plugins/

在项目根目录下执：mvn clean install

mvn clean install是会清除原先的jar包，重新生成jar包替换安装到本地仓库，可以看到jar包与其他文件更新了，修改时间变化了

![](images/Snipaste_2021-11-06_14-56-08.png)

![](images/Snipaste_2021-11-06_15-05-22.png)

**clean插件 maven-clean-plugin:2.5:clean**

clean 阶段是独立的一个阶段，功能就是清除工程目前下的 target 目录 

**resources插件 maven-resources-plugin:2.6:resources**

resource 插件的功能就是把项目需要的配置文件拷贝到指定的目当，默认是拷贝 src\main\resources 目录下的件到 target/classes 目录下 

**compile 插件  maven-compiler-plugin:3.1:compile** 

compile 插件执行时先调用 resouces 插件，功能就是把 src\mainjava 源码编译成字节码生成 class 文件，并把编译好 的 class 文件输出到 target\classes 目录下 

**test资源插件 maven-resources-plugin:2.6:testResources** 与**test编译插件 maven-compiler-plugin:3.1:testCompile**

单元测试所用的 compile 和 resources 插件和主代码是相同的，但执行的目标不行，目标 testCompile 和 testResources 是把 src\test\java 下的代码编译成字节码输出到 target\test-classes，同时把 src\test\resources 下的配置文件拷贝到 target\test-classes

 **测试报告插件 maven-surefire-plugin:2.12.4:test**

生成测试报告的

**package打包插件 maven-jar-plugin:2.4:jar** 

这个插件是把 class 文件、配置文件打成一个 jar(war 或其它格式)包 

**install安装插件 maven-install-plugin:2.4:install** 

安装插件的功能就是把构建好的 artifact 部署到本地仓库。此外还有一个 deploy 插件是将构建好的 artifact 部署到远程仓库 

#### 5.7.2 常用的插件设置

插件可以在自己的项目中设置，最常使用的是 maven 编译插件。设置项目使用的 jdk 版本时通过编译插件指定。 

`pom.xml` 文件`build标签`中设置。

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### 5.8 继承

### 5.9 聚合





## 6. maven的使用

### 6.1  指令的使用

> 需要把 maven包下的bin目录 配置到环境变量中   使用`mvc 指令`完成构建
>
> **这种方式已经过时  了解即可**

![](./images/QQ图片20190823173548.png)

### 6.2  和Idea集成

#### 6.2.1 步骤

> 配置全局Maven设置(不要设置当前工程的Maven) 

![](./images/QQ图片20190823173805.png)

![im](./images/QQ图片20190823173932.png)

**下面的设置一般不配也没关系，需要的时候可以配置**

![](images/Snipaste_2021-11-06_15-38-13.png)

**Delegate IDE build/run action to maven依情况选择，有时候不要选择**

maven项目创建时，会联网下载模版文件， 比较大， 使用-DarchetypeCatalog=internal，不用下载， 创建maven项目速度快。

```properties
-DarchetypeCatalog=internal
```

#### 6.2.2 我的maven配置文件

一般只要配置3个就行

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
 | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.conf}/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
<!--1 本地仓库设置-->    
<localRepository>E:\workSpace\maven\repository</localRepository>
  <!-- interactiveMode
   | This will determine whether maven prompts you when it needs input. If set to false,
   | maven will use a sensible default value, perhaps based on some other setting, for
   | the parameter in question.
   |
   | Default: true
  <interactiveMode>true</interactiveMode>
  -->

  <!-- offline
   | Determines whether maven should attempt to connect to the network when executing a build.
   | This will have an effect on artifact downloads, artifact deployment, and others.
   |
   | Default: false
  <offline>false</offline>
  -->

  <!-- pluginGroups
   | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
   |-->
  <pluginGroups>
    <!-- pluginGroup
     | Specifies a further group identifier to use for plugin lookup.
    <pluginGroup>com.your.plugins</pluginGroup>
    -->
  </pluginGroups>

  <!-- proxies
   | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>

  <!-- servers
   | This is a list of authentication profiles, keyed by the server-id used within the system.
   | Authentication profiles can be used whenever maven must make a connection to a remote server.
   |-->
  <servers>
    <!-- server
     | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
    <server>
      <id>deploymentRepo</id>
      <username>repouser</username>
      <password>repopwd</password>
    </server>
    -->

    <!-- Another sample, using keys to authenticate.
    <server>
      <id>siteServer</id>
      <privateKey>/path/to/private/key</privateKey>
      <passphrase>optional; leave empty if not used.</passphrase>
    </server>
    -->
  </servers>

  <!-- mirrors
   | This is a list of mirrors to be used in downloading artifacts from remote repositories.
   |
   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
   | However, this repository may have problems with heavy traffic at times, so people have mirrored
   | it to several places.
   |
   | That repository definition will have a unique id, so we can create a mirror reference for that
   | repository, to be used as an alternate download site. The mirror site will be the preferred
   | server for that repository.
   |-->
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
    <!--2 设置镜像-->  
	<mirror>
		<id>aliyunmaven</id>
		<mirrorOf>*</mirrorOf>
		<name>阿里云公共仓库</name>
		<url>https://maven.aliyun.com/repository/public</url>
	</mirror>
  </mirrors>

  <!-- profiles
   | This is a list of profiles which can be activated in a variety of ways, and which can modify
   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
   | specific paths and repository locations which allow the build to work in the local environment.
   |
   | For example, if you have an integration testing plugin - like cactus - that needs to know where
   | your Tomcat instance is installed, you can provide a variable here such that the variable is
   | dereferenced during the build process to configure the cactus plugin.
   |
   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
   | section of this document (settings.xml) - will be discussed later. Another way essentially
   | relies on the detection of a system property, either matching a particular value for the property,
   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
   | Finally, the list of active profiles can be specified directly from the command line.
   |
   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
   |       repositories, plugin repositories, and free-form properties to be used as configuration
   |       variables for plugins in the POM.
   |
   |-->
  <profiles>
    <!-- profile
     | Specifies a set of introductions to the build process, to be activated using one or more of the
     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
     | or the command line, profiles have to have an ID that is unique.
     |
     | An encouraged best practice for profile identification is to use a consistent naming convention
     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
     | This will make it more intuitive to understand what the set of introduced profiles is attempting
     | to accomplish, particularly when you only have a list of profile id's for debug.
     |
     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
    <profile>
      <id>jdk-1.4</id>

      <activation>
        <jdk>1.4</jdk>
      </activation>

      <repositories>
        <repository>
          <id>jdk14</id>
          <name>Repository for JDK 1.4 builds</name>
          <url>http://www.myhost.com/maven/jdk14</url>
          <layout>default</layout>
          <snapshotPolicy>always</snapshotPolicy>
        </repository>
      </repositories>
    </profile>
    -->

    <!--
     | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
     | might hypothetically look like:
     |
     | ...
     | <plugin>
     |   <groupId>org.myco.myplugins</groupId>
     |   <artifactId>myplugin</artifactId>
     |
     |   <configuration>
     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
     |   </configuration>
     | </plugin>
     | ...
     |
     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
     |       anything, you could just leave off the <value/> inside the activation-property.
     |
    <profile>
      <id>env-dev</id>

      <activation>
        <property>
          <name>target-env</name>
          <value>dev</value>
        </property>
      </activation>

      <properties>
        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
      </properties>
    </profile>
    -->
    <!--3 常用的profile配置-->  
	<profile>
		<id>jdk1.8</id>
		<activation>
			<activeByDefault>true</activeByDefault>
				<jdk>1.8</jdk>
			</activation>
		<properties>
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
			<maven.compiler.encoding>utf-8</maven.compiler.encoding>
		</properties>
	</profile>
  </profiles>

  <!-- activeProfiles
   | List of profiles that are active for all builds.
   |
  <activeProfiles>
    <activeProfile>alwaysActiveProfile</activeProfile>
    <activeProfile>anotherAlwaysActiveProfile</activeProfile>
  </activeProfiles>
  -->
</settings>

```

#### 6.2.3 pom的常用配置

##### 6.2.3.1 全局变量

在 Maven 的 pom.xml 文件中，`properties标签`用于定义全局变量，POM 中通过${property_name}的形式引用变量的值。 

定义全局变量：

```xml
    <properties>
        <spring.version>4.3.10.RELEASE</spring.version>
    </properties>
```

引用全局变量：

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
    <!--这里引用全局变量，当然也可以在子pom中引用父pom定义的全局变量-->
	<version>${spring.version}</version>
</dependency>
```

Maven 系统采用的变量：

```xml
    <properties>
        <!--源码编译 jdk 版本-->
        <maven.compiler.source>1.8</maven.compiler.source>
        <!--运行代码的 jdk 版本-->
        <maven.compiler.target>1.8</maven.compiler.target>
        <!--项目构建使用的编码，避免中文乱码-->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--生成报告的编码-->
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>
```

##### 6.2.3.2 指定java资源的位置

src/main/java 和 src/test/java 这两个目录中的所有*.java 文件会分别在 comile 和 test-comiple 阶段被编译，编 

译结果分别放到了 target/classes 和 targe/test-classes 目录中，但是这两个目录中的其他文件都会被忽略掉，如果需要把 src/目录下的其他文件（除resources外）放到 target/classes 目录，作为输出的 jar 一部分。需要指定资源文件位置。以下内容放到 `build标签`中。

```xml
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory><!--所在的目录-->
                <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <!--filtering 选项 false 不启用过滤器， *.property 已经起到过滤的作用了-->
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

## 7. idea创建Maven工程

### 7.1. 创建普通java工程

![](images/QQ图片20200212150902.png)

**目录结构**

![](images/QQ图片20200212151043.png)

### 7.2. 创建web工程

#### 7.2.1. 使用骨架（archetype）的形式

```properties
archetype = 原型，骨架
```

![](./images/QQ图片20190823174127.png)

![](./images/QQ图片20190823174218.png)

![](images/QQ图片20190823174305.png)

![](./images/QQ图片20190823174444.png)

![](images/QQ图片20200212153645.png)

#### 7.2.2. java工程改造成web工程 (推荐)

![](./images/QQ图片20190823174626.png)

![](./images/QQ图片20190823174713.png)

​    

![](./images/QQ图片20190823174812.png)

在对的模块下添加web

![](./images/QQ图片20190823174930.png)

这里改成了 E:\javamaven\src\main\webapp\WEB-INF\web.xml ,即在src/main下创建webapp文件夹

![](./images/QQ图片20190823175205.png)

这里是： E:\javamaven\src\main\webapp\，即src/main/webapp是web根目录

![](./images/QQ图片20190823175521.png)

###  7.3. 工程目录介绍

* **大概介绍**

  ![](images/QQ图片20200212154100.png)

* **POM文件**

  * **大概介绍**

  ![](images/QQ图片20200212154613.png)

  

  * **坐标信息**

  ![](images/QQ图片20200212154905.png)

  * **环境变量的配置**

  ![](images/QQ图片20200212154959.png)

  

  * **坐标依赖**

    ![](images/QQ图片20200212155125.png)

  * **插件信息**

    

    ![](images/QQ图片20200212155433.png)


## 8. maven工程的运行方式


### 8.1. tomcat的方式

> 和web工程一样

![](images/QQ图片20200212155915.png)

### 8.2. maven-tomcat插件的方式

* **pom文件添加如下内容**

  ```xml
  <!--在pom文件中 添加comcat7插件 -->

              <plugin>
                  <groupId>org.apache.tomcat.maven</groupId>
                  <artifactId>tomcat7-maven-plugin</artifactId>
                  <version>2.2</version>

                  <configuration>
                      <!-- 项目访问路径 本例：localhost:9090, 如果配置path为aa，则访问路径为localhost:9090/aa -->
                      <path>/</path>
                      <port>9090</port>
                      <uriEncoding>UTF-8</uriEncoding><!-- 非必需项 -->
                  </configuration>
              </plugin>
  ```

  ![](images/QQ图片20200212160143.png)

* **插件启动方式一**

  

  ![](./images/tomcat1.png)

* **插件启动方式二**

  ![](./images/tomcat2.png)

### 8.3. maven-jetty插件的方式

* **在pom文件中添加jetty插件**

  ```xml
        <plugin>
          <groupId>org.eclipse.jetty</groupId>
          <artifactId>jetty-maven-plugin</artifactId>
          <version>9.3.0.M2</version>
          <configuration>
            <webAppConfig>
              <!--配置根路径-->
              <contextPath>/</contextPath>
            </webAppConfig>
            <httpConnector>
              <!--配置端口-->
              <port>10000</port>
            </httpConnector>
          </configuration>
        </plugin>
  ```

* **运行方式一**

  ![](images/QQ图片20200212161215.png)

  ​		

* **第二种运行方式**

  ![](images/QQ图片20200212161335.png)

## 9.maven的JDK版本问题

* **问题描述**

  ![](images/QQ图片20200212162823.png)

![](images/QQ图片20200212162912.png)

### 9.1. 解决方式一（pom.xml配置属性）

* **在pom.xml中配置**

  ```xml
    <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
          <java.version>1.8</java.version>
          <maven.compiler.source>1.8</maven.compiler.source>
          <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
  ```

* **刷新**

  ![](images/QQ图片20200212163224.png)

### 9.2. 解决方式二（pom.xml中配置compile插件）

* **在pom.xml添加如下信息**

  ```xml
   <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <configuration>
                      <source>1.8</source>
                      <target>1.8</target>
                      <encoding>UTF-8</encoding>
                  </configuration>
              </plugin>
          </plugins>
      </build>
  ```

* **刷新**

  ![](images/QQ图片20200212163224.png)

### 9.3. 解决方式三（maven配置文件中添加profile）

*  **在maven配置文件setting中profiles标签内添加如下信息**

```xml
<profile>
		<id>jdk1.8</id>
		<activation>
			<activeByDefault>true</activeByDefault>
				<jdk>1.8</jdk>
			</activation>
		<properties>
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
			<maven.compiler.encoding>utf-8</maven.compiler.encoding>
		</properties>
	</profile>
```

## 10. 坐标的作用范围scope

![](images/QQ图片20200212165446.png)

*  **compile** 

		默认scope为compile，表示为当前依赖参与项目的编译、测试、运行、打包等`全阶段`，属于强依赖。打包之时，会打到包里去。

* **test** 

		该依赖仅仅参与测试相关的内容，包括测试用例的编译(test-compile)和执行(test)，比如定性的Junit。

*  **runtime** 

		依赖仅参与运行周期中的使用，编译不参与。一般这种类库都是接口与实现相分离的类库，比如JDBC类库，在编译之时仅依赖相关的接口，在具体的运行之时，才需要具体的mysql、oracle等等数据的驱动程序。 此类的驱动都是为runtime的类库。

*  **provided** 

		该依赖在打包过程中，不需要打进去，这个由运行的环境来提供，比如tomcat或者基础类库等等，事实上，该依赖可以参与编译、测试和运行等周期，与compile等同。区别在于打包阶段进行了exclude操作。

![](images/Snipaste_2021-11-06_16-27-24.png)

如下就展示了依赖的范围

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.12</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

## 11. maven的聚合工程



## 12.进入公司maven的使用步骤

1.问maven使用什么版本

2.下载对应的版本

3.直接解压，百度配置JDK,百度配置本地仓库，百度配置阿里云镜像（象征性的询问公司的远程的仓库的位置）,配置profile

4.直接和idea集成创建web工程开发即可