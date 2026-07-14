<script setup>
import { computed, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { useAuthStore } from '@/stores/auth'

const route = useRoute()
const router = useRouter()
const authStore = useAuthStore()
const mobileNavVisible = ref(false)

const menuItems = [
  { path: '/', label: '首页', summary: '品牌门户' },
  { path: '/video-contest', label: '视频大赛', summary: '影像专题' },
  { path: '/emb-contest', label: '绣红旗大赛', summary: '作品征集' },
  { path: '/red-culture', label: '红旗文化', summary: '文化专题' },
  { path: '/public-welfare', label: '公益纪实', summary: '公益行动' },
  { path: '/skill-teaching', label: '技艺教学', summary: '非遗传承' },
  { path: '/interaction', label: '互动交流', summary: '社区参与' },
  { path: '/contact', label: '联系我们', summary: '品牌沟通' },
]

const activePath = computed(() => route.path)
const username = computed(() => authStore.user?.display_name || authStore.user?.username || '平台访客')
const showAdminEntry = computed(() => ['admin', 'moderator'].includes(authStore.user?.role))

function isActive(path) {
  return path === '/' ? activePath.value === '/' : activePath.value.startsWith(path)
}

function closeMobileNav() {
  mobileNavVisible.value = false
}

function navigateTo(path) {
  closeMobileNav()
  router.push(path)
}

function handleLogout() {
  authStore.logout()
  closeMobileNav()
  ElMessage.success('已退出登录')
  router.push('/')
}
</script>

<template>
  <div class="app-shell">
    <header class="shell-header">
      <div class="shell-header__brand" @click="navigateTo('/')">
        <p class="brand-kicker">XIU HONG QI</p>
        <div class="brand-title">绣红旗</div>
      </div>

      <nav class="shell-nav" aria-label="主导航">
        <button
          v-for="item in menuItems"
          :key="item.path"
          type="button"
          class="shell-nav__item"
          :class="{ 'shell-nav__item--active': isActive(item.path) }"
          @click="navigateTo(item.path)"
        >
          {{ item.label }}
        </button>
      </nav>

      <div class="shell-header__actions">
        <span class="user-name">{{ username }}</span>
        <el-button v-if="showAdminEntry" size="small" plain @click="navigateTo('/admin')">后台</el-button>
        <el-button v-if="authStore.isLoggedIn" size="small" plain @click="navigateTo('/user')">用户中心</el-button>
        <el-button v-if="authStore.isLoggedIn" size="small" type="danger" @click="handleLogout">退出</el-button>
        <el-button v-else size="small" type="danger" @click="navigateTo('/login')">登录</el-button>
      </div>

      <button class="shell-mobile-toggle" @click="mobileNavVisible = true" aria-label="打开菜单">
        <span></span><span></span><span></span>
      </button>
    </header>

    <main class="shell-main">
      <router-view />
    </main>

    <footer class="shell-footer">
      <div class="footer-card">
        <div>
          <p class="footer-kicker">PLATFORM FOOTER</p>
          <h3>红色文化传播 · 非遗技艺传承 · 多元参与体验</h3>
        </div>
        <p>
          平台围绕文化展示、赛事活动、公益传播与互动参与展开，致力于构建更正式、更完整、
          更具品牌感的数字展示体验。
        </p>
        <p class="footer-company">@ 旗芯数智（陕西）科技有限公司 版权所有</p>
      </div>
    </footer>

    <el-drawer v-model="mobileNavVisible" direction="rtl" :with-header="false" class="shell-mobile-drawer">
      <div class="shell-mobile-drawer__inner">
        <div class="shell-mobile-drawer__brand" @click="navigateTo('/')">
          <p class="brand-kicker">XIU HONG QI</p>
          <h2>绣红旗</h2>
        </div>

        <div class="shell-mobile-drawer__user">
          <span>{{ username }}</span>
        </div>

        <nav class="shell-mobile-drawer__nav" aria-label="移动端导航">
          <button
            v-for="item in menuItems"
            :key="item.path"
            type="button"
            class="shell-nav__item shell-nav__item--mobile"
            :class="{ 'shell-nav__item--active': isActive(item.path) }"
            @click="navigateTo(item.path)"
          >
            {{ item.label }}
          </button>
        </nav>

        <div class="shell-mobile-drawer__actions">
          <el-button v-if="showAdminEntry" plain @click="navigateTo('/admin')">进入后台</el-button>
          <el-button v-if="authStore.isLoggedIn" plain @click="navigateTo('/user')">用户中心</el-button>
          <el-button v-if="authStore.isLoggedIn" type="danger" @click="handleLogout">退出登录</el-button>
          <el-button v-else type="danger" @click="navigateTo('/login')">登录 / 注册</el-button>
        </div>
      </div>
    </el-drawer>
  </div>
</template>

<style scoped lang="scss">
.app-shell {
  position: relative;
  padding: 18px;
}

.shell-header,
.shell-main,
.footer-card {
  width: min(1400px, 100%);
  margin: 0 auto;
}

/* ========== Header: 横向导航条 ========== */
.shell-header {
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 0 28px;
  height: 64px;
  border: 1px solid rgba(234, 217, 199, 0.9);
  border-radius: 16px;
  background: #fffaf5;
  box-shadow: var(--xhq-shadow-md);
}

.shell-header__brand {
  display: flex;
  align-items: baseline;
  gap: 10px;
  cursor: pointer;
  flex-shrink: 0;
}

.brand-kicker {
  margin: 0;
  color: var(--xhq-accent);
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 2px;
}

.brand-title {
  margin: 0;
  font-family: "Source Han Serif SC", "STSong", "SimSun", serif;
  font-size: 20px;
  line-height: 1;
  color: var(--xhq-primary-deep);
  white-space: nowrap;
}

/* ========== Nav: 横向栏目 ========== */
.shell-nav {
  display: flex;
  align-items: center;
  gap: 4px;
  flex: 1;
  overflow-x: auto;
  scrollbar-width: none;

  &::-webkit-scrollbar {
    display: none;
  }
}

.shell-nav__item {
  display: flex;
  align-items: center;
  height: 40px;
  padding: 0 16px;
  font-size: 14px;
  font-weight: 600;
  color: var(--xhq-text-muted);
  background: none;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  white-space: nowrap;
  transition: color 0.2s ease, background 0.2s ease;
  position: relative;

  &::after {
    content: "";
    position: absolute;
    bottom: 4px;
    left: 50%;
    transform: translateX(-50%) scaleX(0);
    width: 20px;
    height: 2px;
    border-radius: 1px;
    background: var(--xhq-primary);
    transition: transform 0.25s ease;
  }

  &:hover {
    color: var(--xhq-primary);
    background: rgba(152, 27, 30, 0.05);
  }

  &--active {
    color: var(--xhq-primary);

    &::after {
      transform: translateX(-50%) scaleX(1);
    }
  }
}

/* ========== Actions: 右侧操作区 ========== */
.shell-header__actions {
  display: flex;
  align-items: center;
  gap: 10px;
  flex-shrink: 0;
}

.user-name {
  font-size: 13px;
  color: var(--xhq-accent);
  font-weight: 600;
  white-space: nowrap;
}

/* ========== 移动端汉堡按钮 ========== */
.shell-mobile-toggle {
  display: none;
  flex-direction: column;
  gap: 5px;
  width: 28px;
  padding: 4px;
  background: none;
  border: none;
  cursor: pointer;

  span {
    display: block;
    height: 2px;
    background: var(--xhq-primary-deep);
    border-radius: 1px;
    transition: transform 0.25s ease;

    &:nth-child(2) {
      width: 70%;
      margin-left: auto;
    }

    &:nth-child(3) {
      width: 50%;
      margin-left: auto;
    }
  }
}

/* ========== Main & Footer ========== */
.shell-main {
  margin-top: 18px;
}

.shell-footer {
  padding: 18px 0 30px;
}

.footer-company {
  margin: 8px 0 0;
  color: var(--xhq-primary-deep);
  font-weight: 700;
}

.footer-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
  padding: 20px 24px;
  border-radius: 26px;
  border: 1px solid rgba(234, 217, 199, 0.9);
  background: rgba(255, 250, 244, 0.9);
  box-shadow: var(--xhq-shadow-md);
}

