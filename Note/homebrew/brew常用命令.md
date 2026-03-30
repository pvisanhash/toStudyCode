# Homebrew 常用命令速查

## 1. 基础信息

```bash
brew --version
```
查看 Homebrew 版本。

```bash
brew help
```
查看帮助。

```bash
brew config
```
查看当前 Homebrew 配置环境。

---

## 2. 搜索与查看包

```bash
brew search <关键词>
```
搜索 formula 或 cask。

示例：
```bash
brew search python
```

```bash
brew info <包名>
```
查看包详情、版本、依赖、安装路径等。

示例：
```bash
brew info git
```

```bash
brew list
```
列出已安装的包。

```bash
brew list --versions
```
列出已安装包及版本号。

```bash
brew leaves
```
查看手动安装的顶层包。

---

## 3. 安装与卸载

```bash
brew install <包名>
```
安装 formula。

示例：
```bash
brew install git
```

```bash
brew install --cask <包名>
```
安装图形应用或 cask。

示例：
```bash
brew install --cask iterm2
```

```bash
brew uninstall <包名>
```
卸载 formula。

```bash
brew uninstall --cask <包名>
```
卸载 cask。

```bash
brew reinstall <包名>
```
重新安装包。

---

## 4. 更新与升级

```bash
brew update
```
更新 Homebrew 自身以及软件仓库元数据。

```bash
brew outdated
```
查看哪些包可以升级。

```bash
brew outdated --verbose
```
查看更详细的可升级版本信息。

```bash
brew upgrade
```
升级所有可升级的软件包。

```bash
brew upgrade <包名>
```
只升级指定包。

示例：
```bash
brew upgrade git
```

```bash
brew upgrade --cask <包名>
```
升级指定 cask。

---

## 5. 清理与诊断

```bash
brew cleanup
```
清理旧版本安装包和缓存。

```bash
brew cleanup -n
```
预览将要清理的内容，不实际删除。

```bash
brew doctor
```
检查 Homebrew 环境是否有问题。

```bash
brew missing
```
检查依赖是否缺失。

---

## 6. 服务管理

```bash
brew services list
```
查看通过 Homebrew 管理的服务状态。

```bash
brew services start <服务名>
```
启动服务。

示例：
```bash
brew services start mysql
```

```bash
brew services stop <服务名>
```
停止服务。

```bash
brew services restart <服务名>
```
重启服务。

---

## 7. 常见实用命令

```bash
brew deps <包名>
```
查看某个包依赖哪些包。

```bash
brew uses <包名>
```
查看哪些包依赖这个包。

```bash
brew pin <包名>
```
锁定版本，防止升级。

```bash
brew unpin <包名>
```
取消锁定。

```bash
brew autoremove
```
自动删除不再需要的依赖。

---

## 8. Cask 常用命令

```bash
brew list --cask
```
列出已安装的 cask 应用。

```bash
brew search --cask <关键词>
```
搜索 cask 应用。

```bash
brew info --cask <包名>
```
查看 cask 详情。

---

## 9. 推荐使用习惯

日常维护常用这几个：

```bash
brew update
brew outdated
brew upgrade
brew cleanup
brew doctor
```

如果你只想快速看看系统里装了什么：

```bash
brew list
brew leaves
brew services list
```

---

## 10. 一个常见工作流

```bash
brew update
brew outdated
brew upgrade
brew cleanup
brew doctor
```

适合定期维护 Homebrew 环境。

---

## 11. 例子

### 安装 git
```bash
brew install git
```

### 安装 Redis 并启动
```bash
brew install redis
brew services start redis
```

### 查看哪些包过期
```bash
brew outdated --verbose
```

### 升级单个包
```bash
brew upgrade python@3.14
```

### 查看服务状态
```bash
brew services list
```

---

如果你愿意，还可以继续扩展这份文档，比如：
- Brew + Python 环境管理
- Brew + MySQL / Redis 常用操作
- Brew 常见报错排查
- Formula 和 Cask 的区别
