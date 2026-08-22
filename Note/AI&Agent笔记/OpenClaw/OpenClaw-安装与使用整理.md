# OpenClaw 安装与使用整理

> 来源：<https://cloud.fynote.com/share/d/ZAHaTUrAC>
> 说明：根据分享链接内容提取并整理为 Markdown 文档。

# 1. **OpenClaw介绍**

## 1.1. **OpenClaw介绍**

OpenClaw是一款在2026年初爆火的开源AI Agent框架，由奥地利开发者Peter Steinberger创建，它最大的创新在于将大语言模型从"只会聊天"的工具转变为"真正会做事"的管家，其能够直接操作计算机系统，执行跨平台跨应用的复杂任务，像人类一样操作电脑，管理文件、发送消息、浏览网页等。

2026年1月，项目最初以Clawdbot命名，1月27更名为Moltbot，1月29号最终定名为OpenClaw。

OpenClaw核心概念如下：

- Gateway（网关）：作为控制平面，负责路由消息、管理并发Agent会话、调用LLM API，默认端口18789。
- Agent Runner（执行引擎）：解析LLM输出的指令并触发具体的本地操作。
- Skills（技能）：功能扩展模块，通过Python、Node.js或Shell脚本扩展能力。
- Memory（记忆）：本地存储系统，记录用户习惯和交互历史。

OpenClaw使用场景如下：

1. 办公自动化：例如文件管理、邮件处理、日程管理、天气/股票生成日报。
2. 浏览器自动化：数据抓取、表单填写、内容发布。
3. 开发与运维：代码自动生成、Docker自动化运维、跨平台转发消息。

OpenClaw官网地址：<https://openclaw.ai/>

OpenClaw官网文档地址：<https://docs.openclaw.ai/zh-CN>

## 1.2. **OpenClaw安装介绍**

可以基于 Mac、Windows（需要 WSL2）、Linux 进行安装 OpenClaw，也可以基于 Docker 安装 OpenClaw。安装 OpenClaw 需要 Node.js 22.16+ 和 Git 环境。不同平台安装 OpenClaw 具体可以参考：<https://docs.openclaw.ai/zh-CN/install>

建议将 OpenClaw 安装在独立的一台机器中，这样可以避免自动操作电脑带来的风险。后续可在 VMware 中安装 Ubuntu 系统进行 OpenClaw 安装及操作。

# 2. **使用VMware安装Ubuntu系统**

## 2.1. **安装并配置VMware**

### **2.1.1. 安装VM并激活**

VMware Workstation 是一系列桌面 Hypervisor 产品，允许用户运行虚拟机。

双击 `VMware-workstation-full-16.0.0-16894299.exe` 安装包即可安装 VMware Workstation。

首次打开需要输入许可证，可以从如下许可证中选择一个输入：

> ZF3RO-FHED2-M80TY-8QYGC-NPKYF
>
> YF390-OHF8P-M81RQ-2DXQE-M2UT6
>
> ZF71R-DMX85-08DQY-8YMNC-PPHV8

### **2.1.2. 配置VMware网络环境**

想要安装的系统能连接网络，需要进行 VMware 网络环境配置。在 VMware 中，打开：

**编辑 -> 虚拟网络编辑器**

进行设置。

- **配置 VMnet1 仅主机模式**
- 点击 DHCP 设置，采用默认即可
- **配置 VMnet8 NAT 模式**
- 点击 DHCP 设置，采用默认即可

### **2.1.3. 配置本地网卡环境**

- 在网络连接中打开 VMnet1，右键 -> 属性 -> IPv4
- 在网络连接中打开 VMnet8，右键 -> 属性 -> IPv4

## 2.2. **VM安装Ubuntu系统**

### **2.2.1. Ubuntu系统下载**

这里基于 VMware 安装 Ubuntu 22.04 系统，系统下载地址：

<https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04/>

### **2.2.2. VM安装Ubuntu系统**

按照 VMware 安装向导配置虚拟机，完成相关参数设置后，启动并安装 Ubuntu 系统。

安装过程说明：

