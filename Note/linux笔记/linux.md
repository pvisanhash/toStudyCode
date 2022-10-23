

# Linux

## 1. linux的读法

* **里纽克斯**
* **利尼克斯**
* **里纳克斯**

## 2. Linux是什么

>  一种免费，开源的操作系统
>
>  常作为服务器的操作系统使用
>
> 在目前市面上的服务器操作系统一般使用linux,极少数使用Windows Server(微软公司为服务器提供的应用操作系统,包含更加全面的权限管理系统以及防火墙机制).

## 3. Linux和Window系统的区别

|                  Linux                  | Window                 |
| :-------------------------------------: | ---------------------- |
|                  免费                   | 收费                   |
|                比较安全                 | 不安全 三天两头有补丁  |
| 命令操作（也可以有图形） 让人感觉更高端 | 有图形界面便于用户操作 |
|           开源的 很强的定制化           | 封闭的定制化很难       |
|        一般应用企业级开发服务器         | 一般用户桌面操作系统   |

## 4. 常用发行版本

* **RedHat（红帽）**
* **centOs（美分 操作系统）** 
* **Ubuntu**

## 5. 虚拟机的安装 

> 由于我们的系统是window系统  我们想在window系统中 安装linux系统 需要依赖虚拟机  

### 5.1. 双击安装程序（然后等待）

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218078.png)

### 5.2. 点击下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218080.png)

### 5.3. 勾选同意下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218081.png)



### 5.4. 选择安装目录下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218082.png)

### 5.5. 不勾选下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218083.png)

### 5.6. 下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218084.png)

### 5.7. 安装

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218085.png)

### 5.8. 等待安装成功

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218086.png)



### 5.9. 点击许可证输入内容

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218087.png)

### 5.10. 安装成功

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218088.png)

## 6. centOS的安装

### 6.1. 新建虚拟机

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218089.png)

### 6.2. 默认直接下一步

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218090.png)

### 6.3. 创建空白虚拟光盘

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218091.png)

### 6.4. 选择对应版本

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218092.png)

### 6.5. 虚拟机版本以及位置

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218093.png)

### 6.6. 处理器配置

> 看自己是否是双核、多核  虚拟机处理器数量可以根据自己的机器配置来定

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218094.png)

### 6.7. 设置内存 

> 这个内存要看自己电脑的内存有多大

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218095.png)



### 6.8.设置网络

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218096.png)



**三种网络连接的区别：**

​	**桥接模式：局域网容易导致ip冲突 **

​	**Nat模式：不容易ip冲突 并且可以访问外网 **

​	**仅主机模式：不容易ip冲突 不能访问外网** 



### 6.9. 设置IO控制类型

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218097.png)

### 6.10. 设置硬盘类型

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218098.png)



**区别：**

**IDE: 老的磁盘类型**

**SCSI: 服务器上推荐使用的磁盘类型，串口。**

**SATA: 也是串口，也是新的磁盘类型。**

 

### 6.11. 创建新的虚拟磁盘

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218099.png)



### 6.12. 设置磁盘容量

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218100.png)



### 6.13. 设置存储位置

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218101.png)

### 6.14. 配置完成

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218102.png)



### 6.15. 虚拟机设置

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218103.png)

### 6.16. 配置镜像

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218104.png)



### 6.17. 启动虚拟机

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218105.png)

### 6.18. 安装centOS7

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218106.png)



### 6.19. 选择语言

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218107.png)



### 6.20. 其他配置(啥都不用做)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218108.png)

### 6.21. 设置root密码

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218109.png)



### 6.22. 输入自己的密码

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218110.png)



### 6.23. 安装完成后重启

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218111.png)



### 6.24. 进入centOS

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218112.png)



### 6.25. 登录

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218113.png)



## 7. 相关指令

### 7.1. 开关机相关指令(了解)

* **shutdown**

  * shutdown -h now  表示立即关机
  * shutdown -h 1  表示一分钟之后关机
  * shutdown  -r now  表示重启 

