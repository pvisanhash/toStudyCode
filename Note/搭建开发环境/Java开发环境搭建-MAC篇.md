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

## 安装tldr

**tldr** 的全称是：**Too Long; Didn't Read**（太长不看）

安装

```
brew install tldr
```

使用

```
tldr tar  
tldr git  
tldr docker
```

👉 输出的是“常用命令示例”，而不是冗长文档

---

更新缓存（建议执行一次）

```
tldr --update
```

## 安装SDKMAN

链接:https://sdkman.io/install/

 🎯 一句话结论

> 👉 用 SDKMAN! 统一管理 =  
> **所有开发工具（JDK / Maven / Gradle / Tomcat 等）都通过 SDKMAN 安装 + 用 `.sdkmanrc` 做项目级版本锁定**

---

🧠 一、SDKMAN 能管理什么？

👉 本质：**JVM 生态工具管理器**

常见你应该纳入统一管理的：

* JDK（最核心）
* Apache Maven
* Gradle
* Apache Tomcat
* Kotlin
* Groovy

---

🥇 二、统一管理的核心原则（非常重要）

👉 记住这三条：

---

✅ 原则1：**不要手动安装**

❌ 不用：

* pkg
* tar.gz
* Homebrew

👉 全部用 SDKMAN：

```
sdk install java  
sdk install maven  
sdk install gradle  
sdk install tomcat
```
---

✅ 原则2：**不要手动配置环境变量**

❌ 不要写：

```
export JAVA_HOME=xxx  
export MAVEN_HOME=xxx  
export PATH=xxx
```
👉 全部交给 SDKMAN

---

✅ 原则3：**用项目锁版本（关键）**

👉 `.sdkmanrc` 是灵魂

---

🥈 三、如何实现“统一管理”（核心步骤）

---

 1️⃣ 安装所有工具

```
sdk install java 21-tem  
sdk install java 17-tem  

sdk install maven  
sdk install gradle  
sdk install tomcat
```

---

2️⃣ 设置全局默认版本

```
sdk default java 21-tem  
sdk default maven 3.9.9
```

---

3️⃣ 项目级锁定（最关键）

进入项目：

```
touch .sdkmanrc
```

写入：

```
java=17-tem  
maven=3.9.9  
tomcat=10.1.24
```

---

4️⃣ 启用自动切换

```
sdk env
```

👉 进入项目自动切版本

---

🧪 四、效果是什么？

👉 不同项目自动切环境：

项目A（老系统）

```
java=8-tem  
maven=3.6.3
```

---

项目B（新系统）

```
java=21-tem  
maven=3.9.9
```

---

👉 进入目录：

```
cd projectA  
sdk env   # 自动变 Java 8  

cd projectB  
sdk env   # 自动变 Java 21
```

---

🧠 五、进阶玩法（高级工程师必备）

---

✔ 自动加载（无需手动 sdk env）

```
sdk install autoenv
```

或在 `.zshrc`：

```
sdk env install
```

👉 进入目录自动切换（无感知）

---

✔ 查看当前环境

```
sdk current
```

---

✔ 查看所有版本

```
sdk list java  
sdk list maven
```

---

✔ 快速切换

```
sdk use java 17-tem
```

---

🚨 六、统一管理常见坑

---

❌ 坑1：系统还有旧工具

```
which mvn  
which java
```

👉 不是 `.sdkman` 路径 → 有污染

---

❌ 坑2：PATH 被手动修改

👉 会覆盖 SDKMAN

---

❌ 坑3：IDEA 用错版本

在 IntelliJ IDEA：

* JDK 指向 `.sdkman`
* Maven 指向 `.sdkman`

---

❌ 坑4：忘记 sdk env

👉 项目不会自动切换

---

🧠 七、底层原理（你可以理解一下）

SDKMAN 实际做了三件事：

1. 所有工具放在：

```
~/.sdkman/candidates/
```

2. 用 `current` 软链接切换版本：

```
java/current → java/17-tem
```

3. 修改 PATH：

```
~/.sdkman/candidates/java/current/bin
```

---

🚀 八、工程级最终形态（推荐你达到）

