# Codex

## 1、Codex是什么
Codex 是 OpenAI 推出的 AI 编程智能体（AI Coding Agent），可以帮助开发者完成编程相关工作。
它不仅能够生成代码，还能够：
- 阅读项目代码
- 理解项目结构
- 修改已有代码
- 修复 Bug
- 编写测试代码
- 执行命令
- 自动完成开发任务
## 2、Codex 与 ChatGPT 的区别
### 2.1、ChatGPT
主要负责：
- 回答问题
- 讲解知识
- 生成代码示例

- 辅助学习

特点：告诉你怎么做。

### 2.2、Codex

主要负责：
- 阅读项目
- 修改代码
- 自动开发功能
- 自动修复问题
- 执行开发任务

特点：直接帮你做。

## 3、为什么学习 Codex？

AI时代的软件开发正在发生变化：

以前：
	程序员自己写代码

现在：
	程序员提出需求
	↓
	AI完成大部分编码
	↓
	程序员负责审核和优化

未来：
	会使用AI开发的人，将比只会手写代码的人效率更高。

## 4、Codex 的使用形态

随着 OpenAI 不断完善 AI 编程生态，Codex 目前主要有四种使用方式：
- Codex CLI
- Codex 网页版
- Codex 插件
- Codex App

虽然底层能力相同，但适用于不同开发场景。

## 5、Codex、Claude Code区别

👉 Codex 更强
因为：

- GPT 对中文理解更好
- 需求分析能力强
- 前后端全栈能力强
- MCP生态发展很快
- OpenAI官方投入最大

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621165656641.png" alt="image-20260621165656553" style="zoom:33%;" />

## 6、Codex的安装

### 6.1、前置准备之Git

下载地址：https://git-scm.com/?hl=zh-cn
安装步骤：一直下一步即可
验证：git -v

### 6.2、前置准备之Node.js
下载地址：https://nodejs.org/zh-cn
安装步骤：一直下一步即可
验证：node -v

### 6.3、前置准备之ChatGPT
#### 6.3.1、ChatGPT注册
【1】科学上网
【2】准备一个邮箱：网易邮箱即可
【3】注册ChatGPT地址：https://chat.openai.com/auth/login

#### 6.3.2、手机端ChatGPT使用
注册appleid：https://account.apple.com/account
注册后，找到个人信息，地址修改为美国

美国地址生成器：美国地址生成器：https://cn.americaaddress.com/
手机登录这个appleid，就可以从AppStore下载chatgpt了
PS: 手机要登录chatgpt，手机科学上网

#### 6.3.3、ChatGPT充值
【1】iPhone 登录美区 Apple ID（App Store 里登，不是设置 iCloud）
打开 App Store → 右上角头像 → 退出旧 ID → 登录美区 ID

【2】打开支付宝
【3】支付宝左上角定位 → 改成「美国/任意城市」

【4】搜索 - 出境 - 惠出境

【5】找到打牌礼卡-精选打牌折扣

【6】选择AppStore & ITunes USA

【7】下拉页面中告知如何兑换

【8】付款后，订单页直接显示兑换码，同时发你邮箱
【9】App Store 兑换（钱进美区 Apple ID 余额），粘贴兑换码 → 兑换 → 余额
到账

【10】美区 App Store 下载官方 ChatGPT
【11】ChatGPT App 内订阅 Plus

【12】选 $20/month → 确认支付（自动扣 Apple ID 余额）
【13】输美区 Apple ID 密码 → 订阅成功

### 6.4、Codex的安装
下载地址：https://openai.com/zh-Hans-CN/codex/
怎么确认它真的装上了 ？ 开始菜单搜Codex.

## 7、Codex的卸载

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621165949425.png" alt="image-20260621165949368" style="zoom:33%;" />

资源管理器地址栏直接粘贴下面的地址找到后进行删除：
```shell
%USERPROFILE%\.codex
%APPDATA%\Codex
%LOCALAPPDATA%\Codex
%TEMP%\codex*
```



## 8、设置沙箱环境
<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621170047321.png" alt="image-20260621170047265" style="zoom:33%;" />

Set up Agent sandbox：
这是 Codex 最核心的功能。
点击 Set up 后，它会配置一个本地沙箱环境，让 Codex 能：

- 读取项目代码
- 修改文件
- 运行命令
- 调试程序
- 帮你开发 Java、Spring Boot、Python 等项目
## 9、多任务并行处理
Codex最大的特点之一：

