# 1. OpenCode介绍

## 1.1. OpenCode介绍

Opencode是一个开源的AI Agent编程助手，是当前AI编程工具领域最活跃的开源项目之一，其内置免费的模型，也可以连接任意提供商的任意模型，包括 Claude , GPT, Gemini 等更多，它提供终端界面、桌面应用和 IDE 扩展等多种使用方式，帮助您在终端、桌面端或IDE 编写代码。

OpenCode官网：[http://opencode.ai/zh](http://opencode.ai/zh)

OpenCode文档地址：[http://opencode.ai/docs/zh-cn](http://opencode.ai/docs/zh-cn)

## 1.2. OpenCode安装

在windows中使用OpenCode，可以直接在Window中安装node.js，使用npm方式安装opencode，具体操作如下：

```
#安装opencode
npm install -g opencode-ai

#查看版本
opencode -v

#卸载
npm uninstall -g opencode-ai
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6f9a42a8-40f6-5ff8-b949-ca904e5230b7.jpg)

为了在Windows中更好的使用命令操作opencode和项目目录，这里建议在Window中安装WSL（Windows Subsystem for Linux，适用于Linux的Windows子系统），基于WSL 安装OpenCode。后续我们将基于WSL安装OpenCode ，WSL 提供更出色的文件系统性能、完整的终端支持，以及与 OpenCode 所依赖的开发工具的良好兼容性。

### 1.2.1. Windows 安装WSL

WSL（Windows Subsystem for Linux，适用于Linux的Windows子系统）是一项由微软开发的功能。它允许开发者在Windows操作系统上直接运行原生的Linux二进制可执行文件，而无需传统的虚拟机或双系统启动。

简单来说，它就像在Windows内部嵌入了一个完整的Linux系统内核，让你可以：

* 在Windows上使用Linux的命令行工具（如Bash、grep、ssh）。
* 运行Linux应用程序和脚本。
* 同时访问Windows和Linux的文件系统。

与虚拟机相比，WSL启动更快、资源占用更少，且能与Windows系统无缝集成，非常适合开发、学习和运维工作。

按照如下步骤安装WSL，首先使用管理员运行powershell：

**1) 检查可用的Linux发行版(需要打开VPN)**

> vpn-科学上网工具推荐：https://www.zionladdern.com/register/w45zwqrDlsKfwozChMOGwoXConPDk8OVwpTCjsKFwpnCu8KaeMOWw5jCmcKHwofDisKJwqbCpMKhwqXCn8KLwrXClcK5/

> vpn-科学上网工具使用参考：https://cloud.fynote.com/share/d/PRLPXg7

```
wsl --list --online
```

**2) 安装或更新WSL2 Linux内核**

访问“[https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package”，下载并安装](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package”，下载并安装) wsl\_update\_x64.msi（64位系统），安装完成后重启电脑。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/62c44d26-0acc-5c22-8956-ed6d34d0b8b8.jpg)

**3) 确保WSL 2设置为默认版本**

```
# 设置WSL2为默认版本

wsl --set-default-version 2
```

**4) 安装Linux 发行版**

```
wsl --install -d Ubuntu
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6bface04-2658-5f00-8147-6fd17d88801c.jpg)

输入用户名和密码（zs/123456）：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/fce0a6ec-0814-56d7-95c4-f0b253c43b06.jpg)

安装完成后，可以在window中打开cmd，输入wsl 来启动对应的Ubuntu系统：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1280365c-3be5-5a1c-90de-0f737c353c5b.jpg)

WSL 可以通过 /mnt/ 目录访问你的所有 Windows 文件，例如：C: 盘 → /mnt/c/、D: 盘 → /mnt/d/，其他盘符以此类推。

### 1.2.2. 通过WSL中安装OpenCode

window中打开cmd，输入“wsl”启动linux系统，按照如下步骤安装OpenCode。

**1) 安装必要依赖**

```
# 更新本地软件包列表，如果执行慢可以跳过

sudo apt update

# 2. 安装所需的基础开发工具包（包含 gcc, g++, make 等）

sudo apt install build-essential -y

# 3. 安装 CMake、Curl、vim

sudo apt install cmake curl vim -y
```

**2) 安装node.js**

在该linux系统中安装node.js,这里我们安装node.js 22.22.1版本。访问“[https://nodejs.cn/en/download”，选择对应的Linux系统和Nodejs版本，查看安装nodejs命令：](https://nodejs.cn/en/download”，选择对应的Linux系统和Nodejs版本，查看安装nodejs命令：)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/02bf823e-4e49-56a1-ad0e-93bd602a8146.jpg)

在Ubuntu终端执行如下命令：

```
# Download and install nvm:

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

# in lieu of restarting the shell

\. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:

nvm install 22

# Verify the Node.js version:

#Should print "v22.22.1".
node -v

# Verify npm version:

# Should print "10.9.4".

npm -v

```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a2ee488b-9857-5566-9160-5cfd5c67786f.jpg)

**3) 安装OpenCode**

```
npm install -g opencode-ai
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/d5238f75-7770-5393-b12c-0f514cbac79b.jpg)

**4) 在Ubuntu系统中设置OpenCode环境变量**

```
# 编辑 bash 配置文件

nano ~/.bashrc

