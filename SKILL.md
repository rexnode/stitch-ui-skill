---
name: stitch-ui
description: |
  Generate UI designs and export production-ready code via Google Stitch Remote MCP API.
  Supports API Key (recommended) and OAuth authentication. Includes Design System management.
  Use when: user asks to design a UI, generate a page/screen, create an app interface, build a landing page, make a dashboard, or mentions "Stitch", "Google Stitch", or "stitch.withgoogle.com".
  通过 Google Stitch Remote MCP API 生成 UI 设计并导出生产级代码。支持 API Key（推荐）和 OAuth 认证。包含设计系统管理。
---

# Stitch UI — Google Stitch Design Automation

Generate UI screens from text prompts and export production-ready HTML+Tailwind code via the official Stitch Remote MCP Server.

## Setup (API Key — Recommended)

1. Go to [Stitch Settings](https://stitch.withgoogle.com) → API Keys → Create API Key
2. Add to your MCP client:

**Claude Code:**
```bash
claude mcp add stitch \
  --transport http https://stitch.googleapis.com/mcp \
  --header "X-Goog-Api-Key: YOUR_KEY" \
  -s user
```

**Cursor** (`.cursor/mcp.json`):
```json
{
  "mcpServers": {
    "stitch": {
      "url": "https://stitch.googleapis.com/mcp",
      "headers": { "X-Goog-Api-Key": "YOUR_KEY" }
    }
  }
}
```

For OAuth setup, see [references/mcp-setup.md](references/mcp-setup.md).

## MCP Tools (12)

### Project Management
| Tool | Description | Key Params |
|------|-------------|------------|
| `create_project` | Create new project / 创建项目 | `title` (string) |
| `get_project` | Get project details / 获取项目详情 | `name` (string, resource name) |
| `list_projects` | List all projects / 列出所有项目 | `filter` (string, owned/shared) |

### Screen Management
| Tool | Description | Key Params |
|------|-------------|------------|
| `list_screens` | List screens in project / 列出页面 | `projectId` (string) |
| `get_screen` | Get screen details + HTML code / 获取页面代码 | `name` (string, resource name) |

### AI Generation
| Tool | Description | Key Params |
|------|-------------|------------|
| `generate_screen_from_text` | Generate UI from prompt / 从描述生成 UI | `projectId`, `prompt`, `modelId`, `deviceType` |
| `edit_screens` | Edit existing screens / 编辑页面 | `projectId`, `selectedScreenIds` (string[]), `prompt` |
| `generate_variants` | Generate design variations / 生成设计变体 | `projectId`, `selectedScreenIds` (string[]), `prompt`, `variantOptions` |

### Design Systems
| Tool | Description | Key Params |
|------|-------------|------------|
| `create_design_system` | Create design system with tokens / 创建设计系统 | `designSystem` (object), `projectId`? |
| `update_design_system` | Update design system / 更新设计系统 | `name`, `projectId`, `designSystem` |
| `list_design_systems` | List design systems / 列出设计系统 | `projectId`? |
| `apply_design_system` | Apply design system to screens / 应用设计系统 | `projectId`, `selectedScreenInstances` (array), `assetId` |

## Models

| Model | Speed | Quality | Use Case |
|-------|-------|---------|----------|
| `GEMINI_3_FLASH` | Fast | Good | Default, most tasks / 默认推荐 |
| `GEMINI_3_1_PRO` | Slower | Best | Complex UI / 复杂界面 |

## Device Types

`MOBILE` · `DESKTOP` · `TABLET` · `AGNOSTIC`

## Variant Options

`generate_variants` supports `variantOptions`:
- `count`: Number of variants (default: 4)
- `creativeRange`: `REFINE` | `EXPLORE` | `REIMAGINE`
- `aspects`: Specific aspects to vary

## Typical Workflow

```
1. list_projects → find or create_project
2. generate_screen_from_text → create initial design (takes 1-2 min, DO NOT RETRY)
3. get_screen → retrieve HTML code
4. edit_screens → iterate on design
5. generate_variants → explore alternatives
6. create_design_system → define brand tokens
7. apply_design_system → apply brand to screens
```

## Prompt Tips

- Be specific: "A pricing page with 3 tier cards, monthly/yearly toggle, dark header" > "a pricing page"
- Reference real products: "Like Stripe's pricing page but for eSIM plans"
- Specify brand: "Primary #0056D2, dark theme, Inter font"
- Include content: Real text, prices, feature names — not lorem ipsum

## Fallback: Playwright Browser Automation

If MCP is not configured, use the Playwright script for browser automation:

```bash
node scripts/stitch-generate.js \
  --prompt "A dashboard with sidebar nav and stats cards" \
  --type web \
  --output ./my-output
```

Requires Chrome with CDP enabled. See [references/playwright-mode.md](references/playwright-mode.md).
