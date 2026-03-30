<!--
/**
 * 顶部导航栏组件
 * 提供面包屑导航、页面标题、搜索、通知、语言切换、主题切换和用户信息
 * 支持响应式设计和移动端适配
 */
-->
<template>
  <!-- 顶部导航栏容器 -->
  <header class="nav-content" :class="navClasses">
    <!-- 左侧：侧边栏切换按钮和面包屑 -->
    <div class="nav-content__left">
      <!-- 侧边栏切换按钮 -->
      <button 
        class="sidebar-toggle-btn"
        @click="handleSidebarToggle"
        :aria-label="t('navigation.toggleSidebar')"
      >
        <NavIcon name="sidebar-toggle" size="large" />
      </button>

      <!-- 面包屑导航 -->
      <div class="breadcrumb-section">
        <nav class="breadcrumb" v-if="breadcrumbs.length > 0">
          <span 
            v-for="(item, index) in breadcrumbs" 
            :key="item.path || index"
            class="breadcrumb-item"
          >
            <button 
              v-if="item.path && index < breadcrumbs.length - 1"
              class="breadcrumb-link"
              @click="navigateTo(item)"
            >
              {{ item.title }}
            </button>
            <span 
              v-else
              class="breadcrumb-current page-title"
            >
              {{ item.title }}
            </span>
            <span 
              v-if="index < breadcrumbs.length - 1"
              class="breadcrumb-separator"
            >
              /
            </span>
          </span>
        </nav>
      </div>
    </div>

    <!-- 右侧：用户头像和操作按钮 -->
    <div class="nav-content__right">
      <!-- 用户头像和下拉菜单 -->
      <el-tooltip :content="t('navigation.tooltip.user')" placement="bottom">
        <el-dropdown trigger="click" @command="handleUserCommand" placement="bottom-start">
          <div class="user-avatar">
            <NavIcon name="user" />
          </div>
          <template #dropdown>
            <el-dropdown-menu class="user-dropdown-menu">
            <!-- 用户信息头部 -->
            <div class="user-info-header">
              <div class="user-avatar-large">
                <div class="avatar-icon">
                  <span class="avatar-text">{{ userInitial }}</span>
                </div>
              </div>
              <div class="user-details">
                <div class="user-name">{{ userInfo?.username || '未登录' }}</div>
                <div class="user-role">{{ userInfo?.role || '--' }}</div>
              </div>
            </div>
            <!-- 基本信息 -->
            <el-dropdown-item command="changePassword" class="dropdown-menu-item">
              <svg class="menu-icon" width="16" height="16" viewBox="0 0 16 16" fill="none">
                <path d="M12 7V5C12 2.79086 10.2091 1 8 1C5.79086 1 4 2.79086 4 5V7H3C2.44772 7 2 7.44772 2 8V14C2 14.5523 2.44772 15 3 15H13C13.5523 15 14 14.5523 14 14V8C14 7.44772 13.5523 7 13 7H12ZM6 5C6 3.89543 6.89543 3 8 3C9.10457 3 10 3.89543 10 5V7H6V5Z" fill="currentColor"/>
              </svg>
              <span class="menu-text">修改密码</span>
            </el-dropdown-item>
            <el-dropdown-item command="profile" class="dropdown-menu-item">
              <svg class="menu-icon" width="16" height="16" viewBox="0 0 16 16" fill="none">
                <path d="M8 2C6.34315 2 5 3.34315 5 5C5 6.65685 6.34315 8 8 8C9.65685 8 11 6.65685 11 5C11 3.34315 9.65685 2 8 2ZM3 5C3 2.23858 5.23858 0 8 0C10.7614 0 13 2.23858 13 5C13 7.76142 10.7614 10 8 10C5.23858 10 3 7.76142 3 5ZM2 13C2 11.3431 3.34315 10 5 10H11C12.6569 10 14 11.3431 14 13V14C14 14.5523 13.5523 15 13 15C12.4477 15 12 14.5523 12 14V13C12 12.4477 11.5523 12 11 12H5C4.44772 12 4 12.4477 4 13V14C4 14.5523 3.55228 15 3 15C2.44772 15 2 14.5523 2 14V13Z" fill="currentColor"/>
              </svg>
              <span class="menu-text">{{ t('userMenu.profile') }}</span>
            </el-dropdown-item>

            <!-- 退出 -->
            <el-dropdown-item command="logout" class="dropdown-menu-item logout-item">
              <svg class="menu-icon" width="16" height="16" viewBox="0 0 16 16" fill="none">
                <path d="M6 14H3C2.44772 14 2 13.5523 2 13V3C2 2.44772 2.44772 2 3 2H6C6.55228 2 7 1.55228 7 1C7 0.447715 6.55228 0 6 0H3C1.34315 0 0 1.34315 0 3V13C0 14.6569 1.34315 16 3 16H6C6.55228 16 7 15.5523 7 15C7 14.4477 6.55228 14 6 14ZM11.2929 4.29289C11.6834 3.90237 12.3166 3.90237 12.7071 4.29289L15.7071 7.29289C16.0976 7.68342 16.0976 8.31658 15.7071 8.70711L12.7071 11.7071C12.3166 12.0976 11.6834 12.0976 11.2929 11.7071C10.9024 11.3166 10.9024 10.6834 11.2929 10.2929L12.5858 9H6C5.44772 9 5 8.55228 5 8C5 7.44772 5.44772 7 6 7H12.5858L11.2929 5.70711C10.9024 5.31658 10.9024 4.68342 11.2929 4.29289Z" fill="currentColor"/>
              </svg>
              <span class="menu-text">{{ t('userMenu.logout') }}</span>
            </el-dropdown-item>
          </el-dropdown-menu>
        </template>
        </el-dropdown>
      </el-tooltip>

      <!-- 分割线 -->
      <div class="divider"></div>

      <!-- 操作按钮组 -->
      <div class="action-buttons">
        <!-- 文档 & GitHub 外部链接 -->
        <template v-for="link in externalLinks" :key="link.key">
          <el-tooltip :content="t(`navigation.tooltip.${link.key}`)" placement="bottom">
            <button
              class="action-btn external-link-btn"
              type="button"
              @click="handleExternalLink(link.url)"
            >
              <span class="external-link-label">{{ t(`navigation.external.${link.key}`) }}</span>
            </button>
          </el-tooltip>
        </template>

        <!-- 搜索按钮（暂时禁用） -->
        <!--
        <el-tooltip :content="t('navigation.tooltip.search')" placement="bottom">
          <button class="action-btn" @click="handleSearchClick">
            <NavIcon name="search" />
          </button>
        </el-tooltip>
        -->

        <!-- 通知按钮（暂时隐藏） -->
        <el-tooltip
          v-if="showNotification"
          :content="t('navigation.tooltip.notification')"
          placement="bottom"
        >
          <button class="action-btn notification-btn" @click="handleNotificationClick">
            <NavIcon name="notification" />
            <span v-if="notificationCount" class="notification-badge">
              {{ notificationCount > 99 ? '99+' : notificationCount }}
            </span>
          </button>
        </el-tooltip>

        <!-- 语言切换按钮 -->
        <el-dropdown trigger="click" @command="handleLanguageChange">
          <span class="dropdown-trigger">
            <el-tooltip :content="t('navigation.tooltip.language')" placement="bottom">
              <button class="action-btn language-btn">
                <NavIcon name="language" />
                <span class="language-label">{{ currentLanguageLabel }}</span>
              </button>
            </el-tooltip>
          </span>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item
                v-for="option in languageOptions"
                :key="option.code"
                :command="option.code"
                :class="{ active: option.code === currentLanguage }"
              >
                {{ option.label }}
              </el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>

        <!-- 主题切换按钮（暂时禁用） -->
        <el-tooltip
          v-if="showThemeToggle"
          :content="t('navigation.tooltip.theme')"
          placement="bottom"
        >
          <button class="action-btn" @click="handleThemeClick">
            <NavIcon name="dark-mode" />
          </button>
        </el-tooltip>
      </div>
    </div>
  </header>

  <el-dialog v-model="changePwdVisible" title="修改密码" width="420px">
    <el-form ref="changePwdFormRef" :model="changePwdForm" :rules="changePwdRules" label-width="100px">
      <el-form-item label="原密码" prop="old_password">
        <el-input v-model="changePwdForm.old_password" type="password" show-password autocomplete="current-password" />
      </el-form-item>
      <el-form-item label="新密码" prop="new_password">
        <el-input v-model="changePwdForm.new_password" type="password" show-password autocomplete="new-password" />
      </el-form-item>
      <el-form-item label="确认新密码" prop="confirm_password">
        <el-input v-model="changePwdForm.confirm_password" type="password" show-password autocomplete="new-password" />
      </el-form-item>
    </el-form>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="changePwdVisible = false">取消</el-button>
        <el-button type="primary" @click="onSubmitChangePwd">保存</el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { computed, reactive, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { useRouter } from 'vue-router'
import { ElMessageBox, ElMessage, FormInstance, FormRules } from 'element-plus'
import { useAppStore } from '@/store/modules/app'
import { useUserStore } from '@/store/modules/user'
import NavIcon from '@/components/common/icons/NavIcon.vue'
import { useBreadcrumb } from '@/utils/breadcrumb'
import { changeLanguage, type SupportedLocale } from '@/utils/language'
import { ChangePassword, type ChangePasswordRequest } from '@/api/user'

// 组件属性接口定义
interface Props {
  notificationCount?: number // 未读通知数量
}

// 定义组件属性和默认值
const { notificationCount = 24 } = defineProps<Props>()

// 功能暂未启用，隐藏通知入口
const showNotification = false

// 主题功能暂未开发完成，禁用主题切换
const showThemeToggle = false

type ExternalLinkKey = 'docs' | 'github'
interface ExternalLink {
  key: ExternalLinkKey
  url: string
}

const externalLinks: ExternalLink[] = [
  { key: 'docs', url: 'https://docs.lm.cedar-v.com/' },
  { key: 'github', url: 'https://github.com/cedar-v/license-manager' }
]

// 定义组件事件
const emit = defineEmits<{
  sidebarToggle: []
  searchClick: []
  notificationClick: []
  languageClick: []
  themeClick: []
  userClick: []
}>()

// 使用国际化
const { t, locale } = useI18n()

// 使用store和组合函数
const router = useRouter()
const appStore = useAppStore()
const userStore = useUserStore()
const { breadcrumbs, navigateTo } = useBreadcrumb()

// 获取用户信息
const userInfo = computed(() => userStore.userInfo)

// 获取用户名首字母（用于头像显示）
const userInitial = computed(() => {
  const username = userInfo.value?.username
  if (!username) return '?'
  // 如果是中文名，取第一个字
  if (/[\u4e00-\u9fa5]/.test(username)) {
    return username.charAt(0)
  }
  // 如果是英文名，取首字母
  return username.charAt(0).toUpperCase()
})

// 计算导航栏样式类
const navClasses = computed(() => ({
  'nav-content--sidebar-collapsed': appStore.sidebarCollapsed,
  'nav-content--mobile': appStore.isMobile
}))

// 处理侧边栏切换
const handleSidebarToggle = () => {
  appStore.toggleSidebar()
  emit('sidebarToggle')
}

// 处理通知按钮点击
const handleNotificationClick = () => {
  emit('notificationClick')
}

const handleExternalLink = (url: string) => {
  window.open(url, '_blank', 'noopener,noreferrer')
}

// 语言切换
const languageOptions: Array<{ code: SupportedLocale; label: string }> = [
  { code: 'en', label: 'English' },
  { code: 'zh', label: '中文' },
  { code: 'ja', label: '日本語' }
]
const currentLanguage = ref(locale.value as SupportedLocale)
const currentLanguageLabel = computed(
  () => languageOptions.find(option => option.code === currentLanguage.value)?.label || ''
)
const handleLanguageChange = (lang: SupportedLocale) => {
  if (lang === currentLanguage.value) return
  changeLanguage(lang)
  currentLanguage.value = lang
  emit('languageClick')
  window.location.reload()
}
watch(
  locale,
  value => {
    currentLanguage.value = value as SupportedLocale
  },
  { immediate: true }
)

// 处理主题切换点击
const handleThemeClick = () => {
  const currentTheme = appStore.theme
  const nextTheme = currentTheme === 'light' ? 'dark' : 'light'
  appStore.setTheme(nextTheme)
  emit('themeClick')
}

// 处理用户下拉菜单命令
const handleUserCommand = async (command: string) => {
  switch (command) {
    case 'changePassword':
      changePwdVisible.value = true
      break
    case 'profile':
      // 跳转到个人信息页面
      ElMessage.info(t('userMenu.profileComingSoon'))
      break
    case 'logout':
      // 退出登录
      try {
        await ElMessageBox.confirm(
          t('userMenu.logoutConfirm'),
          t('userMenu.logoutTitle'),
          {
            confirmButtonText: t('userMenu.confirm'),
            cancelButtonText: t('userMenu.cancel'),
            type: 'warning',
            // center: true,
          }
        )
        // 清除用户信息
        userStore.logout()
        // 跳转到登录页
        await router.push('/login')
        ElMessage.success(t('userMenu.logoutSuccess'))
      } catch (error) {
        // 用户取消退出
        if (error !== 'cancel') {
          console.error('退出登录失败:', error)
        }
      }
      break
  }
}

// 修改密码对话框
const changePwdVisible = ref(false)
const changePwdFormRef = ref<FormInstance>()
const changePwdForm = reactive({
  old_password: '',
  new_password: '',
  confirm_password: ''
})
const changePwdRules: FormRules = {
  old_password: [{ required: true, message: '请输入原密码', trigger: 'blur' }],
  new_password: [{ required: true, message: '请输入新密码', trigger: 'blur' }, { min: 8, message: '至少8位', trigger: 'blur' }],
  confirm_password: [
    { required: true, message: '请再次输入新密码', trigger: 'blur' },
    {
      validator: (_r, v, cb) => {
        if (v !== changePwdForm.new_password) cb(new Error('两次输入不一致'))
        else cb()
      }, trigger: 'blur'
    }
  ]
}

const onSubmitChangePwd = async () => {
  await changePwdFormRef.value?.validate()
  const payload: ChangePasswordRequest = {
    old_password: changePwdForm.old_password,
    new_password: changePwdForm.new_password
  }
  try {
    await ChangePassword(payload)
    ElMessage.success('修改成功')
    changePwdVisible.value = false
    changePwdForm.old_password = ''
    changePwdForm.new_password = ''
    changePwdForm.confirm_password = ''
  } catch (e: any) {
    ElMessage.error(e?.backendMessage || '修改失败')
  }
}
</script>

<style scoped>
/* 顶部导航栏 - 基于1920*1080设计的vw适配，确保4K下正确显示 */
.nav-content {
  position: fixed;
  top: 0;
  left: 280px; /* 默认：侧边栏展开状态 */
  right: 0;
  height: 80px; /* 使用固定高度 */
  background: var(--app-nav-bg);
  border-bottom: 1px solid var(--app-border-light);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 24px; /* 使用固定像素值 */
  z-index: 2001;
  transition: left 0.3s ease, background-color 0.3s ease, border-color 0.3s ease; /* 只对left属性做动画 */
}

/* 左侧区域 */
.nav-content__left {
  display: flex;
  align-items: center;
  gap: 12px;
}

/* 侧边栏切换按钮 */
.sidebar-toggle-btn {
  width: 32px;
  height: 32px;
  border: none;
  background: transparent;
  border-radius: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px;
  transition: all 0.2s;
}

.sidebar-toggle-btn:hover {
  background: var(--el-color-primary-light-9);
}

/* 面包屑区域 */
.breadcrumb-section {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

/* 面包屑导航 */
.breadcrumb {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  color: var(--app-text-secondary);
  flex-wrap: wrap;
}

.breadcrumb-item {
  display: flex;
  align-items: center;
  gap: 4px;
  white-space: nowrap;
}

/* 面包屑当前页面样式，使用页面标题样式 */
.breadcrumb-current.page-title {
  font-family: 'OPPOSans', sans-serif;
  font-size: 20px;
  font-weight: 400;
  color: var(--app-text-primary);
  line-height: 1.32;
}

.breadcrumb-link {
  border: none;
  background: none;
  color: var(--el-color-primary);
  font-size: 12px;
  cursor: pointer;
  padding: 0;
  text-decoration: none;
  transition: all 0.2s;
}

.breadcrumb-link:hover {
  color: var(--el-color-primary-dark-2);
  text-decoration: underline;
}

.breadcrumb-current {
  color: var(--app-text-regular);
  font-size: 12px;
}

.breadcrumb-separator {
  color: var(--app-border-color);
  margin: 0 4px;
  font-size: 12px;
}

.page-title {
  font-family: 'OPPOSans', sans-serif;
  font-size: 20px;
  font-weight: 400;
  color: var(--app-text-primary);
  line-height: 1.32;
}

/* 右侧区域 */
.nav-content__right {
  display: flex;
  align-items: center;
  gap: 24px;
}

/* 用户头像 */
.user-avatar {
  width: 20px;
  height: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.user-avatar:hover {
  opacity: 0.7;
}

/* 用户下拉菜单样式 */
:deep(.el-dropdown-menu) {
  padding: 0 !important;
  border-radius: 8px !important;
  box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.24) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
  margin-top: 8px !important;
  right: 0 !important; /* 右对齐到用户头像 */
  left: auto !important;
}

/* 用户信息头部 */
.user-info-header {
  display: flex;
  width: 200px !important;
  align-items: center;
  gap: 12px;
  padding: 5px 16px;
  background: transparent;
}

.user-avatar-large {
  width: 28px;
  height: 28px;
  flex-shrink: 0;
}

.avatar-icon {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: var(--el-color-primary);
  display: flex;
  align-items: center;
  justify-content: center;
}

.avatar-text {
  font-family: 'PingFang SC', sans-serif;
  font-size: 14px;
  font-weight: 400;
  color: #FFFFFF;
  line-height: 1.29;
  letter-spacing: 0.03em;
}

.user-details {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.user-name {
  font-family: 'Source Han Sans CN', sans-serif;
  font-size: 14px;
  font-weight: 400;
  color: #000000;
  line-height: 1.29;
  letter-spacing: 0.03em;
}

.user-role {
  font-family: 'Source Han Sans CN', sans-serif;
  font-size: 12px;
  font-weight: 400;
  color: #000000;
  line-height: 1.5;
  letter-spacing: 0.03em;
}

/* 分割线项 */
:deep(.dropdown-divider) {
  height: 0;
  padding: 0;
  margin: 8px 16px;
  cursor: default;
  pointer-events: none;
}

/* 菜单项 */
:deep(.dropdown-menu-item) {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 8.5px 24px;
  height: 33px;
  font-family: 'Source Han Sans CN', sans-serif;
  font-size: 14px;
  font-weight: 400;
  color: #202332;
  letter-spacing: 0.0714em;
  transition: background-color 0.2s;
}

:deep(.dropdown-menu-item:hover) {
  background: #F5F5F5;
}

.menu-icon {
  width: 16px;
  height: 16px;
  flex-shrink: 0;
  color: #202332;
}

.menu-text {
  flex: 1;
}

/* 退出菜单项特殊样式 */
:deep(.logout-item:hover) {
  background: rgba(31, 109, 216, 0.12);
}

:deep(.logout-item:last-child) {
  margin-bottom: 4px;
}

/* 分割线 */
.divider {
  width: 1px;
  height: 24px;
  background: var(--app-border-light);
  border-radius: 2px;
}

/* 操作按钮组 */
.action-buttons {
  display: flex;
  gap: 12px;
}

/* 操作按钮 */
.action-btn {
  position: relative;
  width: 40px;
  height: 40px;
  border: none;
  background: var(--app-action-btn-bg);
  border-radius: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.external-link-btn {
  min-width: 72px;
  padding: 0 16px;
  width: auto;
  gap: 6px;
}

.external-link-label {
  font-size: 13px;
  font-weight: 600;
  color: var(--app-text-primary);
  white-space: nowrap;
}

.action-btn:hover {
  background: var(--el-color-primary-light-9);
}

.dropdown-trigger {
  display: inline-flex;
}

.language-btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  width: auto;
  min-width: 40px;
  padding: 0 12px;
}

.language-label {
  font-size: 12px;
  font-weight: 600;
  color: var(--app-text-primary);
}

/* 通知徽章 */
.notification-badge {
  position: absolute;
  top: -3px;
  right: -1px;
  width: 22px;
  height: 16px;
  background: var(--el-color-primary);
  color: white;
  font-size: 10px;
  font-weight: 700;
  border-radius: 90px;
  border: 1px solid #FFFFFF;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Roboto', sans-serif;
  line-height: 1.3;
}

:deep(.el-dropdown-menu__item.active) {
  color: var(--el-color-primary) !important;
  font-weight: 600;
}

/* 侧边栏状态配合 - 桌面端 */
.nav-content--sidebar-collapsed {
  left: 64px !important; /* 侧边栏收起时的宽度，使用!important确保优先级 */
}

.nav-content--mobile {
  left: 0 !important; /* 移动端从左侧边缘开始 */
}

/* 响应式设计 - 平板和移动端 */
@media (max-width: 1024px) {
  .nav-content {
    left: 0;
    padding: 16px 20px;
    height: 80px;
  }
  
  /* 覆盖侧边栏收起状态，平板端和移动端始终从左侧边缘开始 */
  .nav-content--sidebar-collapsed {
    left: 0;
  }
  
  /* 移动端使用固定像素值 */
  .nav-content__left {
    gap: 12px;
  }
  
  .sidebar-toggle-btn {
    width: 32px;
    height: 32px;
    padding: 4px;
    border-radius: 8px;
  }
  
  .breadcrumb {
    font-size: 12px;
    gap: 4px;
  }
  
  .breadcrumb-current.page-title,
  .page-title {
    font-size: 20px;
  }
  
  .nav-content__right {
    gap: 24px;
  }
  
  .user-avatar {
    width: 20px;
    height: 20px;
  }
  
  .divider {
    height: 24px;
  }
  
  .action-buttons {
    gap: 12px;
  }
  
  .action-btn {
    width: 40px;
    height: 40px;
    border-radius: 20px;
  }
  
  .notification-badge {
    width: 22px;
    height: 16px;
    font-size: 10px;
    top: -3px;
    right: -1px;
  }
}

@media (max-width: 768px) {
  .nav-content {
    padding: 12px 16px;
  }
  
  .nav-content__right {
    gap: 16px;
  }
  
  .action-buttons {
    gap: 8px;
  }
  
  .action-btn {
    width: 36px;
    height: 36px;
    border-radius: 18px;
  }
  
  .page-title {
    font-size: 18px;
  }
}

@media (max-width: 480px) {
  .nav-content {
    padding: 10px 12px;
  }
  
  .page-title {
    font-size: 16px;
  }
  
  .action-buttons {
    gap: 6px;
  }
  
  .action-btn {
    width: 32px;
    height: 32px;
    border-radius: 16px;
  }
}

/* 桌面端确保导航栏正确响应侧边栏状态 */
@media (min-width: 1025px) {
  .nav-content {
    left: 280px; /* 桌面端默认侧边栏展开状态 */
    
  }
  
  .nav-content--sidebar-collapsed {
    left: 64px !important; /* 侧边栏收起状态，使用!important确保生效 */
  }
}
</style>

<style lang="scss">
/* 顶部导航栏暗模式样式 - 完美还原设计图 */

/* 导航栏背景和边框 */
[data-theme="dark"] .nav-content {
  background: rgba(31, 41, 53, 1) !important;
  border-bottom-color: rgba(255, 255, 255, 0.12) !important;
}

/* 侧边栏切换按钮暗模式 */
[data-theme="dark"] .sidebar-toggle-btn:hover {
  background: rgba(31, 109, 216, 0.15) !important;
}

/* 面包屑导航暗模式 */
[data-theme="dark"] .breadcrumb {
  color: #9ca3af !important;
}

[data-theme="dark"] .breadcrumb-current.page-title,
[data-theme="dark"] .page-title {
  color: #f9fafb !important;
}

[data-theme="dark"] .breadcrumb-link {
  color: var(--el-color-primary) !important;
}

[data-theme="dark"] .breadcrumb-link:hover {
  color: var(--el-color-primary-dark-2) !important;
}

[data-theme="dark"] .breadcrumb-current {
  color: #e5e7eb !important;
}

[data-theme="dark"] .breadcrumb-separator {
  color: rgba(255, 255, 255, 0.3) !important;
}

/* 分割线暗模式 */
[data-theme="dark"] .divider {
  background: rgba(255, 255, 255, 0.12) !important;
}

/* 操作按钮暗模式 */
[data-theme="dark"] .action-btn {
  background: rgba(255, 255, 255, 0.08) !important;
}

[data-theme="dark"] .external-link-label {
  color: #f9fafb !important;
}

[data-theme="dark"] .action-btn:hover {
  background: rgba(31, 109, 216, 0.15) !important;
}

/* 通知徽章暗模式 */
[data-theme="dark"] .notification-badge {
  background: var(--el-color-primary) !important;
  color: #ffffff !important;
  border-color: rgba(31, 41, 53, 1) !important;
}

/* 导航栏图标暗模式 */
[data-theme="dark"] .sidebar-toggle-btn,
[data-theme="dark"] .user-avatar,
[data-theme="dark"] .action-btn {
  color: #f9fafb !important;
}

[data-theme="dark"] .sidebar-toggle-btn :deep(svg),
[data-theme="dark"] .user-avatar :deep(svg),
[data-theme="dark"] .action-btn :deep(svg) {
  color: #f9fafb !important;
}

[data-theme="dark"] .sidebar-toggle-btn :deep(.nav-icon),
[data-theme="dark"] .user-avatar :deep(.nav-icon),
[data-theme="dark"] .action-btn :deep(.nav-icon) {
  color: #f9fafb !important;
}

/* 用户下拉菜单暗模式样式 */
[data-theme="dark"] :deep(.user-dropdown-menu) {
  background: rgba(31, 41, 53, 1) !important;
  border-color: rgba(255, 255, 255, 0.12) !important;
  box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.4) !important;
}

[data-theme="dark"] .user-info-header {
  background: transparent !important;
}

[data-theme="dark"] .avatar-icon {
  background: var(--el-color-primary) !important;
}

[data-theme="dark"] .user-name {
  color: #f9fafb !important;
}

[data-theme="dark"] .user-role {
  color: #9ca3af !important;
}

[data-theme="dark"] :deep(.dropdown-divider) {
  border-top-color: rgba(255, 255, 255, 0.12) !important;
}

[data-theme="dark"] :deep(.dropdown-menu-item) {
  color: #f9fafb !important;
}

[data-theme="dark"] :deep(.dropdown-menu-item:hover) {
  background: rgba(255, 255, 255, 0.08) !important;
}

[data-theme="dark"] .menu-icon {
  color: #f9fafb !important;
}

[data-theme="dark"] :deep(.logout-item:hover) {
  background: rgba(31, 109, 216, 0.15) !important;
}
</style>