#在文件末尾添加以下行
export PATH="$PATH:/home/zs/.opencode/bin"

#使环境变量生效
source ~/.bashrc
```

保存并退出（按 Ctrl+X，然后 Y，再 Enter），然后执行“source ~/.bashrc”使环境变量生效。

### 1.2.3. 启动OpenCode

首先打开cmd ，输入 wsl，进入Ubuntu系统，然后可以按照如下两种方式启动OpenCode。

* **桌面引用+WSL服务器：**

如果你希望使用 OpenCode 桌面应用，同时在 WSL 中运行服务器，在 WSL 中启动服务器，添加 --hostname 0.0.0.0 以允许外部连接：

```
opencode serve --hostname 0.0.0.0 --port 4096
```

在桌面应用中连接到 [http://localhost:4096，如果localhost不能访问可以在Ubuntu系统中查找该linux系统的ip（hostname](http://localhost:4096，如果localhost不能访问可以在Ubuntu系统中查找该linux系统的ip（hostname) -I） ,然后使用 http://<wsl-ip>:4096访问。

* **Web客户端+WSL:**

要在 Windows 上获得最佳的 Web 体验，在 WSL 终端中运行 opencode web（非在 PowerShell 中运行），即可自动打开Window浏览器访问到OpenCode。

```
opencode web --hostname 0.0.0.0
```

### 1.2.4. 迁移已安装的 Ubuntu 到其他盘

默认情况下，WSL 会将发行版（包括 Ubuntu）安装在 C 盘的用户目录下，我们可以通过如下方式将其迁移到其他盘。这里将安装好的Ubuntu系统迁移到D盘D:\\WSL\\Ubuntu目录中。按照如下步骤设置即可。

**1) 导出当前 Ubuntu 发行版**

打开 Windows PowerShell（以管理员身份运行)，将当前系统导出为一个压缩包（例如，导出到 D 盘）：

```
wsl --export Ubuntu D:\ubuntu_backup.tar
```

注：Ubuntu是您的发行版名称（可通过 wsl -l -v查看），D:\\ubuntu\_backup.tar是导出的目标路径和文件名。

**2) 注销当前 Ubuntu 发行版**

执行如下命令会删除 C 盘中的原始系统文件，但不会影响导出的备份文件：

```
wsl --unregister Ubuntu
```

**3) 将 Ubuntu 导入到新位置**

将备份文件导入到你希望安装的目录（例如 D 盘的 WSL文件夹）：

```
#在D盘创建D:\WSL\Ubuntu 目录，然后执行如下命令
#特别注意，该步骤如果卡住，重启Windows系统再次执行即可
wsl --import Ubuntu D:\WSL\Ubuntu D:\ubuntu_backup.tar --version 2
```

参数说明：

* Ubuntu：发行版名称（可保持原名或自定义）
* D:\\WSL\\Ubuntu：目标安装目录（请确保此文件夹存在或提前创建）
* D:\\ubuntu\_backup.tar：上一步导出的备份文件路径
* --version 2：指定使用 WSL 2（如果你的系统默认是 WSL 2，可省略）

**4) 设置默认用户**

导入后系统默认以 root 用户登录。要恢复原来的普通用户，请执行：

```
ubuntu config --default-user <用户名>
```

这里的“用户名”就是指的前面创建的zs。

**5) 启动/退出wsl**

```
#启动Ubuntu系统
wsl

#退出
exit
```

### 1.2.5. 附-Windows彻底卸载WSL

卸载WSL操作步骤如下：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/9760b442-d790-5949-8eb9-c92c2e4222eb.jpg)

**1) 关闭所有wsl实例**

```
#管理员身份打开PowerShell，执行如下命令，该命令强制终止所有WSL进程
wsl --shutdown
```

**2) 注消所有Linux发行版**

```
#查看已安装的发行版
wsl --list --verbose

#逐个注销发行版（wsl --unregister <发行版名称>，替换<发行版名称>为实际名称）
wsl --unregister Ubuntu
```

**3) 卸载WSL核心组件**

```
wsl --uninstall
```

**4) 清理残留文件**

```
# 删除用户配置文件

Remove-Item -Force "$env:USERPROFILE\.wslconfig" -ErrorAction SilentlyContinue

# 删除旧版WSL1目录（如有）

Remove-Item -Recurse -Force "$env:LOCALAPPDATA\lxss" -ErrorAction SilentlyContinue

# 删除WSL2全局数据

