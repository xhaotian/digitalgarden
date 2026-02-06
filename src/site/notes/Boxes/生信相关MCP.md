---
{"modified":["2026-02-06","2026-02-05"],"dg-publish":true,"permalink":"/Boxes/生信相关MCP/","dgPassFrontmatter":true}
---






# BioMCP




# Simple PubMed

## 1. 这是什么？适合解决什么问题？

**MCP Simple PubMed** 是一个 MCP（Model Context Protocol）服务器：把 **PubMed/PMC（通过 NCBI Entrez API）** 的检索与内容获取能力，封装成“工具（tools）”，让支持 MCP 的 AI 客户端（如 Claude Desktop、OpenCode 等）可以像调用函数一样调用 PubMed。

它特别适合眼科生信（单细胞/多组学整合）中常见的“高频、重复、需要结构化输出”的文献工作，例如：

- 快速做某个主题的**系统性检索草案**（带 MeSH + 同义词 + 布尔逻辑）
- 围绕一个疾病/组织（视网膜/脉络膜/视神经等）快速拉出**近 3–5 年关键方法论文**
- 从文献中抽取可直接落盘的结构化信息：**样本量、平台（10x/SMART-seq）、物种、组织区域、是否有 ATAC/空间/蛋白、多组学融合方法、数据存储库编号（GEO/SRA）**等

> 你的研究背景（眼科 + 单细胞/多组学）里，文献检索往往不是难在“搜到”，而是难在**把检索策略标准化 + 把结果结构化沉淀到知识库**。这个 MCP 的价值在于“把检索/摘要/（可用时）全文 XML 获取”变成可自动化的步骤。

---

## 2. 它能做什么 / 不能做什么（务实边界）

### 2.1 核心能力（Tools）
通常包含三类能力（以 MCP 工具形式提供）：
1. **Search PubMed**：按关键词、MeSH、作者、日期范围、布尔逻辑等检索
2. **Access Abstracts**：获取摘要与元数据（PMID、期刊、年份、作者等）
3. **Get Full Text（当可用）**：如果论文在 **PubMed Central（PMC）开源**，可取到全文（往往是 XML 结构化文本，对 AI 处理友好）

