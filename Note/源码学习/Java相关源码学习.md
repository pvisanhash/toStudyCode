
---

author: aitx

title: Java相关源码学习

time: 2023-06-24 周六

tags: 
  - JDK
  - 学习
  - 编程
  - 源码

---


# JDK源码学习

## 学习大纲

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306242356977.jpg)

## 搭建JDK源码调试工程

### 0. 写在前面

手把手配置一套自己的 azul open JDK 17 的源码阅读环境,方便debug与记笔记。配置很简单，跟着一步步操作可以了，具体 JDK 的版本看你自己选择，区别只在于下载时选择不同的 JDK 版本。

### 1. 下载JDK，获取源码压缩包

如果你本地已经安装了JDK，并且你就是想搭建已安装JDK版本的源码阅读环境，这里就不用下载了，我今天选择重新下载Azul Open JDK 17，这是目前最常用的版本。

Mac上查找你本地安装JDK的路径可以使用命令：`/usr/libexec/java_home -V`

下载地址：https://www.azul.com/downloads/#zulu

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241841753.png)

我们选择 .zip 结尾的免安装版本，下载后直接解压，然后找到解压缩后 lib 目录中的 `src.zip` ，这个就是我们需要的源码压缩包。你如果已经安装了 JDK，就直接去 JDK 的安装目录下 lib 目录中找到 src.zip ，记着这个位置，后面我们会用到哈。

![6230383317e4df23889f092a6756c1ab.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847480.png)



### 2. 创建IDEA项目

在 IDEA 中创建一个简单的 Java 项目，顶部栏 File -> New -> Project…

![59b006583fa358271ce0e9c56f886fd1.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847481.png)

创建一个Java空项目，直接 next：

![90f52a4fd803f34c1d575bc76d350cfd.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847482.png)

名字看你喜欢取就行：

![1b3ea4b0ce4da8e4580df763face3d05.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847483.png)

### 3. 解压源码包到IDEA项目中

找到第一步中的 `src.zip` 源码包，解压到你的项目目录中。我的是在项目的根目录中新建了 jdk17 文件夹，准备将源码解压到这里。

解压完成后的结构如下：

![265d62488008478e6ed1f72a278fd249.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847485.png)

到这其实我们就可以查看 `*.java` 后缀的源代码了：

![c034e5b92b77d97d7b739410e143e345.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847486.png)

但是这样肯定是不够的，我们看源码最重要的还是要靠 debug 调试，现在这样 debug 还是会进入到 jdk 自带的压缩后的源码，这个源码是来自于 src.zip，编辑器中显示是带锁的，无法编辑。

那肯定不行了，别急接下往下看。


### 4. 配置调试时进入可编辑的源码

通过这一步的配置我们就可以**一边调试一边写注释了**，每次 debug 进入的都是不断完善写过注释的源码，这种 feel 倍儿爽😁

需要如下几步，我们继续搞起~

### IDEA配置调试时允许单步进入

JDK 源码是受保护的，无法直接通过 F7 （step into），可以通过 alt+shift+F7 （force step into），需要我们在 IDEA 中取消以下限制，就可以直接通过 F7 进入源码了。

![c4b0b43894dfa7cdbae8f453ca82a3b4.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847487.png)

### 设置当前项目关联解压后的源码

既然默认项目调试进入的源码是 JDK 目录下 src.zip 包中的源代码，那么这里我就需要修改下项目关联的 JDK。

Project Structure -> SDKs 下 新建SDK：

![7099ccc049f21054360f02783d1d7896.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847488.png)

还是先选择我们下载的 JDK17 的 Home 目录：

![b83c1de9b2ae0739c3682da61ff952ae.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847489.png)

名字设置为 `jdk17-source`(根据你的喜好命名即可)。然后选择 `jdk17-source` 的 `Sourcepath` 选项，将源码关联目录删掉。

`command+a` 全选后删除：

![8b15e08093ec14d991debe785ac23572.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847490.png)

然后点击 + 新增，选择我们当前项目中解压后的源码目录：

![0c5766ccc6f9f43ebd5c59da9062b4bb.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847491.png)

导入成功后，点击ok即可：

![781fb809fd5cc35994892bb81cc2001a.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847492.png)

然后设置当前项目的 JDK 为我们新增的 `jdk17-source` ：

![94786f20d2fbed91aae7cd30cda093a4.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847493.png)

至此，大功告成！你是否已经迫不及待的想要调试看看啦，我们搞起！

### 5. debug 调试

此时，我们只需要在 src 目录下写我们的测试代码就好了，打上断点，可劲的钻研吧，少年！

![357e28af427ca7bad202fe4e42af1373.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847494.png)

单步调试就会进入到我们解压缩后的源码文件，可以自由的写写画画：

![60aa37e3fce3c9efbb0d56a54b271436.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847495.png)

### 6. 可能遇到的问题

我按照上面这个步骤搞下来是没有遇到啥问题的，这里记录下可能遇到的问题，一次性搞定肯定是最好的，有报错的可以看看。

#### 编译 OOM

增加IDEA编译所需堆内存，默认700，这里改为1024。

![ea7e06a780eecc2f15ccab1ed839fddc.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202306241847496.png)

### 7. 上传到 github

这一步是为了可以让你在家里和公司的电脑上都可以获取到你注释过的源代码，同时在工作中遇到的问题，排查后有新的理解也可以及时同步到远程仓库，方便多端同步维护嘛~

首先在 github 新建一个和本地项目名一致的仓库，然后给本地项目git初始化后关联远程仓库即可。

进入到本地源码项目根目录中依次执行如下命令：

```properties
git init
# git remote add 远程仓库名,名称唯一即可 远程仓库地址 
git remote add origin https://gitee.com/isevenluo/oracle-jdk17-source-learn.git 
# 查看关联的所有的远程仓库名称及地址 
git remote -v 
# 查看所有的远程仓库名称 
git remote 
# 提交本地仓库分支(master) 给远程仓库(origin)分支(master) 此处是强制提交
git push origin master -f 
# 查看当前未提交的内容，此时应该为空
git status
```

### 8. 写在最后

今天给大家分享了如何在本地搭建一套JDK源码的阅读环境，我建议学习Java的同学都配置下，JDK的源码还是要读一读的，对你将会大有益处。加油吧，少年 🤙