.footer-card h3 {
  margin: 8px 0 0;
  font-size: 22px;
  color: var(--xhq-primary-deep);
}

.footer-card p {
  margin: 0;
  color: var(--xhq-text-muted);
  line-height: 1.8;
}

.footer-kicker {
  margin: 0;
  color: var(--xhq-accent);
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 2px;
}

/* ========== 移动端抽屉 ========== */
.shell-mobile-drawer__inner {
  display: flex;
  flex-direction: column;
  gap: 16px;
  min-height: 100%;
}

.shell-mobile-drawer__brand {
  padding: 4px 0 8px;
  cursor: pointer;
}

.shell-mobile-drawer__brand h2 {
  margin: 10px 0 0;
  color: var(--xhq-primary-deep);
  font-family: "Source Han Serif SC", "STSong", "SimSun", serif;
  font-size: 26px;
}

.shell-mobile-drawer__user {
  display: flex;
  align-items: center;
  padding: 14px 16px;
  border: 1px solid #eadfce;
  border-radius: 16px;
  background: linear-gradient(180deg, rgba(255, 253, 250, 0.96), rgba(252, 244, 234, 0.94));
  color: var(--xhq-primary-deep);
  font-weight: 700;
  font-size: 16px;
}

.shell-mobile-drawer__nav {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.shell-nav__item--mobile {
  justify-content: flex-start;
  height: 44px;
  padding: 0 16px;
  border-radius: 12px;

  &:hover {
    background: rgba(152, 27, 30, 0.06);
  }

  &::after {
    display: none;
  }

  &.shell-nav__item--active {
    background: rgba(152, 27, 30, 0.08);
  }
}

.shell-mobile-drawer__actions {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-top: auto;
}

.shell-mobile-drawer__actions :deep(.el-button) {
  width: 100%;
}

/* ========== 响应式 ========== */
@media (max-width: 1024px) {
  .shell-nav__item {
    padding: 0 12px;
    font-size: 13px;
  }

  .brand-title {
    font-size: 17px;
  }

  .shell-header__actions :deep(.el-button) {
    padding-inline: 12px;
    font-size: 12px;
  }
}

@media (max-width: 767px) {
  .app-shell {
    padding: 12px;
  }

  .shell-header {
    height: 56px;
    padding: 0 16px;
    gap: 12px;
    border-radius: 14px;
  }

  .brand-title {
    font-size: 17px;
  }

  .shell-nav,
  .shell-header__actions {
    display: none;
  }

  .shell-mobile-toggle {
    display: flex;
    flex-shrink: 0;
    margin-left: auto;
  }

  .shell-main {
    margin-top: 12px;
  }

  .footer-card {
    flex-direction: column;
    align-items: flex-start;
    padding: 18px;
    border-radius: 22px;
  }

  .footer-card h3 {
    font-size: clamp(18px, 5vw, 22px);
  }
}
</style>