- 初次安装等待大约 10 分钟
- 重启后会安装相关依赖，大约 30 分钟后安装完成并再次重启
- 之后选择用户名，输入密码进入系统
- 可在系统中安装中文输入法
- 全部配置完成后重启系统即可

# 3. **OpenClaw安装及使用**

## 3.1. **OpenClaw安装及访问**

### **3.1.1. 安装OpenClaw**

进入 Ubuntu 系统，打开终端。

按照如下步骤安装 OpenClaw：

**特别提示：需要打开科学上网工具，否则后续安装访问一些网络可能无法访问。**

#### 1）安装必要依赖

```bash
# 更新本地软件包列表
sudo apt update

# 安装所需的基础开发工具包（包含 gcc, g++, make 等）
sudo apt install build-essential -y

# 安装 CMake、Curl、vim
sudo apt install cmake curl vim -y
```

注意：如果执行安装命令出现类似如下错误：

> 正在等待缓存锁：无法获得锁 /var/lib/dpkg/lock-frontend。锁正由进程 xxx（unattended-upgr）持有

可以多等待一会，或者执行：

```bash
sudo kill -9 xxx
```

杀掉对应进程后再次执行安装命令。

#### 2）安装 Node.js

OpenClaw 需要 Node.js 22+ 环境，这里安装 Node.js 22.22.1 版本。

可参考：<https://nodejs.cn/en/download>

在 Ubuntu 终端执行如下命令：

```bash
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

# in lieu of restarting the shell
. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:
nvm install 22

# Verify the Node.js version:
# Should print "v22.22.1".
node -v

# Verify npm version:
# Should print "10.9.4".
npm -v
```

#### 3）安装 Git

```bash
sudo apt install git -y
```

#### 4）安装 OpenClaw

```bash
npm install -g openclaw@latest
```

也可以执行上述命令安装最新版本。可用版本参考：

<https://github.com/openclaw/openclaw/releases>

#### 5）运行新手引导向导并安装服务

```bash
openclaw onboard --install-daemon
```

引导过程中建议选择：

- 确认继续个人使用配置：**Yes**
- 初始化设置模式：**QuickStart**
- 设置模型：**Skip for now**
- 供应商提供的模型：**All providers** -> **Keep Current**
- 外部通讯平台：**Skip for now**
- Skills：可以先跳过
- API Key：可以先不设置
- Hooks：全部启用
- 启动机器人方式：**在 TUI 中启动**

启用的 hooks 说明：

- `boot-md`：OpenClaw 网关启动时自动寻找并执行 `BOOT.md`
- `bootstrap-extra-files`：初始化时自动加载额外配置文件
- `command-logger`：记录执行命令的审计日志
- `session-memory`：保存会话状态或记忆

如果此时在命令行中输入“你好”，由于尚未配置大模型，会报错。

#### 6）OpenClaw设置大模型

可以新打开一个终端配置大模型。示例中使用通义千问模型。

打开：

`/home/zs/.openclaw/openclaw.json`

将如下内容覆盖或合并到对应位置：

```json
"models": {
  "mode": "merge",
  "providers": {
    "qwen": {
      "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1",
      "apiKey": "sk-xxxx....",
      "api": "openai-completions",
      "models": [
        {
          "id": "qwen-max",
          "name": "Qwen Max",
          "reasoning": false,
          "input": ["text"],
          "contextWindow": 128000,
          "maxTokens": 8192
        }
      ]
    }
  }
},
"agents": {
  "defaults": {
    "model": {
      "primary": "qwen/qwen-max"
    }
  }
}
```

相关地址：

- API Key：<https://bailian.console.aliyun.com/?tab=model#/api-key>
- Base URL：<https://bailian.console.aliyun.com/?tab=api#/api>
- 模型市场：<https://bailian.console.aliyun.com/?tab=model#/model-market/all>
- 模型价格：<https://bailian.console.aliyun.com/?tab=doc#/doc/?type=model&url=2987148>

#### 7）再次进入命令行对话测试

配置完成后，再回到命令行界面输入对话内容，即可得到模型回复。

不再使用命令行对话时，可以按 `Ctrl + C` 退出。

### **3.1.2. 浏览器访问OpenClaw**