支持同时开启多个任务（Task）进行工作。
例如：
- 任务1：开发登录功能
- 任务2：修复支付Bug
- 任务3：编写接口文档
- 任务4：优化数据库SQL

这些任务可以同时运行。每个任务拥有独立的上下文环境。
特点：

- 互不干扰
- 独立执行
- 独立查看结果
- 独立审批

这是很多Vibe Coding工具暂时不具备的能力。

## 10、任务状态管理

Codex会自动显示任务状态。
- 进行中
- 等待批准
- 已完成
## 11、界面基础功能实操
### 11.1、新对话
快捷键 ```ctrl+n```
### 11.2、搜索

Codex里的 搜索对话就是帮你在所有历史记录里，按关键词快速找回以前的聊天、任务、代码和文件。

快捷键 ```ctrl + g```

### 11.3、重命名对话

给聊天改个好认的标题，内容不变，只是方便你快速找到、分清不同任务。
重命名就是你手动把标题改成更短、更清楚、自己好懂的名字
只改名字，不改聊天内容、不改文件、不丢记录
双击对话即可完成重命名操作。

### 11.4、归档
归档 = 把 “做完、暂时不用” 的对话从主列表藏起来，但不删除、内容永久保留，以后随时能找回。
可取消归档

### 11.5、权限管理

在对话框下方，会有下拉菜单选项为权限管理
Codex的权限控制是围绕沙箱展开的，会将当前工作空间（项目文件夹）作为沙箱进行管理
- 默认权限
- 自动审查
- 完全访问权限
#### 11.5.1、请求批准
在默认权限下，Codex具有对沙箱（项目文件夹）具有读写权限
限制：
- 不能修改沙箱外的文件
- 禁止联网（Codex 云端）

如果Codex需要访问沙箱外的文件或者联网需求，会进行提权操作，这是需要人工审核的。

#### 11.5.2、替我审批

自动审查模式下，Codex会调用模型，对提权操作进行安全审查：
- 低风险：直接放行
- 高风险：人工审查
#### 11.5.3、完全访问权限
注意：Codex开启了完全访问权限，会无视沙箱的限制，可以在电脑上进行一切操作，这是危险操作！
### 11.6、模型选择
- 推理：根据任务复杂度，选择模型思考强度
- 模型切换：GPT-5.5 / GPT-5.4 / 其他模型
- 速度：标准 | 快速，快速会增加token消耗
### 11.7、剩余额度
在左下角设置，可以查看剩余额度，注意：这里有2个限额，5小时和周限额，两个限额任意一个到达上
限，都不可以继续使用。但是两个限额都有重置时间，重置后可以还原到100%（考虑切换其他模型）

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621170653701.png" alt="image-20260621170653638" style="zoom:33%;" />

### 11.8、添加照片和文件

如果你需要增加图片和文件作为参考资料，也可以点击权限管理左侧的加号（+）

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621170727679.png" alt="image-20260621170727619" style="zoom:33%;" />

## 12、引导干预
Steer 是 Codex 专属的实时控制、动态引导功能，核心作用是在模型生成内容 / 代码的过程中，人
工介入调整方向、纠正偏差，全程把控输出结果。

当观察到 Codex 执行过程中出现以下问题，立即停止自动执行，使用 Steer 人工引导：

1. 理解偏差：曲解需求、功能逻辑和预期不符
2. 方向跑偏：代码思路、实现方式偏离既定目标
3. 内容错误：出现语法错误、逻辑漏洞、冗余代码
4. 格式不符：输出样式、排版、注释、命名不满足要求

![image-20260621171019290](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/c648727e-2ff8-59ff-b05f-88a704b1d96c.png)

## 13、AI编程工作模式

**普通模式（默认模式）**

- 执行逻辑：用户直接输入开发需求，AI跳过沟通、规划环节，立刻根据需求直接编写、修改代码。
- 优缺点：响应速度快，适合简单代码编写；但面对复杂任务时，容易出现需求理解偏差、代码逻辑
  漏洞、不符合项目整体规划等问题。
- 执行链路：用户提出需求 → AI直接输出代码

**计划模式（进阶专业模式）**

- 核心定义：一种偏向工程化、规范化的AI编程工作模式。区别于普通模式的“直接执行”，该模式新
  增需求澄清、方案规划、用户确认环节，先定方案再写代码，降低开发出错成本。
