# Changelog

本仓库遵循 [Semantic Versioning](https://semver.org/)。`styles/index.scss` 头部带 `@version` 指纹，拷贝方据此对号入座。

## 0.2.2 (2026-09-11)

v3 复评 Phase D 开放项闭环（设计拍板 + 搭车修）：

- **fix(styles)**: N3——`--el-box-shadow-light` 改映射 contact+ambient+top-glow 组合（无 ring）：popper/popover/tooltip/select 下拉等小浮层不再与自带 border 叠双 hairline，大 ambient 不再压小浮层；overlay（含 ring）保留给 Dialog/Drawer 等模态档（`--el-box-shadow` 基座 / `-dark`）。
- **fix(styles)**: N5——新增 `--mp-text-disabled: #a5a5aa`，`--el-text-color-disabled` 改指向之：禁用态明显浅于 placeholder（#767676），恢复禁用可供性。
- **refactor(tokens)**: 新增 `--mp-primary-rgb: 37, 99, 235`；hover 蓝 ring 与 `--mp-primary-bg(-hover)` 改 `rgba(var(--mp-primary-rgb), α)` 引用——换肤时交互反馈色自动跟随（刻意不用 color-mix，避免复发 N1 的 IACVT 问题）。
- **fix(styles)**: light-3~9 hex 兜底值按 EP 公式重算（#6692f1 / #92b1f5 / #bed0f8 / #d3e0fb / #e9effd），兜底与公式档观感一致。
- **fix(examples)**: nav-item 44px 并 `@include touch-target`（≥1024px 的 iPad 横屏触屏可达）；drawer-item 同步挂 mixin；`.user-name` 补 nowrap+ellipsis（长用户名不撑高触发器）。
- **docs**: §4 注明 top-glow 0.5/0.6 为有意分级；§6.2 更新阴影两档映射；§7.1 补文字六级层级（含新增 disabled）与 `--mp-primary-rgb`；§7 补 `getComputedStyle` 图表读取示例。

## 0.2.1 (2026-09-11)

Delta 复评（v3）当轮热修，修复 0.2.0 自引入的两处确定性缺陷：

- **fix(styles)**: light-3~9 的 color-mix 公式行移入 `@supports` 隔离——自定义属性「后行非法回退前行」不成立（级联后者恒胜 + IACVT 回退 initial 而非前行 hex），原写法在不支持 color-mix 的内核（Chrome <111 / Safari <16.2 / Firefox <113，含 iOS 15 WKWebView）上主色派生色整层失效（背景变透明）。
- **fix(styles)**: `.el-card__header` 补 `font-weight: 700`——字体统一单 family 后仅改 family 不再产生粗体（0.2.0 回归）。
- **docs**: §6.1 改述 @supports 机制；§6.3 片段补 transition 行与 header 字重，并注明「EP shadow prop 已被压平为装饰，交互反馈一律挂 `.is-interactive`」。
- **chore**: 补记 0.2.0 一处遗漏的行为变化——顶栏 `transition` 移除了 `backdrop-filter` 项（blur 随 background 淡入，不再单独过渡）；移除本文件对外部归档路径的引用。

## 0.2.0 (2026-09-11)

v2 全量契约级审查后的修复（8 Critical / 15 Warning / 10 Suggestion，28 项任务全处理）：

- **fix(styles)**: 全局 ElCard 主题补 `.is-always-shadow` / `.is-hover-shadow` 同特异性压回 + 映射 `--el-box-shadow-*` → `--mp-shadow-*`（修复默认 el-card 渲染 EP 灰投影、hairline 丢失）。
- **fix(styles)**: EP 主色/语义色改 `var(--mp-*)` 引用、light-3~9 改 `color-mix`（hex 兜底）——「改值即换肤全站自动跟随」成立；light-N 取 EP 官方公式口径，与 0.1.0 手调值略有出入。
- **fix(styles)**: `--mp-info` 收敛为中性灰 `#707070`（与 `--el-color-info` 一致，消除 token 矛盾）。
- **fix(styles)**: `--mp-text-placeholder` `#999999 → #767676`（WCAG 1.4.3）。
- **fix(styles/examples)**: 四处 `backdrop-filter` 补 `-webkit-` 前缀（WKWebView / Safari ≤17）。
- **fix(fonts)**: 三个 `@font-face` 统一单 family「Alibaba PuHuiTi 3」三档 weight（400/700/900），业务侧 700/900 命中真实字面，不再合成加粗。
- **fix(examples)**: 范式断点改用 `responsive.scss` mixin（新增 `below-desktop`），消除 768/1024 双重归属；触摸目标 44px；头像下拉/品牌改 `<button>` + `:focus-visible` 焦点环（WCAG 2.1.1/2.4.7）；汉堡补 `aria-expanded`，导航 active 补 `aria-current="page"`。
- **refactor(tokens)**: 新增 `--mp-surface-glass(-light)` / `--mp-bg-aurora-top/bottom` / `--mp-duration-fast/normal` / `--mp-z-*`；替换全部硬编码玻璃底、极光渐变、z-index、过渡时长；`--mp-chart-1` 改 `var(--mp-primary)`。
- **chore(examples)**: 删死代码 `.el-card.is-hover:hover` border-color 行；交互钩子类 `is-hover → is-interactive`（避开 EP 类命名空间）；HairlineCardExample 去掉 `shadow="hover"` 双轨。
- **docs**: README 增 iconify-icon script 引入步骤、「离线环境」「升级」「回写」三节；docs/design-tokens.md 补全量 token 速查（§7.1）、checklist 增图标/焦点/触摸目标/间距条目、§10 深色主题改写、§11 范式描述对齐实现。
- **chore**: 版本指纹（index.scss 头部 `@version`）+ 本 CHANGELOG + MIT LICENSE 文件 + `package.json` 补 repository/bugs。

## 0.1.0 (2026-07-29)

- feat: 初始化 Aurora 设计语言仓库（token + 规范 + 可拷贝范式，面向智能体读者）。
