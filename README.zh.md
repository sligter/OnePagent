<div align="center">

<img src="logo.svg" alt="OnePagent" width="128" height="128" />

# OnePagent

### *One Page, Omnipotent Agent.*

**浏览器原生的单文件 AI 智能体工作台。**

[![HTML](https://img.shields.io/badge/single--file-HTML-ff6b35?style=flat-square)](onepagent.html)
[![Zero Build](https://img.shields.io/badge/build-none-00ff88?style=flat-square)](#getting-started)
[![BYOK](https://img.shields.io/badge/keys-BYOK-5b8af5?style=flat-square)](#configuration)
[![License](https://img.shields.io/badge/license-MIT-aa66ff?style=flat-square)](#license)

[English](README.md) &nbsp;|&nbsp; **简体中文**

[在线体验](https://onepagent.top) &nbsp;&middot;&nbsp; [一键部署](#deploy) &nbsp;&middot;&nbsp; [使用配置](#configuration)

</div>

---

打开一个 HTML 文件，即可获得一个可联网、可编程、可扩展的完整 AI 智能体——多轮对话、工具调用、Python 沙箱、网页检索、技能系统、上下文压缩、长期记忆、文件操作、云端同步，全部在一张页面中运行。

> 没有后端，没有 npm install，没有 Docker。一张 `.html`，自带整个宇宙。

---

## 预览

![OnePagent Preview](https://jsd.onmicrosoft.cn/gh/mydracula/image@master/20260421/188f31edc79848ff9ed581bc3b5339ff.png)

---

## Highlights

| 能力 | 说明 |
|---|---|
| 单文件部署 | `onepagent.html` 放入任意静态主机或本地打开即可运行 |
| 多 LLM 供应商 | Anthropic / OpenAI / DeepSeek，可自定义 Endpoint；密钥由 Service Worker 安全注入 |
| 推理程度控制 | 顶栏内联选择器，支持 `off / minimal / low / medium / high / xhigh` 六档 |
| 长上下文压缩 | 每模型独立 context window，接近上限时自动 LLM 摘要压缩 |
| 长期记忆 | 可选开启，跨会话持久保留事实 / 偏好 / 事件 / 技能，自动提取 + Agent 工具调用 + 手动增删，标签与关键词检索 |
| MCP 服务器 | 粘贴 `mcpServers` JSON 即可导入（`streamable_http` / `sse`），支持 Bearer 鉴权与 CORS 代理 |
| Plan Mode | Agent 先只读调研，生成 Markdown 计划供审批，批准后才执行写入 |
| Ralph Loop | 无人值守的 continue-until-done 模式，支持完成标记、最大/无限迭代、Stop 和无进展保护 |
| Sub-agents | 委派有边界的只读调研任务，并在侧栏监控运行状态 |
| Agent Swarm | 可选启用的并行 orchestrator-worker 蜂群：lead 一轮内发出多个 `SwarmSpawn`，按角色（researcher / critic / writer / coder）并发执行，带 token 预算与并发上限 |
| Human-in-the-loop | Agent 可在需要用户判断时请求文本、选择或确认 |
| TodoWrite | Agent 主动维护可视任务清单（pending / in-progress / completed） |
| Hooks | 用户自定义 JS 处理函数，挂在 Agent 6 个生命周期事件上 |
| Python 沙箱 | 通过 Pyodide 在浏览器内执行 Python |
| Web Search | Tavily 集成，`basic` / `advanced` 两档搜索深度 |
| Skills 系统 | `.skill` / `.zip` 安装，GitHub 拉取，界面创建，或让 AI 通过 `SkillManager` 管理技能 |
| 会话管理 | 多会话、文件夹分组、拖拽排序、IndexedDB 持久化、一键导出 |
| Cloud Sync | S3 兼容桶增量同步，可选 AES-256-GCM 端到端加密 |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    onepagent.html                           │
│  ┌───────────┐  ┌──────────┐  ┌────────────┐  ┌──────────┐  │
│  │  UI Shell │  │  Chat /  │  │  Tools /   │  │  Skills  │  │
│  │ (3-column)│  │  Streams │  │  MCP Bus   │  │ Registry │  │
│  └─────┬─────┘  └────┬─────┘  └─────┬──────┘  └────┬─────┘  │
│        │             │              │              │        │
│  ┌─────┴─────────────┴──────────────┴──────────────┴─────┐  │
│  │          Pretext Layout Engine (inlined)             │  │
│  │  markdown → blocks → lines → flowed DOM              │  │
│  └──────────────────────────────────────────────────────┘  │
│        │                                                    │
│  ┌─────┴───────┐ ┌──────────────┐ ┌───────────┐ ┌────────┐  │
│  │ Service     │ │ LocalStorage │ │ Pyodide   │ │ S3     │  │
│  │ Worker      │ │ + IndexedDB  │ │ (Python)  │ │ SigV4  │  │
│  │ (key inject)│ │ (all state)  │ │           │ │ Client │  │
│  └─────┬───────┘ └──────────────┘ └───────────┘ └───┬────┘  │
└────────┼──────────────────────────────────────────────┼─────┘
         │                                              │
         ▼                                              ▼
  ┌──────────────┐  ┌────────────┐  ┌──────────┐  ┌─────────────┐
  │  Anthropic   │  │  OpenAI    │  │  Tavily  │  │ 你的 S3 桶  │
  │  DeepSeek    │  │  …         │  │          │  │ AWS/R2/MinIO│
  └──────────────┘  └────────────┘  └──────────┘  └─────────────┘
```

---

## Getting Started

```bash
git clone https://github.com/sligter/OnePagent.git
cd OnePagent
```

双击 `onepagent.html`，或用任意静态服务器托管：

```bash
python -m http.server 8000
# 或
npx serve .
```

访问 `http://localhost:8000/onepagent.html`，点击顶栏 **Settings** 配置 Provider / API Key / 模型，保存后即刻生效。

> **Service Worker 限制**：SW 仅在 `https://` 或 `localhost` 下注册。`file://` 打开时自动降级为直连 fetch。

---

## Skills 技能

可在左侧 **Skills** 面板通过 Market、`.skill/.zip`、GitHub 或 Create 安装技能。GitHub 目录链接可直接使用，例如 `https://github.com/anthropics/skills/tree/main/skills/skill-creator`。

Agent 也可以通过 `SkillManager` 列出、安装、更新、启用/停用或删除技能；远程安装和破坏性/高风险变更会要求确认。

---

## Deploy

OnePagent 是纯静态站点，任意静态主机均可运行：

<div align="center">

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fsligter%2FOnePagent&project-name=onepagent&repository-name=onepagent)
&nbsp;
[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://zeabur.com/new)
&nbsp;
[![Deploy to Cloudflare Pages](https://img.shields.io/badge/Deploy-Cloudflare%20Pages-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://dash.cloudflare.com/?to=/:account/pages/new)

</div>

| 平台 | 步骤 |
|---|---|
| **Vercel** | 点击按钮 → 登录 → Deploy，约 10 秒完成 |
| **Zeabur** | 点击按钮 → 选择 fork 仓库 → 自动识别静态站点 → Generate Domain |
| **Cloudflare Pages** | Connect to Git → Framework preset: None, Build command: 留空, Output: `/` → Deploy |
| **GitHub Pages** | Settings → Pages → Source: GitHub Actions → 推送即自动部署 |

> 所有数据存在浏览器本地，不同域名之间不共享。跨域名/跨设备同步请用 [Cloud Sync](#cloud-sync)。

---

## Cloud Sync

通过任意 S3 兼容桶备份和跨设备同步，配置路径是 **Settings → Cloud Sync**。

- 支持 AWS S3、Cloudflare R2、Backblaze B2 和 MinIO。
- 必填：Endpoint、Region、Bucket、Access Key ID、Secret Access Key。
- 可选：Prefix、AES-256-GCM 加密口令、自动推送，以及 MinIO / R2 常用的 path-style URL。
- 先在 Settings 中 **Test connection** / **Show CORS config**，再用顶栏 **Sync → Push now / Pull now**。
- LLM 密钥不同步，始终留在本机。

---

## Ralph Loop

Ralph Loop 会在普通回答结束后继续推进同一任务。点击顶栏 **Ralph**，正常发送任务，OnePagent 会自动续跑，直到看到完成标记或被保护条件停止。

- 配置路径：**Settings → Ralph Loop**，可设置最大迭代、**Unlimited** 和完成标记（默认 `RALPH_DONE`）。
- 顶栏 **Stop** 可取消当前 run。
- `AskUser` 会使用默认值或取消，不弹窗；Plan Mode 不会自动批准。

示例：`检查这个页面，能修就修，完成后包含 RALPH_DONE。`

---

## Memory

长期记忆保存跨对话可复用的事实，配置路径是 **Settings → Memory**。

- 在 IndexedDB 中保存 `fact` / `preference` / `event` / `skill` / `note`。
- 可选自动提取，在助手回答后保留耐用事实。
- 召回结合时效、标签、关键词和 prompt caching。
- 工具：`memory_save`、`memory_search`、`memory_update`、`memory_forget`。
- Memory Viewer 支持搜索、过滤、JSON 导入导出和废弃记录查看。

---

## Agent Workflow

- **Sub-agents**：主 Agent 可启动有边界的只读调研 worker，侧栏显示运行状态和摘要。
- **Human-in-the-loop**：`AskUser` 支持文本、选择和确认，用于不该让模型猜的决策。
- **Media tools**：图片/视频生成通过工具和配置的生成模型显式触发，不再每轮自动生成。
- **Diagnostics**：文件系统诊断入口位于 **Settings → Diagnostics**。

---

## Agent Swarm

可选启用的并行多 agent 运行时。开关位于 **Settings → Agent Swarm**。

- **Fanout（扇出）**——lead 一轮内发出多个 `SwarmSpawn(role, task)`，按 *Max concurrency* 并行执行。内置角色：researcher、critic、writer、coder。
- **Handoff（接力）**——worker 调 `SwarmHandoff(role, brief)` 串成 `researcher → critic → writer`；环检测与深度超限会被拒绝。
- **Blackboard（黑板）**——单轮共享工作台：`bb_write` / `bb_read` / `bb_list` / `bb_post_task` / `bb_claim`，最新条目自动注入 worker prompt。
- **自定义角色**——左栏 **Swarms** 面板管理：独立 system prompt、工具白名单、handoff 目标、预算；`bindSkills` 直接授予已装技能的工具；支持 JSON 导入/导出。
- **RoleManager**（可选）——lead 专用工具，可在任务中即时 create / update / delete / duplicate 角色。存储模式三选一：内存 / 全局 / 按对话。内置与用户编写的角色受保护。

护栏：单 worker 与单轮总 token 预算、并发上限、Plan Mode 继承、Ralph 互斥、防递归。Hooks 新增 `pre_swarm_spawn` / `post_swarm_spawn`。可设 *Worker model override*（如 Haiku）让 worker 跑廉价模型、lead 留给旗舰模型。

适用于广度优先任务（多源调研、对比分析），不适合紧耦合的代码重构。

---

## Configuration

配置与会话都存于浏览器本地（`localStorage` + `IndexedDB`）。OnePagent 没有服务端，请求只发往你配置的 API Endpoint。

---

## Contributing

单文件项目——欢迎 fork、修改、PR。

- 保持 `onepagent.html` 可独立运行
- 不引入需构建步骤的依赖
- `index.html` 仅做跳转，请勿加逻辑

---

## Star History

<div align="center">

[![Star History Chart](https://api.star-history.com/svg?repos=sligter/OnePagent&type=Date)](https://star-history.com/#sligter/OnePagent&Date)

</div>

---

## 友链

- [Linux Do](https://linux.do/) — 新的理想型社区

---

## License

MIT (c) OnePagent contributors

---

<div align="center">

**One Page. Omnipotent Agent.**

Built for people who believe a single HTML can still do everything.

</div>
