---
{"modified":["2026-02-06","2026-02-05"],"dg-publish":true,"permalink":"/Boxes/win、ubuntu 安装node.js、opencode、ohmyopencode/","dgPassFrontmatter":true}
---


# windows系统powershell
在 PowerShell 中安装 Node.js，最推荐的方法是使用 **Winget**（Windows 自带的包管理器，无需手动去官网下载安装包）。

## 1. 一键安装 Node.js (LTS 稳定版)
请以 **管理员身份** 运行 PowerShell，然后复制并执行以下命令：
```PowerShell
winget install OpenJS.NodeJS.LTS
```

---
## 2. 刷新环境变量 (非常重要)

安装完后，你不需要重启电脑，只需要在当前窗口执行这一行命令，让刚才的安装立即生效：
```
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

---
## 3. 验证是否安装成功

接着输入这两行，如果看到版本号，就说明 Node.js 已经彻底搞定了：
```
node -v
npm -v
```


## 4.使用 PowerShell 安装 OpenCode CLI

```
# 使用 npm 全局安装
npm i -g opencode-ai
```

> **注意：** 如果安装过程中提示权限不足，请右键点击 PowerShell 选择“以管理员身份运行”。

---
## 5.验证安装
```
opencode --version
```

如果看到版本号，说明 CLI 已经安装成功。

## 6.安装ohmyopencode
在opencode中发送：
```
Install and configure oh-my-opencode by following the instructions here:
https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/docs/guide/installation.md
```


# WSL Ubuntu
在 WSL Ubuntu 中安装 **OpenCode** 和 **Oh My OpenCode** 会比 Windows 原生环境更加顺滑，因为 Linux 环境与这些工具的底层逻辑兼容性更好。

---
***！！！总脚本！！！***
```
# 安装 nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc

# 安装最新的 LTS 版本
nvm install --lts
node -v # 确认版本
# 使用 npm 全局安装
npm i -g opencode-ai
```

***！！！配置model后发给opencode：！！！***
```
Install and configure oh-my-opencode by following the instructions here:
https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/docs/guide/installation.md
```

---
以下是详细的安装步骤：
## 1. 基础环境准备 (Node.js)

OpenCode CLI 需要 Node.js。在 Ubuntu 中，建议使用 `nvm` 来管理版本。

```
# 安装 nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc

# 安装最新的 LTS 版本
nvm install --lts
node -v # 确认版本
```

---

## 2. 安装 OpenCode CLI

根据官方文档，在终端运行：

```
# 使用 npm 全局安装
npm i -g opencode-ai
```

安装完成后，输入 `opencode --version` 验证。

---

## 3. 安装 Oh My OpenCode (高级套件)

在opencode中发送：
```
Install and configure oh-my-opencode by following the instructions here:
https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/docs/guide/installation.md
```


# 快捷配置命令：

你可以直接使用 `ulw` (Universal Language Wrapper) 模式来启动自动化任务：
```
# 在项目目录中启动并开启 Ultrawork 模式
opencode . --ultrawork
```


# 常用功能指令对照表：

|**功能**|**指令/操作**|**说明**|
|---|---|---|
|**切换模式**|`Tab` 键|在 **Plan** (规划) 和 **Build** (构建) 模式间切换|
|**全自动执行**|输入 `ulw`|代理将自主完成分析、编码和测试|
|**协同工作**|`/start-work`|启动 Prometheus 引擎，指挥子代理团队|
|**查看工具**|`opencode mcp list`|列出当前连接的所有 MCP 服务|

---

# WSL 特别注意事项

- **文件系统权限：** 建议将代码放在 WSL 的文件系统内（如 `~/projects`），而不是通过 `/mnt/c/` 访问 Windows 磁盘，这样 `git-master` 等 MCP 工具的运行速度会快得多。
    
- **环境变量：** 如果需要使用高级模型（如 Claude Opus 4.5），请在 `~/.bashrc` 中导出 API Key：
    
    `export ANTHROPIC_API_KEY="your_key_here"`