可以通过浏览器访问 OpenClaw，在终端中执行：

```bash
# 运行此命令打开控制界面，可以查看访问链接
openclaw dashboard
```

运行后会自动打开浏览器访问 OpenClaw，也可以退出此终端。

## 3.2. **OpenClaw命令**

### 1）打开 OpenClaw WebUI 访问链接

```bash
openclaw dashboard
```

### 2）查看 OpenClaw 状态

```bash
openclaw status
```

### 3）Gateway 命令

```bash
# 启动网关，默认搭建好后通常就是启动状态
openclaw gateway

# 查看 gateway 状态
openclaw gateway status

# 停止 gateway
openclaw gateway stop

# 启动 gateway
openclaw gateway start

# 重启 gateway
openclaw gateway restart
```

### 4）查看实时日志

```bash
openclaw logs --follow
```

## 3.3. **OpenClaw设置模型**

### **3.3.1. 命令设置 minimax 相关模型**

MiniMax 是一个综合性 AI 模型平台，为用户提供文本、语音、视频、图像和音乐等多种类型的 AI 生成能力。在 OpenClaw 中设置时，可选择使用 minimax 的 MiniMax-M2.5 文本模型。

步骤如下：

1. 注册并获取 minimax API Key
   - 文档：<https://platform.minimaxi.com/docs/guides/models-intro>
   - API Key：<https://platform.minimaxi.com/user-center/basic-information/interface-key>

2. 通过 `openclaw config` 设置大模型

在终端执行：

```bash
openclaw config
```

然后在交互界面中选择配置大模型，找到 minimax 并设置默认模型。

配置完成后，刷新 OpenClaw 页面，可以看到当前对话使用的模型为 `MiniMax-M2.5`。

### **3.3.2. 配置文件设置 deepseek 模型**

也可以直接配置 `openclaw.json` 来设置默认模型，例如配置默认聊天模型为 `deepseek-reasoner`：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "deepseek": {
        "baseUrl": "https://api.deepseek.com",
        "apiKey": "sk-xxxxx",
        "api": "openai-completions",
        "models": [
          {
            "id": "deepseek-reasoner",
            "name": "deepseek reasoner",
            "reasoning": true,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 32000,
            "maxTokens": 64000
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-reasoner"
      },
      "models": {
        "deepseek/deepseek-reasoner": {
          "alias": "deepseek-r1"
        }
      }
    }
  }
}
```

注意：

- `agents.default.model.fallbacks` 可配置备用模型
- `agents.models` 中可以配置模型简化名称

配置完成后重启 gateway：

```bash
openclaw gateway restart
```

# 4. **OpenClaw工具**

## 4.1. **内置工具介绍**

OpenClaw 有很多内置工具，可参考：<https://docs.openclaw.ai/tools>

常见工具包括：

- apply_patch Tool：原子性修改单个或多个文件
- Brave Search：默认 `web_search` 提供商，需要付费
- Perplexity Sonar：`web_search` 的另一个提供商，需要付费
- Diffs：可视化文本差异
- PDFTool：读取 PDF 文档
- Exec Tool：执行 shell 命令，支持前台和后台执行
- Exec Approvals：真实主机执行命令的安全审批机制
- LLM Task：结构化输出，强制模型输出 JSON
- Lobster：运行类型化、可组合工作流的可选插件工具
- Reaction：在支持的平台上添加或移除表情回复

## 4.2. **配置操作系统文件功能**

OpenClaw 安装后，默认对话中对操作系统文件的访问是受限的。可以通过如下命令切换到完整工具集：

```bash
# 使用完整工具集
openclaw config set tools.profile full

# 设置执行主机为 gateway，表示本机
# 如果使用 docker 部署需要设置为 sandbox
openclaw config set tools.exec.host "gateway"