Remove-Item -Recurse -Force "C:\ProgramData\Microsoft\Windows\WSL" -ErrorAction SilentlyContinue
```

还可以手动删除以下文件夹中的残留文件：

* C:\\Users\\<用户名>\\AppData\\Local\\Packages\\CanonicalGroupLimited.Ubuntu\*
* C:\\ProgramData\\Microsoft\\Windows\\开始菜单\\程序\\适用于 Linux 的Windows 子系统

**5) 验证卸载结果**

```
wsl --list --all
```

以上命令返回空列表或提示"Windows Subsystem for Linux has no installed distributions."表示卸载成功。

## 1.3. OpenCode使用示例

浏览器访问：[http://localhost:4096/，选择“/mnt/d/opencode\_project/my\_python\_project”，该目录需要提前创建。](http://localhost:4096/，选择“/mnt/d/opencode_project/my_python_project”，该目录需要提前创建。)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/f331f9cc-b203-50fe-b2ce-5d4254705db7.jpg)

### 1.3.1. /init

初始化项目，在当前目录创建 [AGENTS.md](http://AGENTS.md) 文件，可以让OpenCode更好的识别和导航你的项目，扫描当前目录的代码结构，并生成一个用于记录项目信息的 [AGENTS.md](http://AGENTS.md) 文件。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/57869cfd-1949-5107-bd8d-d4382ebef3f0.jpg)

对话创建一个python 俄罗斯方块游戏，稍后会自动创建项目并启动访问。

```
给我创建一个python项目，该项目是俄罗斯方块游戏，要求有前端页面，启动后可以直接在页面中进行游戏
```

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a58321a5-c6f0-5592-b9ce-e9b38e6d7f69.jpg)

### 1.3.2. /undo

撤销对话中的最后一条消息或者操作，可以多次运行 /undo 来撤销多次修改。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/1d0f0246-b79e-5d4f-94b2-5cbb2201ed41.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/724174ca-aa9e-5748-8e1e-5990f63a95d0.jpg)

### 1.3.3. /redo

重做之前撤销的消息或操作，仅在使用／undo 后可用。目前桌面版本没有/undo命令提示，可以输入命令直接执行。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/181bd62c-675a-5a46-9e71-2718f7060c1f.jpg)

# 2. OpenCode 配置

## 2.1. 模型

OpenCode支持超过75家LLM提供商，并且具备运行本地模型的能力。大多数热门提供商已预置，可以在Web页面中搜索并添加提供商凭据。与OpenCode配合良好且擅长代码生成和工具调用的模型如下：GPT 5.2、GPT 5.1 Codex、Claude Opus 4.5、Claude Sonnet 4.5、Minimax M2.1、Gemini 3 Pro。

如下是通过WebUI配置deepseek模型：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/6396b4d1-e12e-536a-bd48-b86aeb46574b.jpg)

## 2.2. [规则文件-AGENT.md](http://规则文件-AGENT.md)

[AGENTS.md](http://AGENTS.md)是 OpenCode 中一个核心的配置文件，其作用是为 AI 助手提供针对特定项目的自定义指令和上下文。它类似于 Cursor 编辑器中的“规则（Rules）”功能，旨在让 AI 深度理解你的项目结构、技术栈、编码规范和工作流程，从而提供更精准、更符合项目约定的辅助。

在 OpenCode 中运行 /init命令，AI 会自动扫描分析当前项目的结构和内容，生成一个初步的、适合本项目的 [AGENTS.md](http://AGENTS.md)文件。后续随着对话的进行，AI会自动更新补充该文件。

例如继续在项目中进行对话：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7e289df7-f1df-5c8c-b7b4-8ae122bd1b0c.jpg)

[后续打开AGENT.md](http://后续打开AGENT.md)可以看到内容改变：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/8f00e676-8ede-5130-8787-63513fbfe12a.jpg)

特别注意：[AGENTS.md](http://AGENTS.md)并非写给人类读者看的普通项目文档，而是专门写给 OpenCode 内置的 AI 模型（LLM）的“操作指南”。通过阅读这个文件，AI 能够了解项目的整体架构、关键目录的用途、团队遵循的代码规范（如命名约定、框架使用方式、Monorepo 工作区导入规范等），从而在代码补全、重构、调试和回答问题时，输出更符合项目实际情况的内容。

## 2.3. Agent

OpenCode中有两种主要Agent:主Agent和子Agent。主Agent包括 Build和Plan两种，子Agent是主Agetn可以调用来执行特定任务的专业助手，由主Agent根据任务自动调用，也可以在消息中@提及手动调用。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/382b6726-f620-5d2a-878d-1887a3d060f1.jpg)

主Agent和子Agent核心功能如下：

| **Agent名称**  | **类型**  | **核心功能**                                                                    |
| -------------------- | --------------- | ------------------------------------------------------------------------------------- |
| **Build**      | 主Agent         | 默认代理，拥有全部工具权限，用于需要文件操作和系统命令的完整开发工作。                |
| **Plan**       | 主Agent         | 受限代理，默认禁止文件编辑和Bash 命令，专用于代码分析、规划和建议，而不进行实际修改。 |
| **general**    | 子Agent         | 通用代理，拥有完整工具权限（除todo外），用于研究复杂问题、执行多步骤任务或并行工作。  |
| **explore**    | 子Agent         | 只读代理，无法修改文件，用于快速搜索文件、查找代码关键字或回答关于代码库的问题。      |
| **compaction** | 主Agent（隐藏） | 系统代理，自动将长上下文压缩为摘要。                                                  |
| **title**      | 主Agent（隐藏） | 系统代理，自动生成会话标题。                                                          |
| **summary**    | 主Agent（隐藏） | 系统代理，自动创建会话摘要。                                                          |

* **Plan使用**

Plan模式用于安全分析与规划。在创建的项目中选择Plan模式，然后输入“我不希望保留flask方式，请给我去除掉”，可以看到项目中并没有实际删除对应的文件，而是给出方案。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/ee9e6ca5-e1be-5238-90ac-9574d63b6220.jpg)

* **general使用**

切换到Build Agent模式使用general，general用于执行复杂任务。例如输入“
@general 请在当前目录下生成一个python代码，该代码中有python的加减乘除方法和main方法，main方法中给出一些示例使用这些方法”,可以看到通过该子Agent按照要求创建了对应代码。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/cb54b4e7-4eff-52fd-97c7-f9539e549bda.jpg)

* **explore使用**

explore 代理会快速执行搜索操作，并返回只读的结果列表。如下，输入“@explore 给我解释下@gradio\_[app.py](http://app.py) 这个文件中的页面是如何运行出来的，然后方块操作的逻辑在什么位置控制？”

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/da093962-8d1d-5824-9d80-fbace27164ee.jpg)

注意：可以使用@符号引用指定的python文件。

## 2.4. 命令

OpenCode 的命令系统允许你通过快捷指令执行常见任务。命令分为内置命令和自定义命令。

### 2.4.1. 内置命令

OpenCode中的内置核心命令如下：

| **命令**   | **功能描述**                               |
| ---------------- | ------------------------------------------------ |
| **/init**  | 初始化一个新项目或会话，通常用于设置工作环境。   |
| **/undo**  | 撤销上一步操作。在代码编辑或文件操作后非常有用。 |
| **/redo**  | 重做被撤销的操作。                               |
| **/share** | 分享当前会话、代码片段或上下文。                 |
| **/help**  | 显示帮助信息，列出所有可用的命令及简要说明。     |

### 2.4.2. 自定义命令

自定义命令允许你将常用的、多步骤的提示词封装成一个快捷指令，从而高效执行重复性任务。

**1\. 创建方式**

我们可以在“~/.opencode/commands”[目录下创建.md](http://目录下创建.md)文件定义命令，md文件结构如下：

Markdown 文件结构示例（[newpy.md](http://newpy.md)）：

```
---
description: 创建一个新的Python脚本文件
agent: build
---
请帮我创建一个新的Python脚本文件，文件名为：**$ARGUMENTS.py**

