---

author: aitx

title: Java开发环境搭建-MAC篇

time: 2022-10-24 周一

tags: 
  - Java
  - 开发环境
  - MAC
---

# Java开发环境搭建-MAC篇

## 安装JDK

下载zulu JDK : https://www.azul.com/downloads/?package=jdk

正常情况不需要配置环境变量，但如果出现环境变量问题可按如下配置：

首先通过如下命令查看 JDK 安装位置:

```bash
/usr/libexec/java_home -V
```

![JDK安装位置](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042216148.png)

拷贝 JDK 地址，键入如下命令准备编辑环境变量：

```bash
vim ~/.bash_profile
```

随后添加环境变量：

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home
export PATH=$PATH:$JAVA_HOME/bin
```

刷新配置文件

```bash
source ~/.bash_profile
```

如果使用的zsh/终端，需要每次开机后刷新配置文件，可在~/.zshrc中加入如下命令不用每次手工刷新配置

```bash
source ~/.bash_profile
```

## 安装HomeBrew

打开HomeBrew官网：https://brew.sh/

复制官网提供的命令进行安装,后根据提示进行操作即可

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

如果遇到网络原因安装失败可以通过如下的方法解决

1 )只是单次在终端使用代理键入以下命令，关闭终端会话则清理代理

```bash
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890
```

2 )修改hosts文件指定域名的ip

 查找 ip -> 修改 host -> 再去执行安装脚本 （有可能还是失败）

查找ip的网址： https://www.ipaddress.com/ 

找到ip进行ping通：

```bash
ping 199.232.28.133
```

如果可ping通，修改hosts：

```bash
sudo vim /etc/hosts
```

添加如下内容：
199.232.28.133  raw.githubusercontent.com

3 ) 使用国内gitee源的命令进行安装

## 安装Git

可以通过homebrew安装Git

```bash
brew install git
```

## 安装maven

maven 官网下载 ：https://maven.apache.org/

将下载的zip包解压抽取后，放到/usr/local/目录下

vim ~/.bash_profile配置环境变量

```bash
export M2_HOME=/usr/local/apache-maven-3.8.5

export PATH=$PATH:$M2_HOME/bin
```

刷新配置文件

```bash
source ~/.bash_profile
```

后面配置文件，主要修改本地仓库、镜像、profile，详情见maven笔记

## 安装 IDEA

安装IDEA 并 激活后 ，进行全局配置：文件encoding , annotation processing , git , maven

### 激活Idea

### 无限重置试用期方法

[参考链接](https://youwu.today/blog/jetbrains-evaluate-reset/#%E8%AF%95%E7%94%A8%E8%BF%87%E6%9C%9F%E4%BF%AE%E5%A4%8D---202122-%E7%89%88%E6%9C%AC%E4%B9%8B%E5%89%8D)

针对Idea版本为2021.2.2及之前的版本,我们可以使用`IDE Eval Reset`插件无限重置为30天试用

如果超过30天没有打开Idea我们可以通过如下的方法进入Idea,试用授权文件位于程序配置目录下的 _eval_ 文件夹.程序配置目录如下:

```properties
windows：`%userprofile%/AppData/Roaming/JetBrains/产品名版本号`

macos: `~/Library/Application Support/JetBrains/产品名版本号`

linux: `~/.config/JetBrains/产品名版本号`
```

将程序配置路径下的 _eval_ 文件夹 重命名或者删除后，重启程序即可。

## 安装mysql

打开官网，下载社区版https://www.mysql.com/cn/

选择对应的版本，比如mac arm版本，根据提示安装

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042216150.png)

打开偏好设置，查看mysql

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042216151.png)

配置环境变量 vim ~/.bash_profile

```bash
export PATH=$PATH:/usr/local/mysql/bin
```

刷新配置环境变量

```bash
source ~.bash_profile
```

## 安装tomcat

下载tomcat9

链接：https://tomcat.apache.org/download-90.cgi

鼠标放到圈中的两个文件中的一个即可，然后双击获取他的下载链接进行下载

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042216152.png)

进入下载目录，使用命令进行解压

```shell
tar -zxvf apache-tomcat-9.0.63.tar.gz
```

将解压后的文件夹移到/usr/local下

配置环境变量 vim ~/.bash_profile

```bash
export PATH=$PATH:/usr/local/apache-tomcat-9.0.63/bin
```

刷新配置环境变量

```bash
source ~.bash_profile
```

进入/usr/local/apache-tomcat-9.0.63/bin目录执行

```
# 开启tomcat
./startup.sh 
# 关闭tomcat
./shutdown.sh 
```

访问http://localhost:8080/即可看到tomcat主页

idea配置tomcat

选择 preferences->build,execution,deployment->application servers-> 加号-> tomcat

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042216153.png)



## 安装NodeJS

访问官网：https://nodejs.org/en/

下载后长期支持版下一步安装

安装完成后，打开终端，输入 npm -v 和 node -v两个命令,如出现版本信息，说明安装成功。

打开Mac 终端，配置全局环境变量输入`vim .bash_profile`按i进入编辑状态，打开之后添加一行代码：`PATH=$PATH:/usr/local/bin/`并保存，重新打开终端，输入node，就会进入node环境。control+c 按两次退出node环境。

