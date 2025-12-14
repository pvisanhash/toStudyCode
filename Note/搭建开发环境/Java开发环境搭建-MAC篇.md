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

安装成功后查看homebrew版本

```bash
brew --version
```

更新homebrew

```bash
brew update
```

更新某个组件

```bash
brew upgrade xxx
```

如果遇到网络原因安装失败可以通过如下的方法解决(可以考虑将clash打开global代理)

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

## 安装Python3

> 参考:[mac安装python3](https://www.trae.cn/article/3133510146)

**首先，你需要知道你当前 Python 版本以及它的安装路径。**

1. **检查 Python 版本：**

```bash
 python --version # 可能指向 Python 2.x
 python3 --version # 通常指向 Python 3.x
```

  

2. **检查 Python 路径：**

```bash
which python
which python3
```


根据你 `which` 命令的输出，我们可以推断出安装方式。常见的安装方式有：


- **macOS 系统自带 Python：** 通常在 `/usr/bin/python`。**不建议直接修改或升级系统自带的 Python，因为它可能被 macOS 的内部工具所依赖，直接操作可能导致系统不稳定。**
    
- **Homebrew 安装：** 通常在 `/usr/local/bin/python3` 或 `/opt/homebrew/bin/python3` (M1/M2 Mac)。这是 Mac 用户最推荐和最方便的管理 Python 的方式。
    
- **pyenv 安装：** 通常在 `~/.pyenv/shims/python`。pyenv 是一个强大的 Python 版本管理工具。
    
- **Anaconda/Miniconda 安装：** 通常在 `~/anaconda3/bin/python` 或 `~/miniconda3/bin/python`。Anaconda 是一个全面的数据科学平台。
    
- **直接从 Python 官网下载安装包 (pkg)：** 通常安装到 `/Library/Frameworks/Python.framework`。
    


下面针对不同的安装方式讲解如何升级：

---
### 最推荐的方式：使用 Homebrew (如果还没有安装，强烈建议安装)

Homebrew 是 macOS 上最流行的包管理器，也是管理 Python 版本的最佳方式之一。

  
**1. 安装 Homebrew (如果尚未安装):**打开终端并运行：

  
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

  
按照屏幕上的指示完成安装。

  
**2. 升级 Homebrew 本身:**

  
```bash
brew update
```

  
**3. 升级 Python (如果已通过 Homebrew 安装):**如果你的 Python 3 是通过 Homebrew 安装的，只需运行：

  
```bash
brew upgrade python
pip3 install --upgrade pip
```

  
这会把 Homebrew 版的 Python 升级到最新稳定版本。


**4. 如果你尚未通过 Homebrew 安装 Python 3，或者需要安装特定版本:**

  
```bash
brew install python # 这会安装最新稳定版 Python 3
```

  
**5. 确保你的 PATH 环境变量正确设置:**Homebrew 会自动将 `/usr/local/bin` (或 M1/M2 Mac 上的 `/opt/homebrew/bin`) 添加到你的 PATH 中，确保 Homebrew 安装的 Python 优先于系统自带的。在你的 `~/.zshrc` (macOS Catalina 及更高版本默认) 或 `~/.bash_profile` (旧版本) 中确认有类似以下的行：

  
```
export PATH="/usr/local/opt/python/libexec/bin:$PATH" # 对于 Intel Mac
export PATH="/opt/homebrew/opt/python/libexec/bin:$PATH" # 对于 M1/M2 Mac
```

  
或者更通用的，确保 Homebrew 的 bin 目录在 PATH 前面：

  
```
export PATH="/opt/homebrew/bin:$PATH" # For M1/M2 Mac
export PATH="/usr/local/bin:$PATH" # For Intel Mac
```

  
更新后，运行 `source ~/.zshrc` 或 `source ~/.bash_profile` 使其生效。


**6. 验证升级:**

  
```bash
python3 --version
which python3
# 查看python3包管理工具pip3版本
pip3 --version
```

  
现在 `python3` 应该指向 Homebrew 安装的最新版本。


---

### 使用 pyenv (推荐用于管理多个 Python 版本)

如果你需要在同一台机器上管理多个 Python 版本（例如，项目 A 需要 Python 3.8，项目 B 需要 Python 3.10），`pyenv` 是一个绝佳的选择。

  

**1. 安装 pyenv (如果尚未安装):**

  

```
brew install pyenv
```

  

然后，你需要将 pyenv 初始化添加到你的 shell 配置文件。编辑你的 `~/.zshrc` (或 `~/.bash_profile`)，添加以下行：

  
```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```


保存文件并运行 `source ~/.zshrc`。


**2. 列出可用的 Python 版本:**


```
pyenv install --list
```

  

**3. 安装新的 Python 版本 (即升级):**选择你想要安装的最新版本，例如 Python 3.11.x:

  

```
pyenv install 3.11.x # 将 x 替换为具体的最新补丁版本，如 3.11.4
```

  
**4. 设置全局或局部 Python 版本:**

  
- **全局设置 (对所有 shell 会话生效):**


```
 pyenv global 3.11.x
```

  

- **局部设置 (仅对当前目录及其子目录生效):**进入你的项目目录，然后运行：


```
 pyenv local 3.11.x
```


**5. 验证升级:**


```
python --version
python3 --version
which python
which python3
```

  
pyenv 会通过修改 PATH 来“垫片” (shim) 你选择的 Python 版本。


---

### 使用 Anaconda/Miniconda

如果你使用 Anaconda 或 Miniconda 管理 Python 环境，升级方式如下：

  
**1. 更新 conda 本身:**

  
```
conda update conda
```

  

**2. 升级基础环境中的 Python (不推荐，最好创建新环境):**如果你想直接升级 `base` 环境中的 Python，可以尝试：

  
```
conda update python
```


**但更推荐的做法是创建新的环境来管理不同的 Python 版本。**

  

**3. 创建一个新的环境并指定 Python 版本:**

  

```
conda create -n my_new_env python=3.11
conda activate my_new_env
python --version
```

  

这样你就可以在不同的环境中切换，每个环境有自己独立的 Python 版本和库。

  
---

### 从 Python 官网安装包 (pkg) 升级

如果你最初是从 Python 官网下载的 `.pkg` 安装包，你可以：

  

1. **访问 Python 官网：** [www.python.org/downloads/macos/](https://xie.infoq.cn/link?target=https%3A%2F%2Fwww.python.org%2Fdownloads%2Fmacos%2F)  
    
2. **下载最新版本的 macOS 安装程序。**
    
3. **运行下载的** `**.pkg**` **文件。** 它会引导你完成安装，并通常会安装在 `/Library/Frameworks/Python.framework` 下的一个新版本目录中，并更新你的系统 PATH，使其指向最新的安装。
    

  

**注意：** 这种方法可能会安装多个 Python 版本在你的系统上，并且可能需要手动管理 PATH，不如 Homebrew 或 pyenv 灵活。

  
---

### 总结和最佳实践：

1. **永远不要直接修改 macOS 系统自带的 Python (**`**/usr/bin/python**`**)。**
    
2. **对于大多数 Mac 用户，使用 Homebrew 是最简单、最推荐的方式来安装和升级 Python 3。**
    
3. **如果你需要管理多个 Python 版本用于不同的项目，使用** `**pyenv**` **是理想的选择。**
    
4. **如果你进行数据科学或机器学习工作，并且依赖大量的科学计算库，Anaconda/Miniconda 是一个强大的解决方案。**
    
5. **在升级 Python 后，记得重新安装或更新你的项目依赖 (**`**pip install -r requirements.txt**`**)，因为 Python 版本的变化可能会影响库的兼容性。**
    
6. **始终在一个新的终端会话或使用** `**source**` **命令来确保 PATH 环境变量的更改生效，然后再检查 Python 版本。**


通过安装homebrew安装python3

> Homebrew Python 就是 CPython，只是通过 Homebrew 包管理器来安装和管理，更方便 macOS 开发者使用


## 安装Pycharm


如果显示pycharm已损坏,通过如下的方式来修复

先确定你的电脑任何来源是否已经打开(系统偏好设置-安全与隐私-安全性-任何来源)
如果没有，先开任何来源，方法如下：
复制指令到终端粘贴回车运行

```bash
sudo spctl --master-disable
```

再到(系统偏好设置-安全与隐私-安全性-任何来源)看看如何已经开启任何来源。
再修复一下就好了，修复方法如下：

复制指令(这条命令是用来 **移除 macOS 的隔离属性**，特别是针对从非 App Store 下载的应用程序。)

```
sudo xattr -r -d com.apple.quarantine
```

粘贴到终端，手打一个空格，把已损坏的PyCharm拖动到空格后面，回车，输入电脑开机密码，再次回车。

右键打开软件，已经可以了，一定要注意，右键打开。

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