文件内容要包含：
1. 标准的Python文件头注释（包含创建日期和简短描述）
2. 主函数 `main()`
3. 标准的 `if __name__ == "__main__":` 代码块
4. 基本的命令行参数解析示例
5. 适当的日志记录配置示例

这是一个实用工具脚本的模板，请直接创建这个文件。
```

以上MD文件分为两部分：

* Frontmatter (YAML格式)：定义命令的元数据（description， agent等）。
* 正文：命令的核心模板，即运行时会发送给AI的提示词。

注意事项：

* 在TUI中输入 /newpy即可运行此命令，定义的md的名称就是自定义命令的名称。
* 模版中可以使用$ARGUMENTS占位符向命令传递参数，也可以使用位置参数$1、$2...来访问各个参数，调用命令时后面跟上参数即可。
* 如果创建了同名的自定义命令，它将覆盖对应的内置命令。

**2\. 核心配置选项**

定义命令的元数据包含如下：

| **选项**        | **是否必须** | **是否必须**                                                 |
| --------------------- | ------------------ | ------------------------------------------------------------------ |
| **template**    | 是                 | 定义命令执行时发送给AI的完整提示词，在Markdown文件中，正文即模板。 |
| **description** | 是                 | 命令的简要描述，在TUI中输入/时会显示。                             |
| **agent**       | 否                 | 指定执行此命令的代理（如build, plan）。未指定则使用当前代理。      |
| **subtask**     | 否                 | 设为true可强制命令在子代理中运行，避免污染主会话上下文。           |
| **model**       | 否                 | 为此命令指定专用的AI模型，覆盖默认模型。                           |

**3\. 案例**

如下是定义一个智能代码审查的命令，对指定文件或变更进行针对性的代码审查。按照如下步骤实现。

**1)** [**创建myreview.md**](http://创建myreview.md)**文件**

进入“~/.opencode”目录中创建“commands”目录，[然后在该目录中创建myreview.md](http://然后在该目录中创建myreview.md)文件。

```
cd .opencode/
mkdir commands && cd commands

#在commands目录中创建myreview.md文件写入如下内容
```

[myreview.md](http://myreview.md):

```
---
description: 审查特定文件的代码质量
agent: plan
subtask: true
---
请审查以下文件的代码：
**@$ARGUMENTS**

请从以下角度提供审查意见：
1.  **代码风格**：是否符合项目规范（如命名、格式）？
2.  **潜在缺陷**：有无空指针、资源未释放、逻辑错误？
3.  **安全性**：有无注入、硬编码密钥、不当的权限检查？
4.  **性能**：有无低效循环、重复计算、可优化的数据库查询？
5.  **可维护性**：函数/类是否职责单一？注释是否清晰？