👉 你的开发环境应该是：

* ❌ 没有手动 JAVA_HOME
* ❌ 没有 Maven_HOME
* ❌ 没有 brew 安装的 Java/Maven
* ✅ 全部在 `.sdkman/candidates`

---

💡 一句话总结

> 👉 **SDKMAN 统一管理 = 工具全部用它安装 + 环境变量不手写 + 项目用 `.sdkmanrc` 控版本**

---

## 安装JDK

### SDKMAN安装(推荐)

1️⃣ 安装

```
curl -s "https://get.sdkman.io" | bash  
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

2️⃣ 安装 JDK（建议多版本）

```
sdk install java 21-tem   # 主力版本  
sdk install java 17-tem   # LTS兼容  
sdk install java 8-tem    # 老项目
```


 3️⃣ 设置默认版本

```
sdk default java 21-tem
```

4️⃣ 验证

```
java -version
```

### 安装包安装

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

这份指南面向 macOS 用户，目标是搭建一个干净、稳定、容易维护的 Python 开发环境。

推荐结论：

- 使用 Homebrew 安装系统级工具
- 使用 uv 管理 Python 版本、虚拟环境、依赖和命令运行
- 每个项目使用独立的 `.venv`
- 不要直接往 Homebrew Python 或 macOS 系统 Python 里安装项目依赖
- 只有在维护老项目或需要特殊版本矩阵时，再考虑 pyenv

### 推荐方案

当前最推荐的组合是：

```text
Homebrew + uv + 项目级 .venv
```

职责分工：

```text
Homebrew：安装 uv、git、数据库、编译工具等系统级工具
uv：安装 Python 版本、创建虚拟环境、管理依赖、运行项目命令
.venv：每个项目独立隔离依赖
```

Homebrew 自带或安装的 Python 可以保留，但不要把它当作主要项目开发环境。它更适合作为 Homebrew 生态自己的运行依赖。

### 安装 Homebrew

如果已经安装 Homebrew，可以跳过这一节。

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装后确认：

```bash
brew --version
```

Apple Silicon Mac 通常 Homebrew 位于：

```text
/opt/homebrew
```

Intel Mac 通常位于：

```text
/usr/local
```

### 安装 uv

推荐通过 Homebrew 安装：

```bash
brew install uv
```

确认安装：

```bash
uv --version
```

如果 `uv` 命令找不到，检查 shell 配置里是否已经加载 Homebrew：

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

可以把它放进 `~/.zprofile`：

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 安装 Python 版本

使用 uv 安装 Python：

```bash
uv python install 3.12
```

查看可用 Python：

```bash
uv python list
```

查看 uv 会使用哪个 Python：

```bash
uv python find
```

建议优先使用当前稳定版本，例如：

```bash
uv python install 3.12
uv python install 3.13
```

如果某个项目明确要求特定版本，再安装对应版本。

### 创建新项目

创建一个新项目：

```bash
mkdir my-project
cd my-project
uv init
```

固定项目 Python 版本：

```bash
uv python pin 3.12
```

这会生成 `.python-version`，用于记录该项目使用的 Python 版本。

创建虚拟环境：

```bash
uv venv
```

安装依赖：

```bash
uv add requests
```

运行代码：

```bash
uv run python main.py
```

### 迁移已有项目

如果项目已有 `requirements.txt`：

```bash
cd existing-project
uv python pin 3.12
uv venv
uv pip install -r requirements.txt
```

运行：

```bash
uv run python your_script.py
```

如果你习惯激活虚拟环境，也可以：

```bash
source .venv/bin/activate
python your_script.py
```

但长期建议优先使用：

```bash
uv run ...
```

这样不容易因为忘记激活环境而装错包或跑错解释器。

### 管理依赖

添加依赖：

```bash
uv add fastapi
```

添加开发依赖：

```bash
uv add --dev pytest ruff
```

删除依赖：

```bash
uv remove fastapi
```

同步环境：

```bash
uv sync
```

查看依赖树：

```bash
uv tree
```

### 运行常见命令

运行 Python：

```bash
uv run python
```

运行脚本：

```bash
uv run python script.py
```

运行模块：

```bash
uv run python -m pytest
```

运行项目工具：

```bash
uv run ruff check .
uv run pytest
```

### 安装全局 Python 命令行工具

对于 `ruff`、`black`、`ipython`、`httpie` 这类命令行工具，不要装进系统 Python。

推荐使用 `uv tool`：

```bash
uv tool install ruff
uv tool install black
uv tool install ipython
```

查看已安装工具：

```bash
uv tool list
```

升级工具：

```bash
uv tool upgrade --all
```

如果团队已经使用 `pipx`，也可以继续使用：

```bash
brew install pipx
pipx install ruff
```

`uv tool` 和 `pipx` 选一个作为主力即可，不建议混用太多。

### 不推荐的做法

不要这样安装项目依赖：

```bash
pip install requests
pip3 install requests
sudo pip install requests
pip install --break-system-packages requests
```

这些方式容易污染 Homebrew Python 或系统 Python，也容易造成不同项目之间依赖冲突。

也不要依赖全局 Python 环境来运行项目：

```bash
python main.py
```

除非你非常确定当前 shell 已经激活了正确的虚拟环境。

更稳的方式是：

```bash
uv run python main.py
```

### Homebrew Python 的角色

通过 Homebrew 安装某些工具时，Homebrew 可能会自动安装 `python@3.x`。

例如：

```bash
brew install python@3.14
```

这不是坏事，可以保留。

但它的定位更像是系统工具依赖，而不是你的项目环境。项目环境应该交给 uv 创建的 `.venv`。

可以查看 Homebrew Python：

```bash
which python3
python3 --version
```

如果输出类似：

```text
/opt/homebrew/bin/python3
```

说明当前 `python3` 来自 Homebrew。

这没有问题，只要你不直接把项目依赖装进去。

### 是否还需要 pyenv

大多数新项目不再需要 pyenv。

uv 已经可以处理这些事情：

- 安装 Python 版本
- 查找 Python 解释器
- 创建虚拟环境
- 管理依赖
- 固定项目 Python 版本
- 运行项目命令

仍然适合保留 pyenv 的情况：

- 老项目已经大量依赖 `pyenv local`
- 需要非常细的 Python patch 版本矩阵
- 团队工具链明确要求 pyenv
- 需要测试多个历史 Python 版本

如果没有这些需求，可以选择：

```text
卸载 pyenv，使用 uv 统一管理
```

卸载 pyenv 后，应从 shell 配置中删除类似内容：

```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```

常见位置：

```text
~/.zprofile
~/.zshrc
~/.bash_profile
~/.profile
```

### 推荐的目录结构

一个普通 Python 项目可以长这样：

```text
my-project/
  .python-version
  .venv/
  pyproject.toml
  uv.lock
  README.md
  src/
    my_project/
      __init__.py
  tests/
    test_basic.py