# 重启 gateway
openclaw gateway restart
```

以上命令等价于在 `/home/zs/.openclaw/openclaw.json` 的 `tools` 部分加入对应配置。

配置完成后，可直接通过对话让 OpenClaw 执行文件操作，例如：

```text
1. 在桌面上创建 my_dir 目录，我的桌面地址 "/home/zs/桌面"
2. 然后在该目录中创建 "data.txt" 文件
3. 在该文件中写入 "hello word"
4. 并在最后将 "data.txt" 这个文件重命名为 "hello.txt"
```

## 4.3. **配置网络搜索功能**

OpenClaw 默认支持的 Brave Search 和 Perplexity Sonar 都需要付费。可以通过配置 `openclaw-tavily` 插件来使用 Tavily 进行网络搜索。

Tavily 官网：<https://app.tavily.com/home>

注册后免费版每月提供 1000 次 API 调用。

插件参考：<https://github.com/framix-team/openclaw-tavily>

插件提供工具：

- `tavily_search`
- `tavily_extract`
- `tavily_crawl`
- `tavily_map`
- `tavily_research`

### 配置步骤

#### 1）获取 Tavily API Key

登录：<https://app.tavily.com/home>

#### 2）从源代码安装 `openclaw-tavily` 插件

```bash
git clone https://github.com/framix-team/openclaw-tavily.git ~/.openclaw/extensions/openclaw-tavily
cd ~/.openclaw/extensions/openclaw-tavily
npm install --omit=dev
```

#### 3）在 OpenClaw 中配置 Tavily API Key

打开 `openclaw.json`，在 `plugins.entries` 中加入：

```json
"openclaw-tavily": {
  "enabled": true,
  "config": {
    "apiKey": "tvly-dev-m...."
  }
}
```

并在 `plugins.allow` 中加入：

```json
"allow": [
  "openclaw-tavily"
]
```

#### 4）重启 OpenClaw gateway

```bash
openclaw gateway restart
```

## 4.4. **配置 Firecrawl 爬取网页功能**

Firecrawl 是 OpenClaw 中用于 `web_fetch` 工具的备用内容提取服务，适合处理 JavaScript 密集型网站或普通 HTTP 抓取困难的页面。

Firecrawl 官网：<https://www.firecrawl.dev>

注册后默认免费 500 credits。

在 `openclaw.json` 的 `tools` 部分添加：

```json
{
  "tools": {
    "web": {
      "fetch": {
        "firecrawl": {
          "apiKey": "fc....",
          "baseUrl": "https://api.firecrawl.dev",
          "onlyMainContent": true,
          "maxAgeMs": 172800000,
          "timeoutSeconds": 60
        }
      }
    }
  }
}
```

说明：

- 存在 API Key 时，`firecrawl.enabled` 默认为 `true`
- `maxAgeMs` 控制缓存时长，默认 2 天

配置完成后执行：

```bash
openclaw gateway restart
```

## 4.5. **配置OpenClaw操作浏览器**

OpenClaw 能操作所有主流 Chromium 浏览器（Chrome、Brave、Edge、Chromium），不支持 Firefox、Safari 等非 Chromium 内核浏览器。

本机模式中，OpenClaw 会按以下顺序自动检测：

`Chrome -> Brave -> Edge -> Chromium -> Chrome Canary`

### 1）Ubuntu 安装 Chrome 浏览器

```bash
cd /home/zs
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

### 2）通过与 OpenClaw 对话操作浏览器

安装完成后即可通过自然语言让 OpenClaw 打开浏览器、搜索内容、执行网页操作。

# 5. **聊天渠道**

## 5.1. **飞书配置OpenClaw**

飞书是字节跳动旗下的一站式 AI 工作平台与协作办公套件。

OpenClaw 通过飞书开放平台的 WebSocket 事件订阅与飞书机器人连接，无需暴露公网 Webhook URL。当用户在飞书中 @机器人或发送私聊消息时，事件会通过长连接推送给 OpenClaw 网关，再由相应智能体处理并回复。

相关文档：<https://docs.openclaw.ai/zh-CN/channels/feishu#快速开始>

### **5.1.1. 第一步 - 创建飞书应用**

访问飞书开发平台：<https://open.feishu.cn/app>

#### 1）创建应用

点击“创建企业自建应用”，填写应用名称、描述并选择图标。

#### 2）配置应用权限

在权限管理页面点击“批量导入”，粘贴如下 JSON：

