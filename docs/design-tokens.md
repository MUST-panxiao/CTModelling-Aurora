# Aurora 设计 Token 标准

> 本文档是 **Aurora 设计语言**的唯一标准（SSOT），供团队各平台与未来新平台参照。
> 值标准与命名解耦：新平台可沿用 `--mp-*` 命名，也可自定命名，但**取值照此文档**。
> 定义真源：[`styles/index.scss`](../styles/index.scss)。

---

## 0. 血缘链（SSOT 溯源）

```
源头版本（规则最完整）
  └─ 精简版
       └─ 业务平台 A（对齐源头完整版）
            └─ 业务平台 B（在两条规则上领先：EP 中性灰阶映射 + 全局 ElCard 主题）
                 └─ 抽象为 Aurora（SSOT，收敛各平台最新规则）
```

Aurora 是上述各版本设计规则的**收敛点**：规则在此一处定义，新项目起步即自动对齐，不再各自拷贝、反复走样。

---

## 1. 设计理念：hairline（描边 + 轻投影）

摒弃传统 Material Design 的「大墨块投影」，采用 **hairline 体系**：

- 用 1px 描边（edge）定义边界，而非靠浓重投影「浮起」；
- 投影极轻（contact + ambient），仅暗示层级；
- 顶部 1px 白色 inset（top-glow）还原光线方向，增加质感。

---

## 2. 圆角 `--mp-radius-*`（5 档）

| token | 值 | 用途 |
|---|---|---|
| `--mp-radius-sm` | `4px` | badge、小图标按钮、chip、tag |
| `--mp-radius-md` | `8px` | **默认档**：卡片、按钮、输入框、PageHeader、tooltip |
| `--mp-radius-lg` | `12px` | Dialog、Drawer、Hero 卡片、登录卡 |
| `--mp-radius-pill` | `9999px` | 头像、状态点、胶囊按钮、胶囊标签 |
| `--mp-radius-circle` | `50%` | 圆形（极光 shape、状态圆点） |

**默认值 8px（柔和）**。无特殊情况一律用 `md`，不要随手写 `lg`。

---

## 3. 阴影 `--mp-shadow-*`（4 档）

| token | 值（组合） | 用途 |
|---|---|---|
| `--mp-shadow-flat` | `edge + contact` | 统计小卡、内嵌 chip、轻徽章 |
| `--mp-shadow-surface` | `edge + contact + ambient + top-glow` | **默认档**：面板、卡片、内容容器 |
| `--mp-shadow-surface-hover` | `蓝色 ring + contact + ambient + top-glow` | hover 态（**唯一允许的彩色阴影**） |
| `--mp-shadow-overlay` | `强 edge + 大 ambient + top-glow` | Dialog、Drawer、Dropdown、tooltip 浮层 |

完整像素值见 `index.scss`。**默认用 `surface`**，hover 切 `surface-hover`，浮层用 `overlay`。

> `surface-hover` / `overlay` 末尾的 `inset 0 1px 0 rgba(255,255,255,…)`（top-glow）不可省略，否则失去顶部高光。

---

## 4. 原子原语 `--mp-hairline-*`（自由组合）

当 4 档预设不满足时，用原子原语按需组合：

| token | 值 | 含义 |
|---|---|---|
| `--mp-hairline-edge` | `0 0 0 1px rgba(0,0,0,0.06)` | 描边（阴影式边界，替代 border） |
| `--mp-hairline-contact` | `0 1px 2px rgba(0,0,0,0.04)` | 接触投影（紧贴表面的微投影） |
| `--mp-hairline-ambient` | `0 4px 12px rgba(0,0,0,0.06)` | 环境投影（暗示悬浮） |
| `--mp-hairline-top-glow` | `inset 0 1px 0 rgba(255,255,255,0.5)` | 顶部高光（光线方向） |

组合关系：`surface = edge + contact + ambient + top-glow`。

---

## 5. 卡片边框铁律（★Aurora 核心）

**卡片类元素的边界一律用 `box-shadow` 的 hairline edge，禁用 `border`。**

为什么：
- `border` 会占布局像素、与 `box-shadow` 叠加产生双线；
- hairline edge 是阴影，不占布局、可与 backdrop-blur 共存，呈现统一的毛玻璃质感。

怎么写：

```scss
// ✅ 正确：边界由 shadow 提供
.feature-card {
  border: none;                       // 显式禁用，避免继承 EP 默认 border
  box-shadow: var(--mp-shadow-flat);  // 内含 0 0 0 1px hairline edge
  &:hover { box-shadow: var(--mp-shadow-surface-hover); }
}

// ❌ 错误：用 border 画线
.feature-card {
  border: 0.5px solid var(--mp-border-subtle); // 双线风险 + 不统一
}
```

> 分割线（如卡片 header 下沿、footer 分隔）除外，用 `1px solid var(--mp-divider)` 是允许的 —— 它是「内容分隔」而非「卡片边界」。