**注意**：仅提供分析和建议，不要直接修改代码。
```

**2) 浏览器访问opencode，使用myreview 命令**

直接在对话中输入“/myreview”+ 代码文件（参数），可以看到对对应的代码文件进行审核。重启Opencode后可以在对话框中输入/myreview 看到自动提示，不重启也可以使用，但是看不到自动提示。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a98c68f8-f7f3-58b5-89d2-504e85144754.jpg)

## 2.5. 工具

### 2.5.1. 内置工具

OpenCode 为 LLM 提供了一系列内置工具，使其能够在您的代码库中执行各种操作。内置工具主要如下：

| **工具**      | **工具作用**                                                   |
| ------------------- | -------------------------------------------------------------------- |
| **bash**      | 在项目环境中执行任意的Shell命令（如 npm install, git status）。      |
| **edit**      | 通过精确的字符串替换来修改现有文件。这是LLM 编辑代码的主要方式。     |
| **write**     | 创建新文件或完全覆盖现有文件的内容。                                 |
| **read**      | 读取指定文件的内容，支持按行范围读取大文件。                         |
| **grep**      | 使用正则表达式在代码库中搜索文件内容。                               |
| **glob**      | 使用Glob模式（如 **/*.js）来查找匹配的文件路径。                     |
| **list**      | 列出指定目录下的文件和子目录，支持使用Glob 模式过滤。                |
| **patch**     | 对文件应用补丁（diff）。                                             |
| **skill**     | 加载项目中的SKILL.md文件并在对话中返回其内容。                       |
| **todowrite** | 在复杂的编码会话中创建和管理待办事项列表，帮助LLM 跟踪多步骤任务。   |
| **webfetch**  | 获取并返回指定网页的内容。                                           |
| **websearch** | 使用Exa AI在互联网上搜索信息。                                       |
| **question**  | 允许LLM 在执行任务期间向用户提问，用于收集偏好、澄清需求或做出决策。 |

所有工具默认启用且无需权限，LLM 会根据任务需求自动判断并调用相应的工具。如下：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4f4e8a85-8cb2-53ff-ad0f-3a39eb05b571.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bedfb46d-4648-5596-8c6b-966fe9a0d24c.jpg)

链接地址：[https://news.sina.com.cn/w/2026-03-31/doc-inhswxzu2352038.shtml](https://news.sina.com.cn/w/2026-03-31/doc-inhswxzu2352038.shtml)

### 2.5.2. 自定义工具

自定义工具允许你定义任意的函数供 LLM 调用，极大地扩展了 OpenCode 的能力。自定义工具方式需要在“.opencode/tools/”目录下创建ts文件，在该文件中定义工具，示例如下：

假设我们在对应目录中创建一个ts文件如下（.opencode/tools/query-db.ts）：

```
import { tool } from "@opencode-ai/plugin";

export default tool({
  description: "查询项目数据库", // 工具描述，LLM据此决定是否调用
  args: { // 定义工具参数
    sql: tool.schema.string().describe("要执行的 SQL 查询语句"),
  },
  async execute(args) { // 工具的执行逻辑
    // 在这里编写实际的数据库查询代码
    const result = `已执行查询: ${args.sql}`;
    return result; // 返回结果给 LLM
  },
});
```

以上自定义工具示例中注意：

1) 一个ts文件可以导出多个工具，工具名格式为<文件名>\_<导出名>,如以上工具名为query-db\_tool （文件名：query-db,导出名：tool，两者用下划线隔开）。
2) 使用 @opencode-ai/plugin包提供的 tool()辅助函数来定义工具，它能提供类型安全和参数验证。
3) description参数用于给LLM理解工具。
4) args参数定义参数描述。
5) execute：工具的核心逻辑，接收args参数并返回结果，结果通常是字符串。
6) 可以在.opencode/tools目录下创建多个ts文件，也可以在一个ts文件中定义多个工具。
7) 工具逻辑可以用任何语言编写。您只需在 execute函数中调用相应的脚本或命令即可。
8) 如果自定义工具与内置工具（如 bash）同名，自定义工具将覆盖内置工具。除非有意替换，否则建议使用独特的名称

如下示例演示在OpenCode中自定义工具，我们这里在一个ts文件中定义两数相加、相乘、统计代码行数多个工具，工具使用ts或者python实现。

**1) 创建 .opencode/tools目录**

在.opencode/中创建tools目录：

```
cd ~
mkdir -p .opencode/tools
```

**2)** [**编写add.py**](http://编写add.py)**文件**

[add.py](http://add.py)文件内容如下，实现两数相加功能。

```
"""加法工具的实现脚本"""
import sys

def add_numbers(a, b):
    """将两个数字相加"""
    return a + b

if __name__ == "__main__":
    # 从命令行参数读取
    if len(sys.argv) != 3:
        print("用法: python add.py <数字1> <数字2>")
        sys.exit(1)

    try:
        a = float(sys.argv[1])
        b = float(sys.argv[2])
        result = add_numbers(a, b)
        print(f"结果: {result}")
    except ValueError:
        print("错误: 请输入有效的数字")

```

**3) 编写 count\_py\_**[**lines.py**](http://lines.py)**代码**

count\_py\_[lines.py](http://lines.py)文件实现指定python文件或者某个目录下python文件中代码行、注释行、空行统计。

```
#!/usr/bin/env python3
"""Python代码行数统计工具"""

import sys
import os
import json
from pathlib import Path