* **halt （直接使用表示直接挂机）**

* **reboot（直接使用表示重启）**

* **syn （直接使用表示把内存中的数据同步到硬盘上）**

  > 当我们关机或者重启时 都应该把数据同步到硬盘上之后 再做关机或者重启操作 

### 7.2. 文件相关操作(重要)

* **查看路径**

  **命令：pwd**

  获得当前所在目录的绝对路径(和密码没有关系),

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218114.png)

  

* **查看当前目录**

  **命令： ls 或者  ll**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218115.png)

  

* **切换目录**

  **命令：cd + 路径（可以是相对路径 也可以是绝对路径）**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218116.png)

  

* **创建文件夹**

  **语法一：创建一级目录： mkdir +文件名称**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218117.png)

  

  **语法二：创建多级目录： mkdir  + 路径一/路径二/ 路径三  -p **

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218118.png)

  

* **创建文件**

  **语法： touch 文件名称（要加后缀名）**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218119.png)

  

* **复制文件**

  **语法一： 复制文件到指定目录**

  **命令: cp  要复制的文件    指定位置**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218120.png)



​	**语法二： 复制文件夹到指定位置**

​	**命令： cp  -r 要复制的文件夹   指定位置**

  	![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218121.png)



**语法三： 复制时 如果有重复文件 是否覆盖问题  我们可以强制覆盖**

**命令：\cp -r 要复制的文件  指定位置**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218122.png)



* **移动文件**

  **语法一 ：重命名： mv  文件名  新名字**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218123.png)



​	**语法二： 移动文件夹： mv 要移动的文件  目标位置**

​	![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218124.png)



* **删除文件或者文件夹**

  **语法一：  删除文件： rm  文件名(带后缀)**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218125.png)

  

  **语法二： 删除文件（无提示） rm  -f  文件名**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218126.png)

  

  **语法三： 删除文件夹  rm  -r  文件名**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218127.png)



​	**语法四：删除文件夹无提示： rm -rf  文件夹名**

​	![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218128.png)



### 7.3. 文件VI编辑器

> Linux内置vi文本编辑器  vim是vi的增强版 

#### 7.3.1. 三种模式

* **一般模式(默认模式)**

  > 使用vim/vi命令打开一个文档 就直接进入了一般模式 在这个模式中可以使用上下左右键 或者 H J K L移动光标

* **插入模式（编辑模式）**

  > 需要按下 i  I  o  O   a  A  r R等等任何一个字母进入此模式

* **命令行模式（底行模式/末行模式）**

  > 一般模式 按 ”:” 符号 就能进入这个模式 在这个模式下 可以做读取 存盘 替换 离开等



#### 7.3.2. 各种操作

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218129.png)

### 7.4. 日期相关（了解）

* **date指令**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218130.png)

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218131.png)

* **cal指令**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218132.png)

### 7.5. 查询相关

* **find指令**

  > 从指定目录向下递归遍历所有的子目录 将满足的条件的文件或者文件夹 显示在终端

  **命令： find 搜索范围 搜索规则**

  其中规则有 

  ​	-name 根据名字搜索

  ​	-user 查询搜索范围内指定用户的问题

  ​	-size 按照指定的文件大小搜索 ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218134.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218135.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218136.png)



**这个大小 不一定只能用M 也可以用其他的大小单位 比如K**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218137.png)

* **grep 命令**

  > 作用：过滤查找 常常搭配管道符 “|” 使用 

​    **语法一： 在文件中查找对应的内容**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218138.png)

```properties
n = line number = 行号

i = ignore case = 忽略大小写
```

**语法二：管道使用**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218139.png)





**常用命令**

​	 根据软件名字查看进程

​		ps -ef|grep tomcat

 	 根据端口查看占用进程

​    	        netstat -unltp|grep 8080