---

## 6. Element Plus 主题对齐（合并自最新规则）

Aurora 用 CSS 变量把 Element Plus 全量拉入品牌系统，**主色/语义色 + 中性灰阶 + 阴影 + 全局 ElCard 四层覆盖**（均在 `index.scss` 的 `:root` 与全局段，且文件必须在 `element-plus/dist/index.css` 之后引入）：

### 6.1 主色 + 语义色
`--el-color-primary` / `success` / `warning` / `danger` / `info` → 经 `var(--mp-*)` 引用——**改 `--mp-*` 值全站（含 EP）自动跟随**。light-3~9 先落 hex 兜底，`@supports (color: color-mix(...))` 内改走公式值（EP 官方公式 mix(白, 主色, N×10%)）：**不能省 @supports 直接「hex + color-mix 两行同写」**——自定义属性无解析期校验、级联后者恒胜，var() 替换出非法值时按 IACVT 回退 initial 而非前行 hex，旧内核（Chrome <111 / Safari <16.2 / Firefox <113）上会整层失效。按钮、链接、选中态、el-tag / el-alert 随之统一。注意 `--mp-info` 为中性灰 `#707070`（与 EP 惯例一致，勿改回品牌蓝以免与 primary 混淆）。

### 6.2 中性灰阶映射（20 条）
把 EP 的文字 / 背景 / 填充 / 边框灰阶全量映射到 Aurora 调色板，消除「EP 默认灰」与品牌系统的割裂：

| EP 变量族 | 映射到 | 条数 |
|---|---|---|
| `--el-text-color-*` | `--mp-text*` | 6 |
| `--el-bg-color*` | `--mp-surface` / `--mp-bg` | 3 |
| `--el-fill-color-*` | `--mp-bg-secondary` / `--mp-surface*` | 6 |
| `--el-border-color-*` | `--mp-border*` | 5 |

> 仅映射中性灰阶；主色 / 语义色由 6.1 覆盖，圆角不动；阴影已并入 hairline 体系（`--el-box-shadow*` → `--mp-shadow-*`，EP 浮层组件随之统一）。

### 6.3 全局 `.el-card` 主题
所有 `el-card` 默认呈现与介绍页 / 工作台一致的毛玻璃 hairline 观感，**无需逐个写样式**：

```scss
.el-card {
  border: none;                            // 禁用 EP 默认 border（见 §5 铁律）
  background-color: var(--mp-surface-glass);
  border-radius: var(--mp-radius-lg);
  box-shadow: var(--mp-shadow-flat);
  -webkit-backdrop-filter: blur(6px);      // WKWebView / Safari ≤17
  backdrop-filter: blur(6px);
  transition: box-shadow var(--mp-duration-fast) ease;
}
// EP 自带 .is-always-shadow / .is-hover-shadow（特异性 0,2,0）必须同特异性压回 hairline，
// 否则默认 el-card（shadow prop 默认 "always"）渲染 EP 灰投影
.el-card.is-always-shadow,
.el-card.is-hover-shadow,
.el-card.is-hover-shadow:hover,
.el-card.is-hover-shadow:focus { box-shadow: var(--mp-shadow-flat); }
// 可交互卡片 hover 反馈：业务侧自行挂 .is-interactive 类（勿用 is-hover，避开 EP 类命名空间）
.el-card.is-interactive:hover { box-shadow: var(--mp-shadow-surface-hover); }
.el-card__header {
  border-bottom: 1px solid var(--mp-divider);
  font-family: var(--mp-font-bold);
  font-weight: 700; // 单 family 字体方案下必须显式配重
}
```

> **shadow prop 已被压平为装饰**：EP 的 `shadow="always"/"hover"` 只剩占位语义（一律 flat），交互 hover 反馈一律挂 `.is-interactive`，勿依赖 shadow prop 期待 hover 效果。

---

## 7. Token 分层

| 层级 | 范围 | 说明 |
|---|---|---|
| **核心 token** | 主色 / 极光 / 字体 / 表面 / 文字 / 边框 / 圆角 / 阴影 / 间距 | 默认启用，定义 Aurora 观感，勿删 |
| **业务扩展 token** | `--mp-risk-*` / `--mp-chart-*` / `--mp-grade-*` | 公卫监测类示例值，**可删 / 可改值**；非 Aurora 核心 |

业务项目按需保留或整段删除业务扩展 token；核心 token 改值即换肤（全站含 EP 自动跟随）。

### 7.1 其余核心 token 速查（值以 `styles/index.scss` 为准）

