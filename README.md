# Aurora

> 设计语言 · Design Language — design tokens + spec + copyable patterns for AI agent readers.

**Languages:** [English](#english) &nbsp;|&nbsp; [中文](#中文)

---

## English

> A design-language repository built for **AI agent readers** (Claude Code, etc.): design tokens + spec doc + copyable patterns.
> Aurora background + frosted-glass hairline cards + Alibaba PuHuiTi type + Element Plus theme alignment.

Aurora is the **single source of truth (SSOT)** for the shared design language across the team's platforms. Rules are defined once here and aligned everywhere — ending the cycle of copy-and-drift.

### What this repo is / isn't

- **Is**: a machine-readable design contract — token values, hard rules, forbidden behaviors, a self-audit checklist, plus reference implementations of the patterns.
- **Isn't**: a runnable frontend project. No `package.json` scripts / build config / router / login / business pages. Agents read it to **understand and apply** the design language, not to `npm run dev`.

### The three pieces

| Path | Purpose | How an agent uses it |
|---|---|---|
| [`styles/`](styles/) | Token source of truth (`index.scss` + breakpoints `responsive.scss`) | Copy into the project's `src/styles/`; import after Element Plus CSS |
| [`docs/design-tokens.md`](docs/design-tokens.md) | **Spec source**: hairline rules, ElCard theme, token tiers, checklist, forbidden behaviors | Read before touching UI; self-audit against the checklist |
| [`examples/`](examples/) | Copyable reference patterns (`.vue`) | Mimic when building pages; reuse the CSS verbatim |

### Agent workflow

0. **Add the Iconify runtime** — put `<script src="https://cdn.jsdelivr.net/npm/iconify-icon@3/dist/iconify-icon.min.js"></script>` in `index.html`. The examples use `<iconify-icon>`; without this script icons silently render nothing. Offline / desktop shells (pywebview): switch to a local alternative — the `@iconify/vue` package or a prebuilt SVG sprite (see "Offline consumers").
1. **Read the rules** — go through [`docs/design-tokens.md`](docs/design-tokens.md) (esp. §5 card-border rule, §6 Element Plus theme, §8 checklist).
2. **Drop in tokens** — copy [`styles/index.scss`](styles/index.scss) + [`responsive.scss`](styles/responsive.scss) into the target project's `src/styles/`, imported **after** `element-plus/dist/index.css` (so `--el-*` overrides take effect). To use the responsive mixins (`@include mobile { … }`) inside SFC styles, inject `responsive.scss` globally — `vite.config` → `css.preprocessorOptions.scss.additionalData: "@use '@/styles/responsive.scss' as *;"` — or `@use` it locally inside the component.
3. **Copy patterns** — when you need the aurora background / glass header / hairline cards, follow [`examples/`](examples/) and reuse the CSS as-is.
4. **Self-audit** — run through the §8 checklist (borders via shadow? colors via token? default radius `md`? keyboard focus visible?).

### The rule, in one line

**Card boundaries always use a `box-shadow` hairline edge (`--mp-shadow-*`) — never `border`. Colors / radii / shadows always go through `--mp-*` tokens — never hardcoded.**

### examples index

| File | Demonstrates |
|---|---|
| `examples/PortalLayout.vue` | Pure-CSS aurora background (gradient shapes + blobs, zero asset deps) + layout shell |
| `examples/AppHeader.vue` | Dual-state glass header (transparent → blur on scroll) + nav + avatar dropdown |
| `examples/AppFooter.vue` | Transparent footer |
| `examples/HairlineCardExample.vue` | Hairline card in three forms: global ElCard / custom card / hairline pill |

### Structure

```
styles/{index.scss, responsive.scss}   # token source + breakpoint mixins
docs/design-tokens.md                  # ★spec source (hairline + ElCard + checklist)
examples/*.vue                         # copyable reference patterns
package.json                           # assumed peer stack (metadata only, not published)
CHANGELOG.md                           # version history
LICENSE                                # MIT
README.md
```

### Assumed stack

Vue 3 · Element Plus · Vue Router (see `peerDependencies` in `package.json`). Icons via Iconify (`<iconify-icon>` web component — requires its CDN script in `index.html`, see the workflow; offline shells use `@iconify/vue` or an SVG sprite). Type: Alibaba PuHuiTi 3 (`@font-face` inlined in the tokens, single family with 400/700/900 weights, local-first + CDN fallback).

### Offline consumers (desktop / pywebview)

The CDN font fallback and the Iconify runtime both require network. On offline desktop shells (pywebview / WKWebView): fonts degrade silently via `local()` + the system stack — bundle a local woff2 if PuHuiTi is a hard requirement; icons must switch to `@iconify/vue` (npm, works offline) or a prebuilt SVG sprite — never rely on the CDN `<iconify-icon>`.

### Upgrading

`styles/index.scss` carries a version fingerprint in its header (`@version …`). Grep the copy inside your platform for `@version`, then diff against [`CHANGELOG.md`](CHANGELOG.md). Copies without a fingerprint predate 0.2.0 versioning — treat them as 0.1.0.

### Write-back (SSOT discipline)

Rule conflicts or gaps discovered while aligning a platform come back to this repo (issue / PR) — never patch tokens or rules ad hoc inside a platform. Local additions are how the SSOT drifts.

---

## 中文

> 面向 **智能体读者**（Claude Code 等）的设计语言仓库：设计 token + 规范文档 + 可拷贝范式。
> 极光背景 + 毛玻璃 hairline 卡 + 阿里普惠体 + Element Plus 主题对齐。

Aurora 是团队各平台共用设计语言的**单一信源（SSOT）**。规则在此一处定义、处处对齐，终结「各自拷贝、反复走样」。

### 本仓库是什么 / 不是什么

- **是**：一份机器可读的设计契约 —— token 取值、硬性规则、禁止行为、自检 checklist，外加正确范式的参考实现。
- **不是**：可运行的前端项目。没有 `package.json` 脚本 / 构建配置 / 路由 / 登录 / 业务页。智能体读它来**理解和应用**设计语言，而非 `npm run dev`。

### 三件套

| 路径 | 作用 | 智能体怎么用 |
|---|---|---|
| [`styles/`](styles/) | token 真源（`index.scss` + 断点 `responsive.scss`） | 拷进项目 `src/styles/`，在 Element Plus css 之后引入 |
| [`docs/design-tokens.md`](docs/design-tokens.md) | **规范正源**：hairline 铁律、ElCard 主题、token 分层、checklist、禁止行为 | 动手前先读，按 checklist 自检产出 |
| [`examples/`](examples/) | 可拷贝的参考范式（`.vue` 组件） | 照着搭页面，CSS 直接复用 |

### 智能体使用流程

0. **接图标运行时** —— 在 `index.html` 引入 `<script src="https://cdn.jsdelivr.net/npm/iconify-icon@3/dist/iconify-icon.min.js"></script>`。examples 用的是 `<iconify-icon>`，缺这一步图标会静默空白。离线 / 桌面端（pywebview）：改用本地方案——`@iconify/vue` 包或预构建 SVG sprite（见下方「离线环境」）。
1. **读规则** —— 通读 [`docs/design-tokens.md`](docs/design-tokens.md)（尤其 §5 卡片边框铁律、§6 Element Plus 主题、§8 checklist）。
2. **落 token** —— 把 [`styles/index.scss`](styles/index.scss) + [`responsive.scss`](styles/responsive.scss) 拷进目标项目 `src/styles/`，在 `element-plus/dist/index.css` **之后**引入（覆盖 `--el-*` 才生效）。SFC 样式内要用响应式 mixin（`@include mobile { … }`）需先接线：`vite.config` 配 `css.preprocessorOptions.scss.additionalData: "@use '@/styles/responsive.scss' as *;"` 全局注入，或在组件内局部 `@use`。
3. **抄范式** —— 需要极光背景 / 毛玻璃顶栏 / hairline 卡时，照 [`examples/`](examples/) 实现，CSS 原样复用。
4. **自检** —— 用 §8 checklist 逐条核对（边框走 shadow？颜色用 token？圆角默认 `md`？键盘焦点可见？）。

### 规范一句话

**卡片边界一律用 `box-shadow` 的 hairline edge（`--mp-shadow-*`），禁用 `border`；颜色 / 圆角 / 阴影全走 `--mp-*` token，不硬编码。**

### examples 范式索引

| 文件 | 演示 |
|---|---|
| `examples/PortalLayout.vue` | 纯 CSS 极光背景（五色 gradient + blob，零资源依赖）+ 布局壳 |
| `examples/AppHeader.vue` | 双态毛玻璃顶栏（透明 → scroll 后 blur）+ 导航 + 头像下拉 |
| `examples/AppFooter.vue` | 透明底栏 |
| `examples/HairlineCardExample.vue` | hairline 卡三式：全局 ElCard / 自定义卡 / hairline 胶囊 |

### 结构

```
styles/{index.scss, responsive.scss}   # token 真源 + 断点 mixin
docs/design-tokens.md                  # ★规范正源（hairline + ElCard + checklist）
examples/*.vue                         # 可拷贝参考范式
package.json                           # 假设的对端技术栈（仅元数据，不发包）
CHANGELOG.md                           # 版本史
LICENSE                                # MIT
README.md
```

### 假设的技术栈

Vue 3 · Element Plus · Vue Router（见 `package.json` 的 `peerDependencies`）。图标用 Iconify（`<iconify-icon>` Web Component——需在 `index.html` 引入其 CDN script，见「智能体使用流程」；离线端用 `@iconify/vue` 或 SVG sprite）。字体阿里普惠体 3.0（token 内置 `@font-face`，单 family 三档 weight 400/700/900，local 优先 + CDN 降级）。

### 离线环境（桌面端 / pywebview）

CDN 字体降级与 Iconify 运行时都依赖网络。离线桌面壳（pywebview / WKWebView）：字体经 `local()` + 系统字体栈静默降级（若必须普惠体，打包本地 woff2）；图标必须改用本地方案——`@iconify/vue`（npm 包，离线可用）或预构建 SVG sprite，勿依赖 CDN `<iconify-icon>`。

### 升级

`styles/index.scss` 头部带版本指纹（`@version …`）。在你平台内的拷贝里 grep `@version`，对照 [`CHANGELOG.md`](CHANGELOG.md) diff。没有指纹的拷贝早于 0.2.0 版本化，一律视为 0.1.0。

### 回写（SSOT 纪律）

各平台对齐时发现的规则冲突 / 缺口，一律回写本仓库（issue / PR）——禁止在平台内私改 token 或私自加规则。平台内的本地增改就是 SSOT 走样的开始。

---

© Aurora Design System