（安装与配置在 PyPI 项目页有标准写法。）[<sup>1</sup>](https://pypi.org/project/mcp-simple-pubmed/?utm_source=openai)

### 2.2 重要限制
- **拿不到付费墙论文全文**；即使论文页面你浏览器能打开，AI/工具抓取也可能遇到“Forbidden”等访问限制（这是常见现象，不一定代表论文不可读）。
- 全文常以 **XML** 返回：更利于机器抽取结构（标题、段落、图表引用），但不一定“人类阅读友好”。

---

## 3. 安装与配置（以你当前 Windows 11 + WSL2 + conda 为主）

下面给一个**建议做法**：在 WSL2 的 conda 环境里安装并运行 MCP 服务器（更贴合你现有生信环境）。

### 3.1 在 WSL2（Ubuntu）创建环境并安装

```bash
# 1) 创建独立环境（避免污染你的单细胞主环境）
conda create -n mcp-pubmed python=3.11 -y
conda activate mcp-pubmed

# 2) 安装
pip install -U pip
pip install mcp-simple-pubmed

# 3) 简单自检（不保证所有包都有 __version__，所以用 import 即可）
python -c "import mcp_simple_pubmed; print('mcp_simple_pubmed import OK')"
```

### 3.2 准备 NCBI 必需环境变量

该服务需要：
- `PUBMED_EMAIL`：NCBI 要求提供的 email
- `PUBMED_API_KEY`：可选；用于更高的速率上限（文档中提到默认 3 req/s，有 key 可到 10 req/s）[<sup>1</sup>](https://pypi.org/project/mcp-simple-pubmed/?utm_source=openai)

在 WSL 里（建议写入 `~/.bashrc` 或项目 `.env`，但**不要提交到 Git**）：

```bash
export PUBMED_EMAIL="your-email@example.com"
export PUBMED_API_KEY="your-api-key"   # 可选
```

---

## 4. 在 Obsidian 里怎么用：推荐的“可复现文献检索”笔记模板

建议每次检索都落一个 note，最关键的是：**把检索式、日期范围、执行日期、筛选规则写清楚**，后续你做系统综述/补充检索时不会丢上下文。

### 4.1 Obsidian 笔记模板（可直接复制）

```markdown
---
type: pubmed-search
topic: "retina scRNA-seq multiome integration"
date_run: 2026-02-05
date_range: "2019-01-01 ~ 2026-02-05"
species: [human, mouse]
tissues: [retina, RPE, choroid, optic_nerve]
keywords:
  - single-cell
  - multiome
  - scRNA-seq
  - scATAC-seq
  - spatial transcriptomics
  - integration
---

## Question / Goal
- 我想回答的问题是：

## Search strategy (copy/paste)
- PubMed query（最终版）：
- 备注（MeSH、同义词、排除词、日期限制）：

## Results (structured)
| PMID | Year | Title | Journal | Key tech | Data accession | Notes |
|------|------|-------|---------|----------|----------------|-------|

## Triage decision
- Included:
- Excluded (+ reason):

## Extracted methods / parameters (for downstream bioinfo)
- 样本来源：
- 细胞数/批次设计：
- 平台（10x/SMART-seq/Visium/CITE/多组学）：
- 预处理与整合方法（Seurat/Scanpy/scVI/Harmony/LIGER/totalVI/ArchR等）：
- 关键 marker / pathway：
```

---

## 5. 与 OpenCode（opencode）联动：把 PubMed 工具接到“终端智能体”里

OpenCode 是一个可在终端/桌面运行的 AI coding agent，并且支持通过 MCP 挂载外部工具。[<sup>2</sup>](https://opencode.ai/?utm_source=openai)

### 5.1 OpenCode 的 MCP 配置要点（重要：用 `mcp` 字段）

OpenCode 官方文档里，MCP servers 配在 `opencode.json`/`opencode.jsonc` 的 `mcp` 下，既支持本地（local）也支持远程（remote）。[<sup>3</sup>](https://opencode.ai/docs/mcp-servers/)

- **Local**：你提供一个启动命令数组（OpenCode 会帮你拉起该 MCP server 进程），并可注入环境变量。[<sup>3</sup>](https://opencode.ai/docs/mcp-servers/)
- **Remote**：直接配 URL + headers（若对方是远程 MCP 服务）。[<sup>3</sup>](https://opencode.ai/docs/mcp-servers/)

### 5.2 推荐联动方案 A（最稳）：OpenCode 也在 WSL 里跑

1) 在 WSL 安装 OpenCode（按其官方安装方式）
2) 在你的“文献/分析项目目录”根目录放一个 `opencode.json`

示例 `opencode.jsonc`（**关键：用 `bash -lc` 激活 conda 后再启动**）：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "simple_pubmed": {
      "type": "local",
      "command": [
        "bash",
        "-lc",
        "source ~/miniconda3/etc/profile.d/conda.sh && conda activate mcp-pubmed && python -m mcp_simple_pubmed"
      ],
      "enabled": true,
      "environment": {
        "PUBMED_EMAIL": "your-email@example.com",
        "PUBMED_API_KEY": "{env:PUBMED_API_KEY}"
      }
    }
  }
}
```

> 说明：上面把 `PUBMED_API_KEY` 放到系统环境变量里（而不是写死在配置文件），更安全。

然后你就可以在 OpenCode 对话里说类似：
- “检索 **retina AND single-cell**，限定 2020–2026，优先综述与方法学；**use simple_pubmed**”
- “把前 30 篇按 *是否有多组学/空间/整合方法* 分组输出成 markdown 表格；**use simple_pubmed**”

### 5.3 推荐联动方案 B：做一个 OpenCode 自定义命令 `/pubmed`

OpenCode 支持把常用工作流做成 **custom commands**（Markdown/YAML frontmatter 模板），用 `/命令` 一键触发。[<sup>4</sup>](https://opencode.ai/docs/commands/?utm_source=openai)

在项目目录创建：`.opencode/commands/pubmed.md`

```markdown
---
description: PubMed search + summarize for ophthalmology bioinfo
agent: plan
---

你现在是我的眼科生信文献助理。

目标：围绕「$ARGUMENTS」做一次可复现 PubMed 检索，并输出 Obsidian 可直接粘贴的结果表格。

要求：
1) 先给出 1 个“精简检索式”和 1 个“系统综述级检索式”（含 MeSH + 同义词 + 排除词建议）
2) 使用 simple_pubmed 工具执行检索（use simple_pubmed），返回 Top 25（按相关性或近年优先）
3) 输出 markdown 表格：PMID / Year / Title / Key tech / Disease(or tissue) / Accession / 1-sentence takeaway
4) 最后给出：下一步我该精读的 5 篇（说明理由：方法/数据/可复现性/与单细胞整合相关性）