```

其中：

```text
.python-version：记录项目 Python 版本
.venv：项目独立虚拟环境
pyproject.toml：项目元数据和依赖声明
uv.lock：锁定依赖版本，保证环境可复现
```

`.venv` 不应该提交到 Git。

`.gitignore` 中应包含：

```gitignore
.venv/
__pycache__/
.pytest_cache/
.ruff_cache/
```

### 推荐的日常工作流

新建项目：

```bash
mkdir demo
cd demo
uv init
uv python pin 3.12
uv add requests
uv run python main.py
```

进入已有项目：

```bash
cd demo
uv sync
uv run pytest
```

添加依赖：

```bash
uv add pandas
```

添加开发工具：

```bash
uv add --dev pytest ruff
```

格式检查：

```bash
uv run ruff check .
```

运行测试：

```bash
uv run pytest
```

### 常见问题

#### 为什么不直接用 macOS 自带 Python

macOS 自带 Python 或系统相关 Python 可能被系统工具依赖，不适合用来安装项目包。

修改它可能导致系统工具、脚本或权限行为变得混乱。

#### 为什么不直接用 Homebrew Python

Homebrew Python 可以用，但不建议把项目依赖直接装进去。

Homebrew 可能随着 `brew upgrade` 更新 Python 小版本，这会影响全局安装的包。

更好的方式是：

```bash
uv venv
uv add package-name
```

#### 遇到 externally-managed-environment 怎么办

这通常说明你正在尝试往受管理的 Python 环境里直接安装包。

不要使用：

```bash
pip install package-name
```

改用项目虚拟环境：

```bash
uv venv
uv pip install package-name
```

或使用 uv 项目依赖：

```bash
uv add package-name
```

#### 要不要激活虚拟环境

可以激活：

```bash
source .venv/bin/activate
```

但不是必须。

推荐用：

```bash
uv run python main.py
uv run pytest
```

这样命令会自动在项目环境中运行。

#### Python 版本应该选哪个

普通新项目建议选择当前稳定且生态兼容好的版本，例如 Python 3.12 或 3.13。

如果你依赖的数据科学、机器学习、音视频、浏览器自动化等库还没有完全适配最新 Python，优先选更稳的版本。

例如：

```bash
uv python install 3.12
uv python pin 3.12
```

### 最终推荐

对于大多数 Mac 用户，最佳方案是：

```text
安装 Homebrew
安装 uv
使用 uv 安装 Python
每个项目创建 .venv
用 uv add / uv sync / uv run 管理项目
全局 CLI 工具用 uv tool
不要污染系统 Python 或 Homebrew Python
```

最常用命令汇总：

```bash
brew install uv
uv python install 3.12
uv init
uv python pin 3.12
uv add requests
uv run python main.py
uv add --dev pytest ruff
uv run pytest
uv tool install ipython
```

一句话总结：

```text
Mac 上现代 Python 环境的最佳实践是：Homebrew 管工具，uv 管 Python 和项目，每个项目一个 .venv。
```

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

如下的常用git设置

要设置你的代理软件的本地监听ip端口

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890

#如果只是单次在终端使用代理键入以下命令，关闭终端会话则清理代理
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890

#设置默认主分支
git config --global init.defaultBranch main
```