```json
{
  "scopes": {
    "tenant": [
      "aily:file:read",
      "aily:file:write",
      "application:application.app_message_stats.overview:readonly",
      "application:application:self_manage",
      "application:bot.menu:write",
      "cardkit:card:write",
      "contact:user.employee_id:readonly",
      "corehr:file:download",
      "docs:document.content:read",
      "event:ip_list",
      "im:chat",
      "im:chat.access_event.bot_p2p_chat:read",
      "im:chat.members:bot_access",
      "im:message",
      "im:message.group_at_msg:readonly",
      "im:message.group_msg",
      "im:message.p2p_msg:readonly",
      "im:message:readonly",
      "im:message:send_as_bot",
      "im:resource",
      "sheets:spreadsheet",
      "wiki:wiki:readonly"
    ],
    "user": [
      "aily:file:read",
      "aily:file:write",
      "im:chat.access_event.bot_p2p_chat:read"
    ]
  }
}
```

#### 3）启动机器人能力（可选）

在“应用能力”中开启机器人能力并配置名称。

#### 4）配置事件订阅

在飞书开发平台中配置事件订阅。

#### 5）发布应用

在“版本管理与发布”页面创建版本，提交审核并发布。企业自建应用通常自动通过。

通过后，飞书电脑客户端中的“开发者小助手”会有提示。此时会生成配对码，后续需要使用。

### **5.1.2. 第二步 - 配置OpenClaw**

在 Ubuntu 客户端运行向导，添加飞书：

```bash
openclaw onboard
```

然后根据提示完成飞书渠道配置。

### **5.1.3. 第三步 - 启动并测试**

在飞书中找到创建的机器人进行对话，首次对话通常会返回一个配对码。

此时在 Ubuntu 终端执行：

```bash
openclaw pairing approve feishu <配对码>
```

配对完成后即可正常对话。

# 6. **OpenClaw Skills技能**

## 6.1. **什么是Skills**

Skills 是 OpenClaw 中用于添加新功能的模块。每个 Skill 是一个目录，包含一个 `SKILL.md` 文件，以及可选的脚本或资源。

Skills 允许用户通过自定义指令和工具增强 OpenClaw 的能力，例如处理问候、运行脚本、访问外部服务等。

### Skills 的价值

- 灵活添加能力：无需修改核心代码即可扩展能力
- 模块化管理：每个 Skill 独立存在，便于开发、测试和共享
- 提升效率：将常用任务封装为 Skill，让助手自动执行

### 一个完整 Skill 的目录结构

```text
one_skill_dir
|----SKILL.md    # 必须
|----scripts/    # 可选
|----references/ # 可选
|----assets/     # 可选
```

### 说明

#### 1）SKILL.md

必须的核心文件，采用 **YAML 元数据 + Markdown 指令** 的混合格式。

- YAML 元数据区：定义 `name`、`description`、`requires` 等
- Markdown 指令区：描述 AI 应如何执行任务

#### 2）其他目录

- `scripts/`：可执行脚本
- `references/`：规范文档、参考资料
- `assets/`：模板文件、示例图片、配置文件等

### Skills 的加载优先级

从高到低：

1. 工作区 Skill：`~/.openclaw/workspace/skills/`
2. 本地 Skill：`~/.openclaw/skills/`
3. 内置 Skill

当存在同名 Skill 时，高优先级覆盖低优先级。

## 6.2. **自定义Skill**

所有自定义安装的 Skills 位于工作区：

`/home/zs/.openclaw/workspace/skills`

### 自定义步骤

#### 1）环境与路径准备

```bash
mkdir -p ~/.openclaw/workspace/skills/你的技能名
```

#### 2）创建并编写 `SKILL.md`

例如创建一个名为 `file_organizer` 的技能：

```md
---
name: file_organizer
description: 自动整理指定目录下的文件，按扩展名分类到不同文件夹。
requires: ["bash"]
---

# 文件整理技能

当用户要求整理某个目录时，请执行以下操作：

1. 使用 `bash` 工具，在用户指定目录中创建 `Images`、`Documents`、`Archives` 子文件夹。
2. 将所有 `.jpg`、`.png` 文件移动到 `Images` 文件夹。
3. 将所有 `.pdf`、`.docx` 文件移动到 `Documents` 文件夹。
4. 将所有 `.zip`、`.tar.gz` 文件移动到 `Archives` 文件夹。
5. 操作完成后，向用户报告整理结果。
```

