# Java开发环境搭建-MAC篇

## 1 安装JDK

下载zulu JDK : https://www.azul.com/downloads/?package=jdk

正常情况不需要配置环境变量，但如果出现环境变量问题可按如下配置：

首先通过如下命令查看 JDK 安装位置:

```bash
/usr/libexec/java_home -V
```

![](./images/Snipaste_2022-03-27_04-58-58.png)

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

## 2 安装HomeBrew

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

## 3 安装Git

可以通过homebrew安装Git

```bash
brew install git
```

## 4 安装maven

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

## 5 安装 IDEA

安装IDEA 并 激活后 ，进行全局配置：文件encoding , annotation processing , git , maven

## 6 安装mysql

打开官网，下载社区版https://www.mysql.com/cn/

选择对应的版本，比如mac arm版本，根据提示安装

![](./images/Snipaste_2022-03-27_17-07-25.png)

打开偏好设置，查看mysql

![](./images/Snipaste_2022-03-27_20-02-37.png)

配置环境变量 vim ~/.bash_profile

```bash
export PATH=$PATH:/usr/local/mysql/bin
```

刷新配置环境变量

```bash
source ~.bash_profile
```

## 7  安装tomcat

下载tomcat9

链接：https://tomcat.apache.org/download-90.cgi

鼠标放到圈中的两个文件中的一个即可，然后双击获取他的下载链接进行下载

![](./images/Snipaste_2022-06-04_00-23-41.png)

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

![](./images/Snipaste_2022-06-04_00-36-17.png)