有时候需要关闭ssl验证

```bash
git config --global http.sslVerify false
```

Git默认对大小写不敏感，也就是说，将一个文件名某个字母做了大小写转换的修改Git是忽略这个改动的，导致在同步代码时候会出现错误，所以建议把Git设置成大小写敏感

```bash
#如果需要全局配置，加 --global
git config --global core.ignorecase false
```

## 安装maven

### SDKMAN安装(推荐)

安装 Maven

```
sdk install maven
```
👉 自动帮你配好 `M2_HOME`、PATH，比 Homebrew 干净很多

设置默认

```
sdk default maven 3.9.9
```

验证

```
mvn -v
```

⚠️ 国内环境优化（关键）

编辑：

```
~/.m2/settings.xml
```

加入阿里云镜像：

```
<mirrors>  
  <mirror>  
    <id>aliyun</id>  
    <mirrorOf>*</mirrorOf>  
    <name>Aliyun Maven</name>  
    <url>https://maven.aliyun.com/repository/public</url>  
  </mirror>  
</mirrors>
```

### 安装包安装

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

#### 无限重置试用期方法

[参考链接](https://youwu.today/blog/jetbrains-evaluate-reset/#%E8%AF%95%E7%94%A8%E8%BF%87%E6%9C%9F%E4%BF%AE%E5%A4%8D---202122-%E7%89%88%E6%9C%AC%E4%B9%8B%E5%89%8D)

针对Idea版本为2021.2.2及之前的版本,我们可以使用`IDE Eval Reset`插件无限重置为30天试用

如果超过30天没有打开Idea我们可以通过如下的方法进入Idea,试用授权文件位于程序配置目录下的 _eval_ 文件夹.程序配置目录如下:

```properties
windows：`%userprofile%/AppData/Roaming/JetBrains/产品名版本号`

macos: `~/Library/Application Support/JetBrains/产品名版本号`

linux: `~/.config/JetBrains/产品名版本号`
```

将程序配置路径下的 _eval_ 文件夹 重命名或者删除后，重启程序即可。


### 常用插件

- jclasslib


## 安装mysql

### 使用官网包安装

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

### 使用Homebrew（推荐）

1. 安装Homebrew（如果尚未安装）

```bash

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. 安装MySQL

```bash
# 更新Homebrew
brew update

# 安装MySQL（默认为ARM版本）
brew install mysql

# 或者安装指定版本（如MySQL 8.0）
brew install mysql@8.0
```

3. 更新Homebrew

```bash
# 启动MySQL服务(推荐)
brew services start mysql

# 或者手动启动（不注册为服务）
mysql.server start
```

4. 重设密码

```bash
ALTER USER 'root'@'localhost'
IDENTIFIED BY '你的新密码';

FLUSH PRIVILEGES;
```

## 安装tomcat

👉 **现代 Java 架构 = Spring Boot + 内嵌 Tomcat + 容器化（Docker）  
👉 外部 Tomcat = 历史产物（除非维护老系统）**

所以外部安装tomcat是针对老系统

### SDKMAN安装(推荐)



### 安装包安装

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

### 推荐方案（首选）：用 nvm 管理 Node.js

> 参考https://nodejs.org/en/download

#### 1. 安装 nvm

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash
```

如果你是 zsh，再执行：

```
source ~/.zshrc
```

如果没生效，把下面这段加到 `~/.zshrc`：

```
export NVM_DIR="$HOME/.nvm"  
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

然后再执行一次：

```
source ~/.zshrc
```

#### 2. 安装 Node LTS

```
nvm install --lts  
nvm alias default lts/*
```

#### 3. 验证

```
which node  
node -v  
npm -v
```

这时 `which node` 应该类似：

```
/Users/你的用户名/.nvm/versions/node/v20.x.x/bin/node
```

---

## 你现在最适合的处理方式

### 安装包安装(不推荐)

访问官网：https://nodejs.org/en/

下载后长期支持版下一步安装

安装完成后，打开终端，输入 npm -v 和 node -v两个命令,如出现版本信息，说明安装成功。

打开Mac 终端，配置全局环境变量输入`vim .bash_profile`按i进入编辑状态，打开之后添加一行代码：`PATH=$PATH:/usr/local/bin/`并保存，重新打开终端，输入node，就会进入node环境。control+c 按两次退出node环境。

## 安装redis

### 通过homebrew方式安装

前置检查

```bash
brew -v
uname -m
```

安装redis

```bash
brew update
brew install redis
```

启动redis

```bash
# 方式一:后台启动(推荐)
brew services start redis
# 方式二:前台启动
redis-server /opt/homebrew/etc/redis.conf

```

验证redis是否启动成功,如果返回```PONG```就说明成功

```bash
redis-cli ping
```

卸载redis方法

```bash
brew services stop redis
brew uninstall redis
rm -rf /opt/homebrew/var/db/redis
rm -rf /opt/homebrew/etc/redis.conf

```

## 安装OpenClaw

### 通过一键命令安装(推荐)

本质也是通过npm(pnpm)安装

> 链接: https://openclaw.ai/

前提是需要安装nodejs(包括nvm npm pnpm)

### 常用命令

快速设置(运行入门程序)

```
openclaw onboard
```

状态查看

```
openclaw gateway status
```

打开仪表盘

```
openclaw dashboard
```

打开TUI界面

```
openclaw tui
```

配置

```
openclaw configure
```


## 安装codex

> 链接:
> 1) 官网 https://openai.com/zh-Hans-CN/codex/
> 2) 快速开始  https://developers.openai.com/codex/quickstart

推荐通过APP方式或CLI方式安装,如果通过CLI方式安装建议通过homebrew安装,其次才是npm安装


### cc-switch插件

安装

```
brew tap farion1231/ccswitch
brew install --cask cc-switch
```

更新

```
brew upgrade --cask cc-switch
```

如果后面要卸载

```
brew uninstall --cask cc-switch
brew untap farion1231/ccswitch
```

## 安装Docker

### OrbStack(推荐)

```
brew install --cask orbstack
```

### Docker Desktop(官方)

```
brew install --cask docker
```

### Colima(极客)

```
brew install colima docker
colima start
```