```properties
如果提示不存在netstat,则yum -y install net-tools安装相应软件

u = udp = user datagram protocal = 用户数据报协议

n = numberic

l = listening

t = tcp = transfer controll protocal = 传输控制协议

p = pid / programm
```

### 7.6. 压缩命令

#### 7.6.1. tar格式

**解包：tar xvf FileName.tar**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218140.png)

**打包：tar cvf FileName.tar DirName**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218141.png)

#### 7.6.2. gz格式

**压缩: gzip用于压缩文件 把文件压缩层.gz格式**

​	![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218142.png)

**注意使用gzip压缩文件时 不会保留原文件**

**解压：gunzip 用于解压.gz格式的文件 **

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218143.png)

**使用gunzip解压.gz文件时 不会保留压缩包**

#### 7.6.3. tar.gz

**语法一 压缩：tar	选项 XXX.tar.gz	打包的内容 **

**语法二 解压：tar 选项 压缩包 -C 目录**

tar指令 是解压 还是压缩 是根据选项决定的  

​	选项如下：

​		-c 表示 产生.tar打包文件  ---compact

​		-v 表示 显示详细信息  ---verbose

​		-f 表示  指定压缩的文件名  ---file

​		-z 表示 打包同时压缩 ---zip

​		-x 表示 解压 ---extract

​		-C 表示解压到的目录 ---content

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218144.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218145.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218146.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218147.png)

#### 7.7.4. zip格式

* **安装zip  unzip支持**

  **yum install -y  zip  unzip**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218148.png)

* **压缩**

   zip +压缩包名.zip  要压缩的文件  表示：把单个文件压缩 

   zip -r 压缩包名.zip  要压缩的文件夹  表示： 把整个文件夹压缩 

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218149.png)

* **解压**

   unzip 压缩包  表示：把指定压缩包解压在当前目录

   unzip  -d目录 压缩包  表示：把压缩包解压到指定目录

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218150.png)

## 8. 网络相关

### 8.1. 让虚拟机有网

**第一步：**cd /etc/sysconfig/network-scripts/

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218151.png)

**第二步：** 修改内容

   ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218152.png)



**第三步：重启**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218153.png)



**第四步：重启后 查看ip**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218154.png)

### 8.2. 防火墙相关

```properties
systemctl stop firewalld.service             ##停止firewall
systemctl disable firewalld.service        #禁止firewall开机启动
systemctl restart firewalld.service    # 重启防火墙 
```

### 8.3. 开放某个端口

```properties
firewall-cmd --zone=public --add-port=8080/tcp --permanent
## 开放端口之后 需要重新启动防火墙
systemctl restart firewalld.service    # 重启防火墙 
```

## 9. xshell的安装

### 9.1. 解压压缩包

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218155.png)

### 9.2. 进入解压目录

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218156.png)

**注意点：**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218157.png)

### 9.3. 设置不更新 

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218158.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218159.png)

### 9.4. 连接linux

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218160.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218161.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218162.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218163.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218164.png)

## 10. 宿主机和linux文件互通

### 10.1. VMTools 

> 略

### 10.2. 共享文件夹

 

* **设置共享文件夹**

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218165.png)



* **查找共享文件夹**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218166.png)



### 10.3. RZ指令

* **安装rz指令**

  ![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218167.png)



* **rz指令上传**

  rz = receive zmodem  = 接受

* **sz指令可以下载**

  sz = send zmodem = 发送
  
  

**使作rz,sz要在例如xshell的软件中使用才能正确弹出上传下载对话框。**



### 10.4. Xftp工具

 	和shell安装方式一样（记得关闭更新）

## 11. 软件安装

### 11.1 安装JDK

第一步：

```properties
	安装rzsz插件 
	yum install lrzsz 
```

第二步：
```properties
	在opt文件夹下  新建一个java文件夹  通过cd命令进入java文件夹 
	mkdir /opt/java -p 
	cd /opt/java 
```

第三步：
```properties
	把jdk-8u181-linux-x64.rpm 包上传到服务器上 
	输入rz指令 会弹框 双击要选择rpm包 则会上传 
```


