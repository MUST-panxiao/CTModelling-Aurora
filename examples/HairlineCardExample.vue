<script setup lang="ts">
// 参考范式 · hairline 卡片三式
// ─────────────────────────────────────────────────────────────
// 演示 Aurora 卡片边框铁律（见 docs/design-tokens.md §5）：
// 边界一律走 box-shadow 的 hairline edge，禁用 border。
//
// ① <el-card>          —— 全局主题自动套用（index.scss 末段的 .el-card 规则），无需自写样式
// ② 自定义 .hairline-card —— 用 --mp-shadow-flat，hover 切 --mp-shadow-surface-hover
// ③ .hairline-pill     —— 极轻 edge（--mp-hairline-edge）做胶囊 / chip 边界
//
// 依赖 styles/index.scss 的 --mp-* token（拷进项目后自动生效）。
</script>

<template>
  <div class="hairline-examples">
    <!-- ① 全局 ElCard 主题：border:none + shadow-flat + backdrop-blur，由 index.scss 全局赋予 -->
    <el-card class="is-hover" shadow="hover">
      <template #header>全局 ElCard（自动毛玻璃 hairline）</template>
      <p>这个 <code>&lt;el-card&gt;</code> 没有任何自定义样式 —— 边界、圆角、毛玻璃全由 <code>index.scss</code> 的全局 <code>.el-card</code> 规则赋予。</p>
    </el-card>

    <!-- ② 自定义 hairline 卡：border:none + shadow-flat，hover 切 surface-hover -->
    <div class="hairline-card">
      <span class="card-title">自定义 hairline 卡</span>
      <span class="card-desc">边界由 <code>--mp-shadow-flat</code>（内含 0 0 0 1px edge）提供，不写 border；hover 切 <code>--mp-shadow-surface-hover</code> 的品牌蓝 ring。</span>
    </div>

    <!-- ③ hairline 胶囊：极轻 edge 做边界 -->
    <div class="hairline-pill">
      <iconify-icon icon="tabler:sparkles" width="14"></iconify-icon>
      <span>hairline 胶囊（--mp-hairline-edge）</span>
    </div>
  </div>
</template>

<style scoped lang="scss">
.hairline-examples {
  display: flex;
  flex-direction: column;
  gap: 16px;
  max-width: 480px;
}

// ② 自定义 hairline 卡 —— 卡片边界铁律：border:none + box-shadow edge
.hairline-card {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding: 18px 20px;
  background: rgba(255, 255, 255, 0.72);
  border: none; // ✅ 禁用 border
  border-radius: var(--mp-radius-lg);
  box-shadow: var(--mp-shadow-flat); // ✅ 边界走 shadow 的 hairline edge
  backdrop-filter: blur(6px);
  transition: box-shadow 0.2s ease;

  &:hover {
    box-shadow: var(--mp-shadow-surface-hover); // hover：唯一允许的彩色 ring
  }
}

.card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--mp-text);
}

.card-desc {
  font-size: 13px;
  line-height: 1.6;
  color: var(--mp-text-muted);
}

// ③ hairline 胶囊
.hairline-pill {
  align-self: flex-start;
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 14px;
  background: rgba(255, 255, 255, 0.6);
  border: none; // ✅ 禁用 border
  border-radius: var(--mp-radius-pill);
  box-shadow: var(--mp-hairline-edge); // ✅ 极轻 edge
  backdrop-filter: blur(6px);
  font-size: 13px;
  color: var(--mp-text-regular);
}

code {
  padding: 1px 5px;
  border-radius: var(--mp-radius-sm);
  background: var(--mp-bg-secondary);
  color: var(--mp-primary-deep);
}
</style>