#### 3）添加辅助脚本（可选）

如逻辑复杂，可将命令写入 `scripts/` 中，然后在 `SKILL.md` 中调用。

#### 4）刷新技能列表并测试

创建完成后，重启 OpenClaw Gateway 服务，或在对话中提示刷新 Skill。

### **6.2.1. 自定义Skill案例 - HelloWorld**

#### 1）创建新文件夹

```bash
mkdir -p ~/.openclaw/workspace/skills/hello-world
```

#### 2）编写 `SKILL.md`

```md
---
name: hello_world
description: 一个简单的技能用于打招呼
requires: ["echo"]
---

# 打招呼技能

当用户请求问候时，使用“echo”工具回复：“你好——来自 hello-world Skill 的问候！”
```

#### 3）重启 OpenClaw Gateway

```bash
openclaw gateway restart
```

### **6.2.2. 自定义Skill案例 - file_search**

该技能可用于查找工作空间指定类型文件信息、最近修改文件、统计文件类型数量。

#### 1）创建目录

```bash
mkdir -p ~/.openclaw/workspace/skills/file-search
mkdir -p ~/.openclaw/workspace/skills/file-search/scripts
```

#### 2）编写 `SKILL.md`

可使用原文中的 `file_search` 示例，核心思路是：

- 不直接用 `find`
- 使用 Python 脚本进行文件搜索与统计

#### 3）准备 Python 脚本

原文提供了两个示例脚本：

- `search_files.py`
- `file_counter.py`

可直接复制到：

`~/.openclaw/workspace/skills/file-search/scripts`

#### 4）重启 Gateway

```bash
openclaw gateway restart
```

#### 5）测试技能

例如：

```text
使用 file_search skill 技能统计工作空间中文件类型及数量
使用 file_search skill 技能给我统计工作空间中 md 文件信息
使用 file_search skill 技能查找工作空间中最近修改的 python 文件有哪些？
```

## 6.3. **ClawHub Skills**

OpenClaw 还可以通过 ClawHub 搜索和安装公开免费的 Skill。

ClawHub 地址：<https://clawhub.ai/skills>

推荐的 Skills 中文网站：<https://hub.cocoloop.cn/>

### **6.3.1. ClawHub Skill安装及使用**

#### 安装 clawhub

```bash
npm i -g clawhub
```

#### 搜索、安装与更新示例

```bash
# ClawHub 搜索指定 Skill
clawhub search "china"

# ClawHub 安装对应 skill
clawhub install "china-weather"

# ClawHub 更新 skill
clawhub update "china-weather"
```

安装完成后，可以在工作空间 `~/.openclaw/workspace/skills` 中看到对应目录。

安装新 Skill 后，重启 Gateway：

```bash
openclaw gateway restart
```

如果需要删除技能：

- 直接删除对应目录
- 或执行：`clawhub delete [skill_name]`

### **6.3.2. ClawHub Skill使用示例**

#### 1）搜索微信公众号文章

Skill：`wechat-article-search`

地址：<https://clawhub.ai/wuchubuzai2018/wechat-article-search>

安装：

```bash
clawhub install "wechat-article-search"
cd /home/zs/.openclaw/workspace/skills/wechat-article-search
npm install cheerio
openclaw gateway restart
```

#### 2）聚合新闻

Skill：`news-aggregator`

地址：<https://clawhub.ai/httencn/news-aggregator>

安装：

```bash
clawhub install "news-aggregator"
openclaw gateway restart
```

#### 3）获取抖音热榜

Skill：`douyin-hot-trend`

地址：<https://clawhub.ai/franklu0819-lang/douyin-hot-trend>

安装：

```bash
clawhub install "douyin-hot-trend"
openclaw gateway restart
```

### 其他推荐

- 小红书全能助手：<https://clawhub.ai/hi-yu/xhs>
- B站热门视频监控：<https://clawhub.ai/Jacobzwj/bilibili-hot-monitor>
