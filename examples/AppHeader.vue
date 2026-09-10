<script setup lang="ts">
// 参考范式 · 双态毛玻璃顶栏
// ─────────────────────────────────────────────────────────────
// 顶部透明融合极光背景，scrollY>8 后切 backdrop-blur + hairline 分割线。
// 桌面横向导航 + 移动端抽屉；右栏未登录显「登录」、已登录显头像下拉。
//
// 拷进你的项目：把下方 isLoggedIn / userName / handleLogout 替换为你的 auth store
// （如 useAuthStore() 的 isAuthenticated / user / logout）。其余结构与样式可直接用。
// 依赖 styles/index.scss 的 --mp-* token；图标用 Iconify（CDN：<iconify-icon>，
// 需在 index.html 引入其 script 标签，离线桌面端见 README「离线环境」）。
// 断点用 responsive.scss 的 mixin（需 vite additionalData 或局部 @use 接线，见 README）。
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessageBox, ElMessage } from 'element-plus'

const route = useRoute()
const router = useRouter()

// 参考范式占位登录态（接入 auth store 后替换这三个）
const isLoggedIn = ref(false)
const userName = ref('')

interface NavItem {
  label: string
  name: string
  icon: string
  active: boolean
}

// 横向导航（首页 + 业务模块占位）；业务项目按需增删
const navItems = computed<NavItem[]>(() => [
  { label: '首页', name: 'Home', icon: 'tabler:home', active: route.name === 'Home' },
  { label: '工作台', name: 'Dashboard', icon: 'tabler:layout-dashboard', active: route.name === 'Dashboard' },
  // { label: '模块A', name: 'ModuleA', icon: 'tabler:world-search', active: route.name === 'ModuleA' },
])

const displayName = computed(() => userName.value || '未登录')
const avatarLetter = computed(() => (userName.value || 'U').charAt(0).toUpperCase())

// 双态毛玻璃：顶部透明融合，scrollY>8 后 backdrop-blur
const scrolled = ref(false)
function onScroll(): void {
  scrolled.value = window.scrollY > 8
}
onMounted(() => {
  onScroll()
  window.addEventListener('scroll', onScroll, { passive: true })
})
onBeforeUnmount(() => window.removeEventListener('scroll', onScroll))

const drawerVisible = ref(false)
const loggingOut = ref(false)

function go(name: string): void {
  router.push({ name })
  drawerVisible.value = false
}

function goLogin(): void {
  router.push({ name: 'Login' })
}

async function handleLogout(): Promise<void> {
  try {
    await ElMessageBox.confirm('确认退出登录？', '提示', {
      confirmButtonText: '退出',
      cancelButtonText: '取消',
      type: 'warning',
    })
  } catch {
    return // 用户取消
  }
  loggingOut.value = true
  try {
    // 接入 auth store 后换成 authStore.logout()
    isLoggedIn.value = false
    userName.value = ''
    ElMessage.success('已退出登录')
    await router.replace({ name: 'Login' })
  } finally {
    loggingOut.value = false
  }
}

function onCommand(command: string): void {
  if (command === 'logout') handleLogout()
}
</script>

<template>
  <header class="app-header" :class="{ 'is-transparent': !scrolled, 'is-blur': scrolled }">
    <!-- 左：品牌 -->
    <div class="header-left">
      <button
        v-if="isLoggedIn"
        type="button"
        class="icon-btn hamburger"
        aria-label="菜单"
        :aria-expanded="drawerVisible"
        @click="drawerVisible = true"
      >
        <iconify-icon icon="tabler:menu-2" width="20"></iconify-icon>
      </button>
      <button type="button" class="brand" @click="go('Home')">
        <!-- 替换为你的 logo -->
        <span class="brand-mark">A</span>
        <span class="brand-title">产品名称</span>
      </button>
    </div>

    <!-- 中：横向导航（桌面，仅登录态显示） -->
    <nav v-if="isLoggedIn" class="main-nav">
      <button
        v-for="item in navItems"
        :key="item.name"
        type="button"
        class="nav-item"
        :class="{ active: item.active }"
        :aria-current="item.active ? 'page' : undefined"
        @click="go(item.name)"
      >
        <iconify-icon :icon="item.icon" class="nav-icon"></iconify-icon>
        <span>{{ item.label }}</span>
      </button>
    </nav>

    <!-- 右：未登录显「登录」；已登录显头像下拉 -->
    <div class="header-right">
      <el-button v-if="!isLoggedIn" type="primary" round @click="goLogin">
        登录
      </el-button>
      <el-dropdown v-else trigger="click" @command="onCommand">
        <button type="button" class="user-trigger">
          <el-avatar :size="30" class="user-avatar">{{ avatarLetter }}</el-avatar>
          <span class="user-meta">
            <span class="user-name">{{ displayName }}</span>
          </span>
          <iconify-icon icon="tabler:chevron-down" class="caret"></iconify-icon>
        </button>
        <template #dropdown>
          <el-dropdown-menu>
            <!-- 业务项目可在此追加「数据管理 / 用户管理」等角色可见项 -->
            <el-dropdown-item command="logout" :disabled="loggingOut">
              <iconify-icon icon="tabler:logout" class="dd-icon"></iconify-icon>
              <span>退出登录</span>
            </el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>
    </div>

    <!-- 移动端抽屉 -->
    <el-drawer v-model="drawerVisible" direction="ltr" :size="260" :show-close="false" title="导航">
      <nav class="drawer-nav">
        <button
          v-for="item in navItems"
          :key="item.name"
          type="button"
          class="drawer-item"
          :class="{ active: item.active }"
          :aria-current="item.active ? 'page' : undefined"
          @click="go(item.name)"
        >
          <iconify-icon :icon="item.icon" class="nav-icon"></iconify-icon>
          <span>{{ item.label }}</span>
        </button>
      </nav>
    </el-drawer>
  </header>