def count_file_lines(filepath):
    """统计单个Python文件的行数"""
    try:
        # 读取文件内容
        with open(filepath, 'r', encoding='utf-8') as f:
            lines = f.readlines()

        total = len(lines)
        code = 0
        comment = 0
        blank = 0

        for line in lines:
            stripped = line.strip()
            if not stripped:
                blank += 1
            elif stripped.startswith('#'):
                comment += 1
            else:
                code += 1

        return {
            "success": True,
            "file": os.path.basename(filepath),
            "path": filepath,
            "total": total,
            "code": code,
            "comment": comment,
            "blank": blank
        }

    except Exception as e:
        return {"success": False, "error": f"无法读取文件: {str(e)}"}

def count_directory_lines(dirpath, recursive=False):
    """统计目录中所有Python文件的行数"""
    try:
        if recursive:
            # 递归搜索所有子目录
            py_files = list(Path(dirpath).rglob("*.py"))
        else:
            # 非递归搜索当前目录
            py_files = list(Path(dirpath).glob("*.py"))

        if not py_files:
            return {"success": False, "error": f"在 {dirpath} 中未找到Python文件"}

        results = []
        total_stats = {"total_files": 0, "total": 0, "code": 0, "comment": 0, "blank": 0}

        for py_file in py_files:
            # 跳过隐藏文件和缓存目录
            if any(part.startswith('.') for part in py_file.parts) or '__pycache__' in py_file.parts:
                continue

            try:
                # 读取文件内容
                with open(py_file, 'r', encoding='utf-8') as f:
                    lines = f.readlines()

                file_total = len(lines)
                file_code = 0
                file_comment = 0
                file_blank = 0

                for line in lines:
                    stripped = line.strip()
                    if not stripped:
                        file_blank += 1
                    elif stripped.startswith('#'):
                        file_comment += 1
                    else:
                        file_code += 1

                relative_path = str(py_file.relative_to(dirpath))
                file_result = {
                    "file": py_file.name,
                    "path": relative_path,
                    "total": file_total,
                    "code": file_code,
                    "comment": file_comment,
                    "blank": file_blank
                }

                results.append(file_result)

                total_stats["total_files"] += 1
                total_stats["total"] += file_total
                total_stats["code"] += file_code
                total_stats["comment"] += file_comment
                total_stats["blank"] += file_blank

            except Exception as e:
                results.append({
                    "file": py_file.name,
                    "path": str(py_file.relative_to(dirpath)),
                    "error": str(e)
                })

        # 返回结果
        return {
            "success": True,
            "directory": dirpath,
            "recursive": recursive,
            "total_stats": total_stats,
            "files": results
        }

    except Exception as e:
        return {"success": False, "error": f"无法处理目录: {str(e)}"}

def format_result(result):
    """格式化结果"""
    if not result.get("success"):
        return f"❌ 错误: {result.get('error', '未知错误')}"

    if "file" in result:  # 单个文件结果
        r = result
        output = []
        output.append(f"📄 文件: {r['file']}")
        output.append(f"📈 总行数: {r['total']}")
        output.append(f"  ├─ 代码行: {r['code']} ({r['code'] / r['total'] * 100:.1f}%)")
        output.append(f"  ├─ 注释行: {r['comment']} ({r['comment'] / r['total'] * 100:.1f}%)")
        output.append(f"  └─ 空行: {r['blank']} ({r['blank'] / r['total'] * 100:.1f}%)")
        return "\n".join(output)

    else:  # 目录结果
        output = []
        output.append(f"📁 目录: {result['directory']}")
        output.append(f"📄 文件数: {result['total_stats']['total_files']}")
        output.append(f"📈 总行数: {result['total_stats']['total']}")

        if result['total_stats']['total'] > 0:
            total = result['total_stats']['total']
            # 计算百分比
            code_pct = result['total_stats']['code'] / total * 100
            comment_pct = result['total_stats']['comment'] / total * 100
            blank_pct = result['total_stats']['blank'] / total * 100

            output.append(f"  ├─ 代码行: {result['total_stats']['code']} ({code_pct:.1f}%)")
            output.append(f"  ├─ 注释行: {result['total_stats']['comment']} ({comment_pct:.1f}%)")
            output.append(f"  └─ 空行: {result['total_stats']['blank']} ({blank_pct:.1f}%)")

        return "\n".join(output)

def main():
    """主函数"""
    if len(sys.argv) < 2:
        print("错误: 需要指定文件或目录路径")
        print("用法: python count_py_lines.py <路径> [--recursive]")
        sys.exit(1)

    path = sys.argv[1]
    recursive = "--recursive" in sys.argv

    if not os.path.exists(path):
        result = {"success": False, "error": f"路径不存在: {path}"}
    elif os.path.isfile(path):
        if not path.endswith('.py'):
            result = {"success": False, "error": f"不是Python文件: {path}"}
        else:
            result = count_file_lines(path)
    else:
        result = count_directory_lines(path, recursive)

    print(format_result(result))

if __name__ == "__main__":
    main()

```

**4) 创建 calc.ts文件**

calc.ts文件中定义多个工具，每个工具调用对应的逻辑，这些逻辑可以是ts实现的逻辑，也可以是其他脚本实现的逻辑。

calc.ts内容如下：

```
import { tool } from "@opencode-ai/plugin";