| 类别 | token | 值 / 说明 |
|---|---|---|
| 表面（毛玻璃） | `--mp-surface-glass` / `--mp-surface-glass-light` | `rgba(255,255,255,0.72)` / `0.6` —— 全站统一玻璃底，勿再硬编码白值 |
| 极光底色 | `--mp-bg-aurora-top` / `--mp-bg-aurora-bottom` | `#fdfdff` / `#f3f6fb`（PortalLayout `.aurora` 基线渐变） |
| 语义状态 | `--mp-success` / `--mp-warning` / `--mp-danger` / `--mp-info` | `#22c55e` / `#f97316` / `#dc2626` / **`#707070`（中性灰，见 §6.1）** |
| 间距 | `--mp-space-1` … `--mp-space-6` | 4 / 8 / 12 / 16 / 24 / 32px |
| 布局 | `--mp-header-height` | `56px` |
| 过渡 | `--mp-duration-fast` / `--mp-duration-normal` | `0.2s` / `0.3s` |
| 层级 | `--mp-z-aurora` / `-content` / `-footer` / `-header` | 0 / 1 / 2 / 100 |
| 字体补充 | `--mp-font-display` / `--mp-font-bold` | 单 family「Alibaba PuHuiTi 3」，用时配 `font-weight: 900` / `700` |
| 工具类 | `.text-fluid-sm` / `.text-fluid-md` / `.w-full` / `.op-50` / `.text-xs` | 流体字号与高频工具类，随 styles/ 全局生效 |
| 基础样式 | `body` 14px 基准 + 细滚动条 | 由 index.scss 全局提供，项目内勿重复定义 |

---

## 8. 新增页面 Checklist

新建业务页 / 组件时逐条核对：

- [ ] **边框**：卡片边界走 `box-shadow`（`--mp-shadow-*` 或 `--mp-hairline-edge`），不写 `border: …solid`。
- [ ] **颜色**：用 `--mp-*` token，不硬编码十六进制（主色 `--mp-primary`、文字 `--mp-text*`、表面 `--mp-surface*`、玻璃底 `--mp-surface-glass`）。
- [ ] **圆角**：默认 `--mp-radius-md`（8px），仅 Dialog / 大卡用 `lg`。
- [ ] **阴影**：默认 `--mp-shadow-surface`，hover 切 `surface-hover`，浮层用 `overlay`。
- [ ] **字体**：标题用 `--mp-font-display`（配 `font-weight: 900`） / `--mp-font-bold`（配 `700`），正文继承 `--mp-font-sans`。
- [ ] **EP 组件**：直接用 `el-card` / `el-button` 等，无需自写主题（§6 已全局对齐）；可交互 el-card 挂 `.is-interactive`。
- [ ] **响应式**：断点用 `responsive.scss` 的 mixin（`@include mobile { … }`；前置接线：vite `additionalData` 全局注入或组件内局部 `@use`，见 README）。
- [ ] **间距**：优先 `--mp-space-*`，不随手写魔法 px。
- [ ] **图标**：确认 iconify-icon 运行时已引入（CDN script 或离线替代 `@iconify/vue` / SVG sprite，见 README）。
- [ ] **键盘焦点**：可交互元素有 `:focus-visible` 焦点环，不用裸 `outline: none`。
- [ ] **触摸目标**：触屏可达的按钮 ≥ 44px（`@include touch-target`）。

---

## 9. 禁止行为

1. **禁止裸 `box-shadow` / `border-radius` 硬编码**（如 `box-shadow: 0 4px 16px rgba(…)`、`border-radius: 12px`）—— 必须经 token。
2. **禁止卡片用 `border` 画边界** —— 见 §5 铁律，一律走 shadow hairline edge。
3. **禁止传统 Material 大投影**（如 `0 8px 24px rgba(0,0,0,0.3)` 浓重墨块）—— 用 hairline 体系。
4. **禁止彩色阴影**，**除了** `--mp-shadow-surface-hover` 的品牌蓝 ring —— 全站彩色阴影仅此一处。

---

## 10. 深色主题（备查）

Aurora 当前仅亮色。未来引入深色模式时：用 `:root[data-theme='dark']` 整段覆盖核心 token（背景 / 表面 / 文字 / 阴影各档）即可，组件代码无需改动；EP 侧另需引入其官方 dark css-vars 并同样映射到 `--mp-*`。

---

## 11. 参考范式（`examples/`）

以下 `.vue` 是上述规则的可拷贝参考实现（非可运行应用，仅示范「如何用 token 组装」）。需要时整段复制、按项目实际改业务字段：

| 文件 | 演示的规则 |
|---|---|
| `examples/PortalLayout.vue` | 极光背景（纯 CSS，零资源依赖）—— `--mp-glow-*` / `--mp-bg-aurora-*` 的用法 |
| `examples/AppHeader.vue` | 双态毛玻璃顶栏 —— `--mp-hairline-contact` 做 scroll 后分割线；键盘焦点环与 aria 范式 |
| `examples/AppFooter.vue` | 透明底栏；内容分隔线用 1px div + `background: var(--mp-border-subtle)`（§5 允许的内容分隔，非卡片边界） |
| `examples/HairlineCardExample.vue` | hairline 卡三式 —— §5 铁律 + §6.3 全局 ElCard 主题的对照（示例小卡用 `flat` 属 §3 允许档） |