</template>

<style scoped lang="scss">
.app-header {
  height: var(--mp-header-height);
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 0 max(24px, calc((100vw - 1280px) / 2));
  position: sticky;
  top: 0;
  z-index: var(--mp-z-header);
  // backdrop-filter 不做过渡（各内核表现不一）：blur 随 background 淡入即可
  transition: background var(--mp-duration-normal) ease, box-shadow var(--mp-duration-normal) ease;

  // 顶部：透明无阴影，透出极光光晕
  &.is-transparent {
    background: transparent;
    box-shadow: none;
  }
  // 滚动后：毛玻璃半透明 + 细分割线（hairline-contact）
  &.is-blur {
    background: var(--mp-surface-glass);
    -webkit-backdrop-filter: blur(12px); // WKWebView / Safari ≤17
    backdrop-filter: blur(12px);
    box-shadow: var(--mp-hairline-contact);
  }
}

// ── 左：品牌 ──
.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
  min-width: 0;
}

.brand {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0;
  border: none;
  background: none;
  font: inherit;
  cursor: pointer;

  &:focus-visible {
    outline: 2px solid var(--mp-primary);
    outline-offset: 2px;
  }
}

// 品牌 mark 占位（换成你的 <img> logo）
.brand-mark {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: var(--mp-radius-md);
  background: linear-gradient(135deg, var(--mp-primary), var(--mp-primary-lighter));
  color: var(--mp-surface);
  font-weight: 700;
  flex-shrink: 0;
}

.brand-title {
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 0.5px;
  color: var(--mp-text);
  white-space: nowrap;
}

// ── 中：主菜单 ──
.main-nav {
  display: flex;
  align-items: center;
  gap: 4px;
  flex-shrink: 0;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 6px;
  @include touch-target; // 44px 触摸目标（≥1024px 的 iPad 横屏触屏可达桌面导航）
  height: 44px;
  padding: 0 14px;
  background: transparent;
  border: none;
  border-radius: var(--mp-radius-md);
  color: var(--mp-text-regular);
  font-size: 14px;
  font-family: inherit;
  cursor: pointer;
  transition: background var(--mp-duration-fast), color var(--mp-duration-fast);

  &:hover {
    background: var(--mp-surface-hover);
    color: var(--mp-text);
  }

  &.active {
    background: var(--mp-primary-bg);
    color: var(--mp-primary);
    font-weight: 600;
  }
}

.nav-icon {
  font-size: 17px;
  color: currentColor;
  vertical-align: middle;
}

// ── 右：头像下拉 ──
.header-right {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  flex: 1;
}

.user-trigger {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 4px 8px 4px 4px;
  border: none;
  background: transparent;
  font: inherit;
  border-radius: var(--mp-radius-pill);
  cursor: pointer;
  transition: background var(--mp-duration-fast) ease;

  &:hover {
    background: var(--mp-surface-hover);
  }

  &:focus-visible {
    outline: 2px solid var(--mp-primary);
    outline-offset: 2px;
  }
}

.user-avatar {
  background: var(--mp-primary);
  color: var(--mp-surface);
  font-weight: 600;
  flex-shrink: 0;
}

.user-meta {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  line-height: 1.2;
}

.user-name {
  font-size: 13px;
  color: var(--mp-text);
  font-weight: 500;
  max-width: 160px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis; // 长用户名不撑高触发器
}

.caret {
  font-size: 16px;
  color: var(--mp-text-muted);
}

// 下拉项图标对齐（el-dropdown-item 内）
:deep(.dd-icon) {
  font-size: 16px;
  margin-right: 6px;
  vertical-align: -3px;
  color: var(--mp-text-regular);
}

// ── 汉堡（默认隐藏，移动端显示；44px 触摸目标，iOS HIG） ──
.icon-btn {
  display: none;
  align-items: center;
  justify-content: center;
  width: 44px;
  height: 44px;
  border: none;
  background: transparent;
  color: var(--mp-text);
  border-radius: var(--mp-radius-md);
  cursor: pointer;

  &:hover {
    background: var(--mp-surface-hover);
  }

  &:focus-visible {
    outline: 2px solid var(--mp-primary);
    outline-offset: 2px;
  }
}

// ── 抽屉 ──
.drawer-nav {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 8px;
}

.drawer-item {
  display: flex;
  align-items: center;
  gap: 12px;
  @include touch-target; // 44px 触摸目标
  height: 44px;
  padding: 0 16px;
  border: none;
  background: transparent;
  border-radius: var(--mp-radius-md);
  color: var(--mp-text-regular);
  font-size: 15px;
  font-family: inherit;
  cursor: pointer;
  transition: background var(--mp-duration-fast), color var(--mp-duration-fast);

  &:hover {
    background: var(--mp-surface-hover);
  }

  &.active {
    background: var(--mp-primary-bg);
    color: var(--mp-primary);
    font-weight: 600;
  }
}

// ── 响应式（断点走 responsive.scss 的 mixin；接线见 README「智能体使用流程」） ──
@include below-desktop {
  .main-nav {
    display: none;
  }
  .icon-btn.hamburger {
    display: flex;
  }
}

@include mobile {
  .app-header {
    padding: 0 12px;
    gap: 8px;
  }
  .user-meta {
    display: none;
  }
  .caret {
    display: none;
  }
  .brand-title {
    font-size: 15px;
  }
}
</style>