// 1. 加法工具
export const add = tool({
  description: "将两个数字相加",
  args: {
    a: tool.schema.number().describe("第一个数字"),
    b: tool.schema.number().describe("第二个数字"),
  },
  async execute(args, context) {
    const scriptPath = "/home/zs/.opencode/tools/add.py";

    try {
      const result = await Bun.$`python3 ${scriptPath} ${args.a} ${args.b}`.text();
      return result.trim();
    } catch (error) {
      return `计算错误: ${error}`;
    }
  },
});

// 2. 乘法工具
export const multiply = tool({
  description: "将两个数字相乘",
  args: {
    a: tool.schema.number().describe("第一个数字"),
    b: tool.schema.number().describe("第二个数字"),
  },
  async execute(args, context) {
    const result = args.a * args.b;
    return `${args.a} × ${args.b} = ${result}`;
  },
});

// 3. 统计指定python文件或目录路径下的Python代码行数工具
export const countPyLines = tool({
  description: "统计指定python文件或目录路径下的Python代码行数（递归统计子目录）",
  args: {
    path: tool.schema.string().describe("要统计的Python文件绝对路径或目录绝对路径"),
  },
  async execute(args, context) {
    const scriptPath = "/home/zs/.opencode/tools/count_py_lines.py";

    try {
      const result = await Bun.$`python3 "${scriptPath}" "${args.path}" --recursive`.text();
      return result;
    } catch (error: any) {
      return `执行Python脚本时出错: ${error.message || error}\n请确保Python脚本存在且可执行: ${scriptPath}`;
    }
  },
});
```

特别注意：

* 该ts代码中定义的scriptPath要指定python脚本文件所在的路径，即“/home/zs/.opencode/tools”路径中的脚本。
* 使用 Bun.$ 工具函数来运行 Python 脚本

**5) 重启OpenCode**

将文件上传到“/home/zs/.opencode/tools”路径中，可以先讲文件放在D盘中或者指定的一个目录中，然后在Ubuntu系统中将该路径中的文件复制到“/home/zs/.opencode/tools”路径中。最后重启OpenCode。

```
# 复制对应文件到/home/zs/.opencode/tools路径中

cd /home/zs/.opencode/tools/
cp /mnt/d/add.py /mnt/d/calc.ts /mnt/d/count_py_lines.py ./

#重启OpenCode
opencode serve --hostname 0.0.0.0 --port 4096
```

注意：将文件复制到“/home/zs/.opencode/tools/”路径中，每个python文件后有个\*号，表示该文件是可以操作的。

**6) 测试工具使用**

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/165f5700-382d-53c1-b4bb-b619b1c3cad9.jpg).

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a0b93b36-538b-5978-82f0-725690f40f1d.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/100d84d4-de2f-5252-812a-db161840a7a9.jpg)

### 2.5.3. 调用MCP服务器工具

OpenCode中也可以调用外部MCP服务器发布的工具，我们需要在“/home/zs/.opencode/opencode.json”文件中进行相关配置，示例如下：

```
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "name-of-mcp-server": {
      // ...
      "enabled": true,
    },
    "name-of-other-mcp-server": {
      // ...
    },
  },
}

```

注意：以上enabled 设置为 false 来禁用某个服务器，当你想临时禁用某个服务器而不将其从配置中移除时，这个选项非常有用。

如下演示在OpenCode中配置SpringAI 创建的MCP服务发布的工具。

**1) 准备SpringAI 对应的MCP工具**

可以参考SpringAI课程中MCP开发工具部分，这里通过SpringAI创建MCP 服务端，使用StreaableSSE 通信模式，对应的工具为getWeather。

**2) 创建opencode.json文件**

在“/home/zs/.opencode”目录下创建opencode.json文件，写入如下内容：

```
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-springboot-mcp": {
      "type": "remote",
      "url": "http://192.168.1.106:8090/mcp",
      "enabled": true
    }
  }
}
```

注意：以上url需要写成MCP Server所在服务端的ip。在“/home/zs/.opencode”目录下创建opencode.json文件配置的远程工具在opencode所有项目中都可以使用。也可以在对应的项目根目录下创建opencode.json文件配置MCP工具，该配置只能在该项目中使用。

**3) 重启OpenCode并测试**

```
#重启OpenCode
opencode serve --hostname 0.0.0.0 --port 4096
```

重启OpenCode后，可以在webui页面中看到MCP中有了配置的“my-springboot-mcp”，可以在对话中使用对应工具。

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/a086825b-ab1d-54e8-9ef4-985aa9d3c096.jpg)

对话：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/5d33bb5b-fb84-589c-96d5-6943cf545199.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c07c4a14-1250-5589-9a02-b167d47e5c86.jpg)

可以在MCP Server端看到对应的日志：![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/7acf76a8-3980-5f89-b611-c44da08b428b.jpg)

## 2.6. Agent Skill

OpenCode 中的 Skill是一个核心功能，允许你将常用的、复杂的操作指令封装成可复用的“技能包”，让 Agent在需要时按需调用，从而极大地提升工作效率和一致性。

Skill 是 OpenCode 代理的可复用行为模板,每个Skill是一个目录，[包含一个SKILL.md](http://包含一个SKILL.md)文件（提供Skill功能和对应执行指令给大语言模型），以及可选的脚本或资源。一个完整的Skill文件结构如下：

```
one_skill_dir
|----SKILL.md    #必须
|----scripts/       #可选
|----references/    #可选
|----assets/       #可选
```

以上文件解释如下：

**1)** [**SKILL.md**](http://SKILL.md)

必须指定的核心文件，该文件中包含技能相关的元数据和执行指令，采用“YAML元数据 + Markdown指令”的混合格式。

* YAML元数据区（位于文件顶部---内）：用于定义Skill的基本属性，元数据区包含name（英文，Skill的唯一标识符，如 hello\_world，名称必须为小写字母、数字和单连字符组合（如 git-release），且目录名需与此一致）、description（对Skill功能的简要描述，帮助AI理解何时调用此技能）、requires（可选，声明执行此技能所需依赖的工具列表，如 \[“bash”\]，）
* Markdown指令区：用自然语言清晰描述AI应如何执行任务。该部分核心原则是导AI“做什么”和“用什么工具做”，而非教授AI基础知识，内容可以包括任务触发条件、分步操作流程、所需调用的工具（如 echo, bash）、输出结果的格式或标准。

**2) 其他目录**

另外三个目录用于存放支持技能运行的资源，使Skill更强大、更独立。

* scripts/: 存放可执行的Shell、Python等脚本，供Skill指令调用。
* references/: 存放相关的规范文档、数据表等参考材料。
* assets/: 存放模板文件、示例图片、配置文件等静态资源。

### 2.6.1. 自定义Skill

OpenCode中创建Skill非常简单，只需要在“~/.opencode/skills”目录中创建对应skill的目录，[然后在该skill目录中创建SKILL.md](http://然后在该skill目录中创建SKILL.md)文件，编写skill的功能即可。

如下示例中创建一个SKILL 进行商品价格统计，该SKILL中会调用calc\_multiply和calc\_add工具。

**1) 创建对应目录**

创建“/home/zs/.opencode/skills/price-calculator”目录。

```
cd ~
mkdir -p /home/zs/.opencode/skills/price-calculator
```

**2)** [**准备SKILL.md**](http://准备SKILL.md)**文件**

进入到“/home/zs/.opencode/skills/price-calculator”目录，[创建SKILL.md](http://创建SKILL.md)文件，写入如下内容：

```
---
name: price-calculator
description: 计算商品总价，包括单价×数量+运费
---

