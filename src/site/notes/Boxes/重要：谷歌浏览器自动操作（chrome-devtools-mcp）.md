---
{"dg-publish":true,"permalink":"/Boxes/重要：谷歌浏览器自动操作（chrome-devtools-mcp）/","noteIcon":"","created":"2026-02-08T14:54:56.386+08:00","updated":"2026-02-08T16:17:38.638+08:00"}
---

# 教程：用 OpenCode 启动并使用 `chrome-devtools-mcp`（复用已登录的 Chrome）
（示例项目：多厂商 coding plan 套餐余额查询与 API 调用推荐）

## 1. 目标与原理
- `chrome-devtools-mcp` 是一个 MCP Server，让你的 coding agent（OpenCode 这类 MCP Client）可以控制并检查一个“真实的”Chrome 浏览器，用于可靠自动化、调试与性能分析。[6]
- 默认情况下，`chrome-devtools-mcp` 会启动一个**新的**Chrome 实例并使用专用 profile，这不利于复用你手动登录后的站点状态。[5]
- 为了复用你已经登录过的控制台页面，我们使用 **`--autoConnect`**：它会连接到你手动启动的 Chrome（Chrome 144+），并复用其默认 profile 的现有登录态与窗口/标签页。[2][5]

---

## 2. 准备工作（一次性）
### 2.1 确认 OpenCode 配置文件位置
- Linux/WSL 常见路径：`~/.config/opencode/opencode.json`（没有就创建）。[1]
- 你在 Windows 与 WSL2 的具体路径也可以继续沿用你现有的（你之前已说明各自的用户级配置目录）。

### 2.2 安全注意事项（建议必读）
- MCP Client 连接后，**可以检查、调试、修改**该浏览器实例里的任何数据与页面内容，因此不要在同一浏览器会话里暴露你不想分享给 agent 的敏感信息。[6]
- （补充）如果你改用 remote debugging 端口方式，会有“本机任何程序都可能连接并控制浏览器”的风险；不过本教程主线是 `--autoConnect`。[2]

---

## 3. Step-by-step：OpenCode 启动并接管“已登录 Chrome”的完整流程（方案 1：autoConnect）

### Step 1）配置 OpenCode：在 `opencode.json` 添加 MCP Server
在你的 `opencode.json` 里加入（或合并）如下配置，让 OpenCode 用本地命令拉起 MCP Server。[1]
同时加上 `--autoConnect` 参数来启用自动连接到你正在运行的 Chrome。[3][5]

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "chrome-devtools": {
      "type": "local",
      "command": ["npx", "-y", "chrome-devtools-mcp@latest", "--autoConnect"]
    }
  }
}
```

说明：OpenCode 作为 MCP Client，会按配置启动/管理该本地 MCP Server；仅“连上 server”本身不等于会立刻启动浏览器动作。[1]

---

### Step 2）手动启动 Chrome（必须你先开）
`autoConnect` 要求**用户先启动 Chrome**。[2]
如果你有多个 profile 在用，MCP Server 会连接到 Chrome 判定的**默认 profile**，并且能访问该 profile 下所有已打开窗口/标签页（这正是复用登录态的关键）。[2]

建议做法：
- 用你平时已登录各厂商控制台的那个 Chrome profile 打开浏览器，并保持这些控制台标签页处于已登录状态（或至少保证 cookie/session 未过期）。

---

### Step 3）在 Chrome 中启用 remote debugging（Chrome 144+）
在 Chrome（>= M144）中，进入：`chrome://inspect/remote-debugging` 并启用 remote debugging。[5]
然后按弹出的对话框 UI 选择允许/拒绝调试连接；后续要连接成功需要你点击 **Allow**。[5][2]

---

### Step 4）启动 OpenCode，并用“测试提示词”验证链路
在 OpenCode 中输入以下测试提示词，用于验证浏览器已能被控制并做一次性能 trace。[1][5]

```text
Check the performance of https://developers.chrome.com
```

当 MCP Server 尝试连接你运行中的 Chrome 时，会出现权限对话框请求许可；点击 **Allow** 后会打开网页并记录性能 trace（这是预期行为）。[2]

> 备注：MCP Server 会在 MCP Client 第一次调用“需要浏览器实例”的工具时，才会启动/连接并驱动浏览器；仅建立 server 连接本身不会自动启动浏览器操作。[1]

---

## 4. 示例项目：多厂商 coding plan 套餐余额查询与 API 使用推荐（用浏览器已登录态抓取）
你的目标是：因为你已经在 Chrome 中登录了多个厂商控制台的“额度/用量”页面，所以让 OpenCode 直接驱动这些页面，读取剩余额度并汇总。

### 4.1 典型自动化动作（工具能力）
你会高频用到这些工具类别：
- 导航类：`list_pages`、`select_page`、`navigate_page`、`wait_for` 等。[3]
- 调试/读取类：`evaluate_script`（从页面 DOM 读取文本/数值）、必要时 `take_screenshot` 辅助定位。[3]
- 输入类：`click`、`fill` 等（如果某家控制台需要点开某个面板）。[3]

### 4.2 推荐的执行策略（稳定、可复用）
1) **先列出当前所有标签页**，找到已经登录的“用量/额度”页面（避免重复登录）。[3][2]
2) 如果页面不在当前标签页中：用 `navigate_page` 直接跳转到用量 URL（仍会复用同一 profile 的登录态）。[3][2]
3) 用 `wait_for` 等待关键元素出现（比如“Remaining / Credits / Quota”所在的 DOM）。[3]
4) 用 `evaluate_script` 抽取数值并标准化成统一结构（例如：`{vendor, remaining, unit, reset_time}`）。[3]
5) 对多厂商结果做排序/打分，输出“下一次优先调用哪家 API”的建议（例如按剩余额度、是否临近重置、你自己的偏好权重）。

### 4.3 可直接粘贴到 OpenCode 的“项目提示词模板”
把下列内容当作任务描述交给 OpenCode（你需要把 URL/关键字段按你的厂商页面实际情况补齐）：

> 任务：我已在当前 Chrome 默认 profile 登录了多个厂商的控制台。请用 chrome-devtools MCP：
> 1. `list_pages` 找到（或 `navigate_page` 打开）以下页面：A厂用量页URL、B厂用量页URL、C厂用量页URL……
> 2. 每个页面 `wait_for` 关键元素出现后，用 `evaluate_script` 抽取“剩余额度/剩余次数/余额/到期或重置时间”等字段。
> 3. 汇总成表格（vendor / remaining / unit / reset）。
> 4. 给出下次 API 调用的推荐顺序与理由（以剩余额度优先，接近重置优先消耗为辅）。
> 约束：不要登出、不做任何付费/危险操作；只读查询为主。

---

## 5. 常见问题
### Q1：我还需要手动运行 `chrome-devtools-mcp` 命令来“开服务”吗？
如果你已经在 `opencode.json` 里配置了本地 MCP server 命令，那么 OpenCode 会按该配置启动/管理它；你通常不需要再单独开一个终端手动运行。[1]

### Q2：为什么强调“复用登录态”用 `--autoConnect`？
因为默认模式会起一个带专用 profile 的新 Chrome，不一定包含你手动登录的状态；而 `--autoConnect` 适合在“手动测试 + agent 自动化”间共享同一浏览器状态。[5]

---

如果你把“每个厂商的用量页面 URL + 页面上显示剩余额度的具体字段文案/截图”给我，我可以帮你把 **`wait_for` 的定位点**和 **`evaluate_script` 的提取逻辑**写成更接近“一键运行”的版本（并尽量适配页面改版）。[3]