第四步: 
```properties
	安装jdk 
	rpm  -ivh  jdk-8u181-linux-x64.rpm   
```

第五步:
```properties
	安装完成之后  
	java -version 查看jdk版本  如果有版本则安装成功  
```

注意：这种方式安装 不需要配置环境变量  

但是如果需要配置请在/etc/profile中配置

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_181-amd64
export PATH=$PATH:$JAVA_HOME/bin
```

```bash
source /etc/profile
```

### 11.2. 安装tomcat

 第一步：

```properties
 在opt下新建文件夹tomcat cd到这个文件夹中 
   		mkdir /opt/tomcat -p 
	      cd  /opt/tomcat   
```

第二步：


```properties
	使用rz指令把apache-tomcat的tar.gz传送到服务器 
```

 第三步：解压  

```properties
	tar -zxvf  tomcat的压缩包
```

 第四步： 
```properties
进入解压目录（当前目录 ls即可插件）中的bin目录  启动tomcat  
 ./startup.sh  
```

  第五步： 使用本机的浏览器 去访问  

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218168.png)

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202210042218169.png)



### 11.3. 安装mysql

第一步： 

```properties
	在opt下面新建一个mysql文件夹 并且cd命令进入
	mkdir /opt/mysql -p 
	cd  /opt/mysql     
```

第二步：
```properties
	通过xftp软件 把mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar包 拉取到新建的mysql文件夹下 
  （也可以通过rz进行上传 ）
```

第三步：
```properties
	解压mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar文件 
	tar  -xvf  mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar
```

第四步： 
```properties
  查看是否有MariaDB   
  rpm -qa|grep mariadb
```

第五步：
```properties
	如果有则删除 
	rpm -e mariadb包全名 --nodeps		
```
第六步：
```properties
	依次安装一下内容	
	rpm -ivh mysql-community-common-5.7.20-1.el7.x86_64.rpm
	rpm -ivh mysql-community-libs-5.7.20-1.el7.x86_64.rpm
	rpm -ivh mysql-community-client-5.7.20-1.el7.x86_64.rpm
```

第七步：
```properties
	安装mysql-community-server-5.7.20-1.el7.x86_64.rpm 
	由于需要其他依赖的支持 所以要先安装依赖 
	yum install -y perl-Module-Install.noarch
	yum install net-tools
```

第八步：
```properties
	依赖安装完成后 安装server
	rpm -ivh mysql-community-server-5.7.20-1.el7.x86_64.rpm
```

第九步：
```properties
	启动mysql  
	service mysqld start
	或者 
	systemctl start mysqld.service
```

第十步：
```properties
	查找原始密码   去log文件中查找临时密码 
	vi /var/log/mysqld.log
```

第十一步： 	
```properties
	mysql -uroot -p+临时密码进行登录  
```

第十二步： 
```properties
	修改root密码  
	ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootroot';
```

第十三步：
```properties
	如果修改的密码不符合密码策略 则修改策略   修改完策略之后 再重新设置密码 
	SET GLOBAL validate_password_policy=0;
```

第十四步：
```properties
	修改完密码后 使用exit 推出当前用户  重新使用mysql -uroot -p新密码登陆  
```

第十五步：
```properties
	给root用户赋予权限  
	1： 切换到mysql数据库中 
		use mysql (mysql表示数据库  并且安装时自带的数据库 不需要你创建) 

	2： 给root用户赋予权限 
		update user set host='%' where user = 'root';
	
	3： 刷新权限列表 
		flush privileges;
```

第十六步： 
```properties
	开通端口
	firewall-cmd --zone=public --add-port=3306/tcp --permanent
	重启防火墙
	systemctl restart firewalld.service

	或者
	
	关闭防火墙  
	systemctl stop firewalld.service
	开机禁用防火墙
	systemctl disable firewalld.service
```

第十七步： 使用本机的navicat进行远程连接 



  