## 我的职责

我是一个价格计算器，可以帮你计算购物总成本。

## 我将执行以下操作

当你提供以下信息时：
- 商品单价 (price)
- 商品数量 (quantity)
- 运费 (shipping)

我会：
1. 调用calc_multiply工具计算商品小计：单价 × 数量
2. 调用calc_add工具计算总价：小计 + 运费

## 如何使用

只需要告诉我："请计算总价，单价是 {价格}，数量是 {数量}，运费是 {运费}"

例如：
- "请计算总价，单价是 25，数量是 3，运费是 10"
- "帮我算一下，单价 99.9，买 2 个，运费 15"
```

**3) 重启OpenCode并测试SKILL**

```
#重启OpenCode
opencode serve --hostname 0.0.0.0 --port 4096
```

重启OpenCode后，可以在webui页面中进行如下对话：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/58b448e1-7b66-56f1-a1df-323164bb172e.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/15df1d81-f6e7-53ed-b0d7-360d66f4c6d6.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/4e66a075-89f7-5e27-9742-d1b929a9cb76.jpg)

### 2.6.2. ClawHub Skills

OpenCode中还可以通过ClawHub搜索公开、免费的Skill。ClawHub是OpenClaw的官方公共技能中心，完全免费开放且每个skill都有版本历史，地址：[https://clawhub.ai/skills。如果想为OpenClaw助手添加新功能，ClawHub是最简单的查找和安装技能的方式。此外，推荐一个Skills中国网站：https://hub.cocoloop.cn/](https://clawhub.ai/skills。如果想为OpenClaw助手添加新功能，ClawHub是最简单的查找和安装技能的方式。此外，推荐一个Skills中国网站：https://hub.cocoloop.cn/)

直接进入ClawHub官网搜索技能，找到对应技能名称再进行安装：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/dbfa97b2-5e5f-57b3-9ba5-4fcdd590b0f3.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/bdb5d2cf-a4e4-5abc-b704-97693328fbe3.jpg)

将下载好的SKILL技能包解压放入“~/.opencode/skills”目录下，然后重启OpenCode即可。

例如，搜索微信公众号文章 技能地址：[https://clawhub.ai/wuchubuzai2018/wechat-article-search，我们可以下载该SKILL](https://clawhub.ai/wuchubuzai2018/wechat-article-search，我们可以下载该SKILL) 后解压到D盘，将解压的内容上传至“~/.opencode/skills”目录下：

```
cd /home/zs/.opencode/skills/
mv /mnt/d/wechat-article-search-0.1.0 ./

#重启OpenCode
opencode serve --hostname 0.0.0.0 --port 4096
```

然后可以在对话中使用该SKILL：

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/376bd7ae-a314-52a5-8c8d-9c6ade12bd0b.jpg)

![image.png](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/26927c1f-7f7d-58c4-ad26-603416b4b741.jpg)

# 3. OpenCode 案例开发

## 3.1. 扫雷游戏

## 3.2. 2048游戏

## 3.3. 飞机大战游戏