开始执行。
```

使用方式（在 OpenCode TUI 中）：
- `/pubmed retina organoid single-cell multiome integration`

---

## 6. 面向“眼科单细胞/多组学整合”的检索用法建议（可直接复用）

下面是一些“高命中率”的检索构造思路（你可以让 MCP Prompts 帮你生成，也可以手写）：

### 6.1 主题：视网膜单细胞图谱 + 疾病对照
- retina OR retinal
- single-cell OR scRNA-seq OR snRNA-seq
- disease：AMD / diabetic retinopathy / retinitis pigmentosa / glaucoma（视情况加 MeSH）
- 可加：cell type annotation / atlas / reference mapping

### 6.2 主题：多组学整合（RNA+ATAC / CITE / multiome）
- multiome OR "scATAC-seq" OR "ATAC" OR "chromatin accessibility"
- integration OR "joint embedding" OR "paired" OR "matched"
- 方法关键词：totalVI / multiVI / scVI / Harmony / LIGER（按你的技术栈选择）

### 6.3 主题：空间转录组与单细胞对齐（retina 常用）
- spatial transcriptomics OR Visium OR MERFISH OR seqFISH
- deconvolution OR mapping OR "cell2location" OR "tangram"（按你常见工具加）

---

## 7. 实操小贴士（避免踩坑）

1) **先拿摘要做分层**：把“方法学/数据资源型论文”优先级拉高（对你做整合最有用）。
2) **把检索式当作实验参数**：固定日期范围、写清纳排标准；否则你以后很难解释“为什么这批论文被选中”。
3) **全文抓不到不等于不可读**：MCP 拿不到全文时，用 PMID/DOI 去图书馆或浏览器手动补齐是常态。
4) **控制上下文开销**：OpenCode 文档提示 MCP 会增加上下文占用，别一次启用太多 MCP servers；建议为“文献 agent”单独启用 simple_pubmed。[<sup>3</sup>](https://opencode.ai/docs/mcp-servers/)

---

## 8. 你可以让我继续做的定制（可选）

如果你告诉我：
- 你主要方向（视网膜/青光眼/角膜/葡萄膜/视神经等）
- 你关注的多组学类型（multiome、CITE、空间、蛋白、甲基化等）
- 你偏好的分析栈（Scanpy/scVI vs Seurat/ArchR 等）

我可以把第 4 节的 Obsidian 模板和第 5 节的 `/pubmed` 命令进一步定制成：
- 自动输出你需要的字段（比如 **细胞数、批次数、测序平台、QC/整合方法、marker 基因、数据 accession**）
- 自动生成“下一步可复现分析 checklist”（更贴合你单细胞+多组学整合的工作流）





