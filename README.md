# 🎨 stitch-ui

> Google Stitch UI design automation for [OpenClaw](https://github.com/openclaw/openclaw) and AI agents.
>
> 通过 Google Stitch 自动生成 UI 设计，导出生产级 HTML+Tailwind 代码。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## What is this? / 这是什么？

An [AgentSkill](https://docs.openclaw.ai) that lets AI agents generate production-ready UI code from natural language prompts using [Google Stitch](https://stitch.withgoogle.com).

一个 AgentSkill 插件，让 AI 代理通过 [Google Stitch](https://stitch.withgoogle.com) 用自然语言生成生产级 UI 代码。

**Input:**
> "A pricing page with 3 tier cards, monthly/yearly toggle, dark header, primary color #0056D2"

**Output:**
- `code.html` — Complete HTML + Tailwind CSS (opens directly in browser)
- `screen.png` — Design screenshot
- `DESIGN.md` — Design system documentation (colors, fonts, components)

## Two Modes / 两种模式

| | MCP API | Playwright |
|---|---------|------------|
| **How** | Direct API calls via [mcporter](https://github.com/nicobrinkkemper/mcporter) | Browser automation via Chrome CDP |
| **Auth** | Google Cloud OAuth (gcloud) | Logged-in Chrome browser |
| **Setup** | `gcloud auth login` + mcporter config | `openclaw browser start` |
| **Output** | Screen data (JSON) | Full ZIP (HTML + screenshot + design doc) |
| **Best for** | Programmatic workflows, CI/CD | Interactive use, full exports |

## Quick Start / 快速开始

### Install / 安装

```bash
# OpenClaw users / OpenClaw 用户
openclaw skills install stitch-ui

# Manual / 手动安装
git clone https://github.com/cloudlinkhk/stitch-ui-skill.git
cp -r stitch-ui-skill ~/.openclaw/workspace/skills/stitch-ui
```

### Mode 1: Playwright (Easiest) / Playwright 模式（最简单）

**Prerequisites / 前置条件:**
```bash
npm install -g playwright-core    # or: npm install playwright-core
```

**Start Chrome with CDP / 启动 Chrome：**
```bash
# OpenClaw
openclaw browser start

# Or manually / 或手动启动
google-chrome --remote-debugging-port=18800
# macOS:
open -a "Google Chrome" --args --remote-debugging-port=18800
```

Then log into your Google account at https://stitch.withgoogle.com

然后在 https://stitch.withgoogle.com 登录 Google 账号。

**Generate / 生成：**
```bash
node scripts/stitch-generate.js \
  --prompt "A dashboard for IoT device management with sidebar nav and stats cards" \
  --type web \
  --output ./my-dashboard
```

**Output / 输出：**
```
my-dashboard/
├── code.html       ← Open in browser / 浏览器直接打开
├── screen.png      ← Design preview / 设计预览
├── DESIGN.md       ← Design system / 设计系统文档
└── stitch.zip      ← Original archive / 原始压缩包
```

### Mode 2: MCP API / MCP 模式

**Setup / 配置：**
```bash
# Install & auth gcloud / 安装并认证 gcloud
brew install google-cloud-sdk
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
gcloud auth application-default login

# Add to mcporter / 添加到 mcporter
mcporter config add stitch \
  --url "https://stitch.googleapis.com/mcp" \
  --transport http \
  --description "Google Stitch AI UI design"

# Verify (should show 8 tools) / 验证（应显示8个工具）
mcporter list stitch
```

**Use / 使用：**
```bash
# Create project / 创建项目
mcporter call stitch.create_project title="My App"

# Generate screen (takes 1-2 min) / 生成页面（需1-2分钟）
mcporter call stitch.generate_screen_from_text \
  projectId="PROJECT_ID" \
  prompt="A travel booking app home screen" \
  deviceType="MOBILE" \
  modelId="GEMINI_3_FLASH"

# Edit screen / 编辑页面
mcporter call stitch.edit_screens \
  projectId="PROJECT_ID" \
  selectedScreenIds='["SCREEN_ID"]' \
  prompt="Switch to dark mode and add a search bar"

# Get code / 获取代码
mcporter call stitch.get_screen \
  name="projects/PROJECT_ID/screens/SCREEN_ID" \
  projectId="PROJECT_ID" \
  screenId="SCREEN_ID"
```

## MCP Tools / MCP 工具列表

| Tool | Description / 描述 |
|------|-------------------|
| `create_project` | Create a new design project / 创建设计项目 |
| `list_projects` | List all projects / 列出所有项目 |
| `get_project` | Get project details / 获取项目详情 |
| `list_screens` | List screens in a project / 列出项目中的页面 |
| `get_screen` | Get screen details and code / 获取页面详情和代码 |
| `generate_screen_from_text` | Generate UI from prompt / 从描述生成 UI |
| `edit_screens` | Edit existing screens / 编辑现有页面 |
| `generate_variants` | Generate design variants / 生成设计变体 |

## Models / 可用模型

| Model | Speed | Quality | Notes |
|-------|-------|---------|-------|
| `GEMINI_3_FLASH` | ⚡ Fast | Good | Default, recommended / 默认推荐 |
| `GEMINI_3_1_PRO` | 🐢 Slower | Best | Complex UI / 复杂界面 |

## Device Types / 设备类型

`MOBILE` · `DESKTOP` · `TABLET` · `AGNOSTIC`

## Environment Variables / 环境变量

| Variable | Default | Description |
|----------|---------|-------------|
| `CDP_URL` | `http://127.0.0.1:18800` | Chrome DevTools Protocol URL |

## Prompt Tips / Prompt 技巧

```
✅ Good: "A pricing page with 3 tier cards (Basic $9/mo, Pro $29/mo, Enterprise custom),
         monthly/yearly toggle, dark gradient header, CTA buttons, FAQ section below.
         Primary #0056D2, Inter font, minimal style like Stripe."

❌ Bad:  "a pricing page"
```

- **Be specific / 描述具体**: Include layout, colors, content, number of elements
- **Reference real products / 参考真实产品**: "Like Notion's sidebar" or "Stripe's pricing layout"
- **Specify brand / 指定品牌**: Colors, fonts, dark/light theme
- **Include content / 包含内容**: Real text, prices, feature names — not lorem ipsum

## Project Structure / 项目结构

```
stitch-ui/
├── SKILL.md                    ← Agent entry point / AI 代理入口
├── README.md                   ← This file / 本文件
├── LICENSE                     ← MIT
├── package.json
├── references/
│   ├── mcp-setup.md            ← MCP setup guide / MCP 配置指南
│   └── playwright-mode.md      ← Playwright guide / Playwright 使用指南
└── scripts/
    └── stitch-generate.js      ← Browser automation script / 浏览器自动化脚本
```

## Requirements / 系统要求

- Node.js ≥ 18
- Chrome browser (for Playwright mode)
- `playwright-core` npm package
- Google account with Stitch access
- (Optional) `gcloud` CLI for MCP mode

## License / 许可证

[MIT](LICENSE) © CLOUD(HK)LIMITED

## Links / 相关链接

- [Google Stitch](https://stitch.withgoogle.com)
- [OpenClaw](https://github.com/openclaw/openclaw)
- [AgentSkills Docs](https://docs.openclaw.ai)
- [ClawHub Skills Marketplace](https://clawhub.com)
