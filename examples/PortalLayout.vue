<script setup lang="ts">
// 参考范式 · 极光背景布局壳
// ─────────────────────────────────────────────────────────────
// 全站极光光晕背景（纯 CSS，零资源依赖）+ 顶栏 + 内容区 + 底栏。
// 拷进你的项目：把 AppHeader / AppFooter 换成你的组件，<router-view /> 放业务页。
// 依赖 styles/index.scss 的 --mp-glow-* / --mp-bg / --mp-radius-* token。
import AppHeader from './AppHeader.vue'
import AppFooter from './AppFooter.vue'
</script>

<template>
  <div class="portal-layout">
    <!-- 全站极光光晕背景：五色 gradient shape + blob，纯 CSS、fixed 铺底 -->
    <div class="aurora" aria-hidden="true">
      <span class="aurora-shape aurora-shape-1" />
      <span class="aurora-shape aurora-shape-2" />
      <span class="aurora-blob blob-green" />
      <span class="aurora-blob blob-orange" />
      <span class="aurora-blob blob-blue" />
    </div>

    <AppHeader />

    <main class="portal-main">
      <div class="portal-container">
        <!-- 你的业务页 / <router-view /> -->
        <slot />
      </div>
    </main>

    <AppFooter />
  </div>
</template>

<style scoped lang="scss">
.portal-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background: var(--mp-bg);
}

/* 全站极光光晕背景 —— fixed 铺底，内容提到 z:1 之上 */
.aurora {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  overflow: hidden;
  background: linear-gradient(180deg, #fdfdff 0%, #f3f6fb 100%);

  .aurora-shape {
    position: absolute;
    width: 55vw;
    height: 55vw;
    max-width: 820px;
    max-height: 820px;
    border-radius: var(--mp-radius-circle);
    filter: blur(48px);
    opacity: 0.32;
  }
  .aurora-shape-1 {
    top: -8%;
    left: -6%;
    transform: rotate(-18deg);
    background: radial-gradient(circle, var(--mp-glow-blue-light), transparent 70%);
  }
  .aurora-shape-2 {
    bottom: -12%;
    right: -8%;
    width: 48vw;
    height: 48vw;
    max-width: 720px;
    max-height: 720px;
    opacity: 0.28;
    transform: rotate(24deg);
    background: radial-gradient(circle, var(--mp-glow-green), transparent 70%);
  }

  .aurora-blob {
    position: absolute;
    border-radius: var(--mp-radius-circle);
    filter: blur(90px);
    opacity: 0.3;
  }
  .blob-green {
    width: 360px;
    height: 360px;
    top: 18%;
    right: 8%;
    background: radial-gradient(circle, var(--mp-glow-green), transparent 70%);
  }
  .blob-orange {
    width: 300px;
    height: 300px;
    bottom: 22%;
    left: 10%;
    background: radial-gradient(circle, var(--mp-glow-orange), transparent 70%);
    opacity: 0.24;
  }
  .blob-blue {
    width: 420px;
    height: 420px;
    top: 45%;
    left: 42%;
    transform: translate(-50%, -50%);
    background: radial-gradient(circle, var(--mp-glow-blue-mid), transparent 70%);
    opacity: 0.22;
  }
}

.portal-main {
  position: relative;
  z-index: 1; // 提到 .aurora(fixed) 之上
  padding: 20px 24px;
  // 不锁高、不用 flex：让各业务页内容自然撑开，由 body 整体滚动
}

// 内容容器居中限宽 1280；不锁高，让页面内容自然撑开、body 滚动
.portal-container {
  max-width: 1280px;
  margin: 0 auto;
  width: 100%;
}

@media (max-width: 768px) {
  .portal-main {
    padding: 12px 12px 24px;
  }
}
</style>