- 完整执行流程：
  ○ 步骤1：提出需求：用户向AI描述本次编程任务；
  ○ 步骤2：需求澄清：AI针对模糊、缺失的信息，主动向用户提问，补齐需求细节（技术栈、功能
  细节、约束条件、兼容要求等）；
  ○ 步骤3：生成详细计划：AI结合完整需求，制定结构化开发方案，包含整体架构、开发步骤、代
  码模块划分、风险预案等；
  ○ 步骤4：用户确认：用户审核开发计划，可直接通过、提出修改意见；
  ○ 步骤5：落地执行：计划确认无误后，AI正式按照方案完成编码、重构、排错等任务。

最佳使用场景

- 首次搭建项目
- 重大重构（框架迁移、技术栈升级）
- 复杂 Bug 修复
- 多步骤任务

## 14、注释

- 切换至右侧浏览器查看模式，打开目标文档 / 页面；
- 点击工具栏中的注释按钮，激活标注工具；
- 支持分区域标注：鼠标框选页面任意区块、段落、图文区域，即可在对应区域添加专属注释、笔记。

<img src="https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/e6d7dde3-e1a0-5b48-a9d2-5c2989e1b268.png" alt="image-20260621171327043" style="zoom:33%;" />

## 15、插件

插件的作用，是让 Codex 连接外部平台和工具。不同版本、账号和系统环境下可见插件可能不同
提示词：

```txt
请制作一支 10 秒、16:9 的高燃短视频，主题是 “Codex 超强插件盘点”。整体风格科技感、高级感、快节奏，快速闪现 HyperFrames、Remotion、Figma、Build Web Apps、Build iOS Apps、Test Android Apps、Spreadsheets、Presentations、Computer Use、Browser Use 的代表画面，用卡片切换、界面飞入、关键词闪现和强转场，突出做视频、做动画、做产品、做网站、做 App、做表格、做 PPT、接管电脑、控制浏览器，“看完就想立刻试” 的激动感。
```

## 16、自动化

Codex 的自动化，本质是让 AI 按你设定的计划，在后台定时 / 持续跑重复开发任务，不用你手动触
发，解放日常机械劳动。
特点：

- 定时任务：像 “AI 版定时待办”，每天 / 每周固定时间自动执行指令（如 “早 9 点检查代码规
范”）。
- 长期后台运行：支持跨天 / 周的长任务，中途可暂停、唤醒、续跑，保留完整上下文。
- 无人值守：你下班 / 睡觉时，它自动干活，完成后发报告或自动归档。

要跑 Codex 自动化，必须保持 Codex App 打开、电脑开机、不睡眠，否则定时任务到点不会自己
跑。

## 17、Codex安装Skills

找一个生成ppt的skills —— PPT Master：https://github.com/hugohe3/ppt-master
【1】先把这个skills下载，并解压
【2】需要新创建一个项目：codexdemo05
【3】项目下需要创建一个目录：.codex
【4】里面再创建skills目录
【5】然后把解压的skills文件夹放在skills目录下
【6】在codex中新建会话，选择codexdemo05项目
【7】录入提示词：

```txt
根据以下内容生成毕业答辩PPT：

论文题目：《基于 SpringBoot+Vue 的在线图书商城管理系统》

专业：计算机科学与技术
学生：韩梅梅
指导教师：李雷

要求：
1. 生成16页答辩PPT
2. 风格简洁专业，符合本科毕业答辩场景
3. 每页包含标题和要点
4. 自动设计目录页、过渡页和结束页
5. 适当加入流程图、架构图、数据图表建议
6. 内容逻辑符合毕业答辩顺序

答辩结构：
第1页：封面
第2页：目录

第3页：研究背景与意义
第4页：国内外研究现状
第5页：需求分析

第6页：系统总体设计
第7页：系统架构设计
第8页：数据库设计

第9页：核心功能实现
第10页：关键技术介绍
第11页：核心代码逻辑说明

第12页：系统测试
第13页：测试结果分析

第14页：创新点与亮点
第15页：总结与展望

第16页：致谢

要求：生成ppt格式的文件，不要网页版本
```



## 18、MCP

Codex 本身原生工具能力有限，插件生态割裂；MCP 作为中间标准化层，解耦模型与外部资源 。
添加MCP服务器：

![image-20260621171810451](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621171810495.png)

先去高德开放平台注册：https://lbs.amap.com/，

| 图示                                                         |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20260621171945754](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621171945789.png) | ![image-20260621172003521](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621172003553.png) |
| ![image-20260621172012086](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621172012159.png) | ![image-20260621172022026](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621172022069.png) |

这里选web服务

![image-20260621172031817](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img2/20260621172031860.png)
