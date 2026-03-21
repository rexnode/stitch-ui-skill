# Stitch MCP Setup / MCP 配置指南

## Overview / 概述

Google Stitch provides an official MCP server at `https://stitch.googleapis.com/mcp`.
It requires OAuth2 authentication via Google Cloud Application Default Credentials (ADC).

Google Stitch 提供官方 MCP 服务器。需要通过 Google Cloud ADC 进行 OAuth2 认证。

## Setup Steps / 配置步骤

### 1. Install gcloud CLI / 安装 gcloud

```bash
# macOS
brew install google-cloud-sdk

# Linux
curl https://sdk.cloud.google.com | bash

# Verify / 验证
gcloud --version
```

### 2. Authenticate / 认证

```bash
# Login to Google account / 登录 Google 账号
gcloud auth login

# Set project (create one at console.cloud.google.com if needed)
# 设置项目（如果没有，在 console.cloud.google.com 创建）
gcloud config set project YOUR_PROJECT_ID

# Set up Application Default Credentials / 设置 ADC
gcloud auth application-default login
gcloud auth application-default set-quota-project YOUR_PROJECT_ID
```

### 3. Configure mcporter / 配置 mcporter

```bash
mcporter config add stitch \
  --url "https://stitch.googleapis.com/mcp" \
  --transport http \
  --description "Google Stitch AI UI design"
```

### 4. Verify / 验证

```bash
# List tools (should show 8 tools) / 列出工具（应显示8个）
mcporter list stitch

# Test: list projects / 测试：列出项目
mcporter call stitch.list_projects
```

## Alternative: API Key (Limited) / 替代方案：API Key（受限）

API Keys from aistudio.google.com do NOT work with Stitch MCP.
Stitch MCP requires OAuth2 access tokens, not API keys.

aistudio.google.com 的 API Key 不能用于 Stitch MCP。
Stitch MCP 需要 OAuth2 access token。

## Available Tools / 可用工具

| Tool | Description / 描述 |
|------|-------------------|
| `create_project` | Create new project / 创建新项目 |
| `get_project` | Get project details / 获取项目详情 |
| `list_projects` | List all projects / 列出所有项目 |
| `list_screens` | List screens in project / 列出项目中的页面 |
| `get_screen` | Get screen details + code / 获取页面详情和代码 |
| `generate_screen_from_text` | Generate screen from prompt / 从描述生成页面 |
| `edit_screens` | Edit existing screens / 编辑现有页面 |
| `generate_variants` | Generate design variants / 生成设计变体 |

## Models / 模型

| Model | Speed / 速度 | Quality / 质量 | Notes / 备注 |
|-------|-------------|----------------|-------------|
| `GEMINI_3_FLASH` | ⚡ Fast | Good | Default / 默认 |
| `GEMINI_3_1_PRO` | 🐢 Slower | Best | For complex UI / 复杂界面用 |
| `GEMINI_3_PRO` | — | — | Deprecated / 已废弃 |

## Troubleshooting / 故障排查

**"API keys are not supported"**
→ Need OAuth2, not API key. Run `gcloud auth application-default login`.
→ 需要 OAuth2 而非 API Key，运行 `gcloud auth application-default login`。

**"does not support dynamic client registration"**
→ Don't use `--auth oauth` in mcporter. Use ADC (gcloud) instead.
→ mcporter 不要用 `--auth oauth`，用 gcloud ADC。

**"Project ID not found"**
→ Run `gcloud config set project YOUR_PROJECT_ID`.
→ 运行 `gcloud config set project 你的项目ID`。
