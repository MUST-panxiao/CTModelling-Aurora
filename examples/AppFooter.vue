<script setup lang="ts">
// 参考范式 · 透明底栏
// ─────────────────────────────────────────────────────────────
// 透明底透出极光背景，左品牌 + 右版权。业务项目按需补「公众号二维码 / 友链 / 备案号」。
// 依赖 styles/index.scss 的 --mp-* token。
</script>

<template>
  <footer class="app-footer">
    <div class="footer-container">
      <div class="footer-brand">
        <!-- 品牌 mark 占位（换成你的 <img> logo） -->
        <span class="footer-mark">A</span>
        <div class="footer-brand-text">
          <!-- 通用化占位：替换为你的产品名 / 机构 -->
          <span class="footer-name">产品名称</span>
          <span class="footer-org">[你的机构名称 · 团队]</span>
        </div>
      </div>

      <div class="footer-divider" />
      <div class="footer-copy">© 2026 [产品名称]. All rights reserved.</div>
    </div>
  </footer>
</template>

<style scoped lang="scss">
// 透明底，透出极光背景
.app-footer {
  position: relative;
  z-index: 2;
  width: 100%;
}

.footer-container {
  max-width: 1280px;
  margin: 0 auto;
  padding: 40px 24px 28px;
  display: flex;
  flex-direction: column;
  gap: 28px;
}

.footer-brand {
  display: flex;
  align-items: center;
  gap: 12px;

  .footer-mark {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 32px;
    height: 32px;
    border-radius: var(--mp-radius-md);
    background: linear-gradient(135deg, var(--mp-primary), var(--mp-primary-lighter));
    color: var(--mp-surface);
    font-weight: 700;
    flex-shrink: 0;
  }

  .footer-brand-text {
    display: flex;
    flex-direction: column;
    line-height: 1.3;
  }

  .footer-name {
    font-size: 18px;
    font-weight: 700;
    letter-spacing: 0.5px;
    color: var(--mp-text);
  }

  .footer-org {
    font-size: 13px;
    color: var(--mp-text-muted);
  }
}

// 内容分隔线（非卡片边界，允许用 1px solid）
.footer-divider {
  height: 1px;
  background: var(--mp-border-subtle);
}

.footer-copy {
  font-size: 12px;
  color: var(--mp-text-muted);
}

@media (max-width: 768px) {
  .footer-container {
    padding: 28px 16px 20px;
    gap: 22px;
  }
}
</style>
