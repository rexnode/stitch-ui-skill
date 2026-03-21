---
name: stitch-ui
description: |
  Generate UI designs and export production-ready code via Google Stitch.
  Supports two modes: MCP API (direct tool calls, requires OAuth) and Playwright browser automation (uses logged-in Chrome, exports ZIP with HTML+CSS+screenshot+design doc).
  Use when: user asks to design a UI, generate a page/screen, create an app interface, build a landing page, make a dashboard, or mentions "Stitch", "Google Stitch", or "stitch.withgoogle.com".
  通过 Google Stitch 生成 UI 设计并导出生产级代码。当用户需要设计界面、生成页面、创建 App 界面、制作落地页或仪表盘时使用。
---

# Stitch UI — Google Stitch Design Automation

Generate UI screens from text prompts and export production-ready HTML+Tailwind code.

## Two Modes

### Mode 1: MCP API (Recommended)
Direct API calls via `mcporter`. Requires OAuth setup (see [references/mcp-setup.md](references/mcp-setup.md)).

```bash
# List projects
mcporter call stitch.list_projects

# Create project
mcporter call stitch.create_project title="My App"

# Generate screen (may take 1-2 min, DO NOT RETRY)
mcporter call stitch.generate_screen_from_text \
  projectId="PROJECT_ID" \
  prompt="A pricing comparison page with cards" \
  deviceType="DESKTOP" \
  modelId="GEMINI_3_FLASH"

# Edit existing screen
mcporter call stitch.edit_screens \
  projectId="PROJECT_ID" \
  selectedScreenIds='["SCREEN_ID"]' \
  prompt="Change to dark mode"

# Get screen details (includes HTML code)
mcporter call stitch.get_screen \
  name="projects/PROJECT_ID/screens/SCREEN_ID" \
  projectId="PROJECT_ID" \
  screenId="SCREEN_ID"
```

**Available tools:** `create_project`, `get_project`, `list_projects`, `list_screens`, `get_screen`, `generate_screen_from_text`, `edit_screens`, `generate_variants`

**Models:** `GEMINI_3_FLASH` (fast) | `GEMINI_3_1_PRO` (best quality)

**Device types:** `MOBILE` | `DESKTOP` | `TABLET` | `AGNOSTIC`

### Mode 2: Playwright Browser Automation
Uses the user's logged-in Chrome via CDP. Exports a complete ZIP (code.html + screen.png + DESIGN.md).

**Prerequisites:**
- Chrome running via `openclaw browser start` (CDP on port 18800)
- User logged into Google account on stitch.withgoogle.com

```bash
node scripts/stitch-generate.js \
  --prompt "eSIM travel data plan comparison page" \
  --type web \
  --output ./my-output
```

**Args:**
- `--prompt` (required): UI description in natural language
- `--type`: `app` (default) or `web`
- `--output`: output directory (default: `/tmp/stitch-TIMESTAMP`)

**Output files:**
- `code.html` — Full HTML+Tailwind, opens directly in browser
- `screen.png` — Design screenshot
- `DESIGN.md` — Design system doc (colors, fonts, components)
- `stitch.zip` — Original ZIP from Stitch

See [references/playwright-mode.md](references/playwright-mode.md) for details.

## Workflow

1. **Choose mode**: MCP if configured, Playwright as fallback
2. **Generate**: Describe the UI in natural language
3. **Iterate**: Edit/modify with follow-up prompts
4. **Export**: Download code and assets
5. **Integrate**: Use the HTML+Tailwind output in your project

## Tips

- Be specific: "A pricing page with 3 tier cards, monthly/yearly toggle, dark header" > "a pricing page"
- Reference real products: "Like Stripe's pricing page but for eSIM plans"
- Specify colors/brand: "Primary #0056D2, dark theme, Inter font"
- For variants: Use `generate_variants` with `creativeRange` = REFINE / EXPLORE / REIMAGINE
