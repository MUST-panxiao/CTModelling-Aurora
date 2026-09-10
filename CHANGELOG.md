# Changelog

本仓库遵循 [Semantic Versioning](https://semver.org/)。`styles/index.scss` 头部带 `@version` 指纹，拷贝方据此对号入座。

## 0.2.0 (2026-09-11)

v2 全量审查（契约级）修复，见归档 `28_CTM-Aurora/REVIEW_REPORT_v2.md`：

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
