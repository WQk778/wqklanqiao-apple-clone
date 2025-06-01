<template>
  <div class="wqk-theme-page" :class="wqkGetThemeClasses">
    <!-- 页面头部 -->
    <header class="wqk-page-header">
      <div class="wqk-container">
        <h1 class="wqk-page-title">深色主题切换演示</h1>
        <p class="wqk-page-subtitle">基于 Pinia 全局状态管理的主题切换功能</p>
        
        <!-- 主题切换器 -->
        <div class="wqk-theme-controls">
          <WangThemeToggle 
            :wqk-show-indicator="true"
            :wqk-show-transition="true"
            wqk-size="large"
            @wqk-theme-changed="wqkHandleThemeChange"
            @wqk-toggle-start="wqkHandleToggleStart"
            @wqk-toggle-end="wqkHandleToggleEnd"
          />
        </div>
      </div>
    </header>

    <!-- 主要内容区域 -->
    <main class="wqk-main-content">
      <div class="wqk-container">
        
        <!-- 主题状态信息 -->
        <section class="wqk-theme-info">
          <h2>当前主题状态</h2>
          <div class="wqk-info-grid">
            <div class="wqk-info-card">
              <h3>主题模式</h3>
              <p class="wqk-theme-value">{{ wqkCurrentTheme }}</p>
              <span class="wqk-theme-badge" :class="wqkIsDarkMode ? 'dark' : 'light'">
                {{ wqkIsDarkMode ? '深色模式' : '浅色模式' }}
              </span>
            </div>
            
            <div class="wqk-info-card">
              <h3>系统主题</h3>
              <p class="wqk-theme-value">{{ wqkSystemTheme }}</p>
              <button @click="wqkFollowSystemTheme" class="wqk-btn wqk-btn-secondary">
                跟随系统
              </button>
            </div>
            
            <div class="wqk-info-card">
              <h3>切换次数</h3>
              <p class="wqk-theme-value">{{ wqkToggleCount }}</p>
              <button @click="wqkResetCount" class="wqk-btn wqk-btn-outline">
                重置计数
              </button>
            </div>
          </div>
        </section>

        <!-- 主题切换方式演示 -->
        <section class="wqk-toggle-methods">
          <h2>主题切换方式</h2>
          <div class="wqk-methods-grid">
            
            <!-- 基础切换器 -->
            <div class="wqk-method-card">
              <h3>基础切换器</h3>
              <WangThemeToggle wqk-variant="default" />
            </div>
            
            <!-- 最小化切换器 -->
            <div class="wqk-method-card">
              <h3>最小化切换器</h3>
              <WangThemeToggle wqk-variant="minimal" wqk-size="small" />
            </div>
            
            <!-- 手动切换按钮 -->
            <div class="wqk-method-card">
              <h3>手动切换</h3>
              <div class="wqk-manual-controls">
                <button @click="wqkSetLightMode" class="wqk-btn wqk-btn-light">
                  🌞 浅色模式
                </button>
                <button @click="wqkSetDarkMode" class="wqk-btn wqk-btn-dark">
                  🌙 深色模式
                </button>
              </div>
            </div>
            
            <!-- 快捷键切换 -->
            <div class="wqk-method-card">
              <h3>快捷键切换</h3>
              <p class="wqk-shortcut-info">
                按 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>T</kbd> 切换主题
              </p>
              <span class="wqk-status" :class="{ active: wqkShortcutEnabled }">
                {{ wqkShortcutEnabled ? '已启用' : '已禁用' }}
              </span>
            </div>
          </div>
        </section>

        <!-- 主题效果展示 -->
        <section class="wqk-theme-showcase">
          <h2>主题效果展示</h2>
          
          <!-- 卡片展示 -->
          <div class="wqk-showcase-grid">
            <div class="wqk-showcase-card">
              <h3>产品卡片</h3>
              <div class="wqk-product-card">
                <div class="wqk-product-image"></div>
                <div class="wqk-product-info">
                  <h4>iPhone 16 Pro</h4>
                  <p>钛金属设计，A18 Pro 芯片</p>
                  <span class="wqk-price">¥7,999 起</span>
                </div>
              </div>
            </div>
            
            <div class="wqk-showcase-card">
              <h3>表单元素</h3>
              <div class="wqk-form-demo">
                <input type="text" placeholder="用户名" class="wqk-input" />
                <input type="password" placeholder="密码" class="wqk-input" />
                <button class="wqk-btn wqk-btn-primary">登录</button>
              </div>
            </div>
            
            <div class="wqk-showcase-card">
              <h3>通知消息</h3>
              <div class="wqk-notifications-demo">
                <div class="wqk-notification wqk-success">
                  ✅ 操作成功完成
                </div>
                <div class="wqk-notification wqk-warning">
                  ⚠️ 请注意检查信息
                </div>
                <div class="wqk-notification wqk-error">
                  ❌ 操作失败，请重试
                </div>
              </div>
            </div>
            
            <div class="wqk-showcase-card">
              <h3>数据统计</h3>
              <div class="wqk-stats-demo">
                <div class="wqk-stat-item">
                  <span class="wqk-stat-value">1,234</span>
                  <span class="wqk-stat-label">用户数量</span>
                </div>
                <div class="wqk-stat-item">
                  <span class="wqk-stat-value">89.5%</span>
                  <span class="wqk-stat-label">满意度</span>
                </div>
                <div class="wqk-stat-item">
                  <span class="wqk-stat-value">567</span>
                  <span class="wqk-stat-label">订单数</span>
                </div>
              </div>
            </div>
          </div>
        </section>

        <!-- 主题历史记录 -->
        <section class="wqk-theme-history">
          <h2>主题切换历史</h2>
          <div class="wqk-history-list">
            <div 
              v-for="(record, index) in wqkThemeHistory" 
              :key="index"
              class="wqk-history-item"
            >
              <span class="wqk-history-time">{{ wqkFormatTime(record.timestamp) }}</span>
              <span class="wqk-history-theme" :class="record.theme">
                {{ record.theme === 'dark' ? '深色模式' : '浅色模式' }}
              </span>
              <span class="wqk-history-method">{{ record.method }}</span>
            </div>
          </div>
          
          <button @click="wqkClearHistory" class="wqk-btn wqk-btn-outline wqk-clear-btn">
            清空历史
          </button>
        </section>
      </div>
    </main>

    <!-- 浮动主题切换器 -->
    <WangThemeToggle wqk-variant="floating" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { wqkUseTheme } from '@/composables/wqkUseTheme'
import WangThemeToggle from '@/components/ui/WangThemeToggle.vue'

// 使用主题管理
const {
  wqkCurrentTheme,
  wqkIsDarkMode,
  wqkIsLightMode,
  wqkGetThemeClasses,
  wqkToggleTheme,
  wqkSetDarkMode,
  wqkSetLightMode,
  wqkFollowSystemTheme,
  wqkDetectSystemTheme
} = wqkUseTheme()

// 页面状态
const wqkToggleCount = ref(0)
const wqkShortcutEnabled = ref(true)
const wqkThemeHistory = ref<Array<{
  timestamp: number
  theme: string
  method: string
}>>([])

// 系统主题检测
const wqkSystemTheme = computed(() => {
  return wqkDetectSystemTheme()
})

// 主题切换事件处理
const wqkHandleThemeChange = (theme: string) => {
  wqkToggleCount.value++
  wqkAddToHistory(theme, '切换器')
  console.log('主题已切换到:', theme)
}

const wqkHandleToggleStart = () => {
  console.log('主题切换开始')
}

const wqkHandleToggleEnd = () => {
  console.log('主题切换完成')
}

// 手动切换主题
const wqkSetLightModeManual = () => {
  wqkSetLightMode()
  wqkToggleCount.value++
  wqkAddToHistory('light', '手动切换')
}

const wqkSetDarkModeManual = () => {
  wqkSetDarkMode()
  wqkToggleCount.value++
  wqkAddToHistory('dark', '手动切换')
}

// 跟随系统主题
const wqkFollowSystemThemeManual = () => {
  wqkFollowSystemTheme()
  wqkToggleCount.value++
  wqkAddToHistory(wqkCurrentTheme.value, '跟随系统')
}

// 历史记录管理
const wqkAddToHistory = (theme: string, method: string) => {
  wqkThemeHistory.value.unshift({
    timestamp: Date.now(),
    theme,
    method
  })
  
  // 限制历史记录数量
  if (wqkThemeHistory.value.length > 10) {
    wqkThemeHistory.value = wqkThemeHistory.value.slice(0, 10)
  }
}

const wqkClearHistory = () => {
  wqkThemeHistory.value = []
}

const wqkFormatTime = (timestamp: number) => {
  return new Date(timestamp).toLocaleTimeString('zh-CN', {
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit'
  })
}

// 重置计数
const wqkResetCount = () => {
  wqkToggleCount.value = 0
}

// 快捷键处理
const wqkHandleKeydown = (event: KeyboardEvent) => {
  if (!wqkShortcutEnabled.value) return
  
  // Ctrl + Shift + T
  if (event.ctrlKey && event.shiftKey && event.key === 'T') {
    event.preventDefault()
    wqkToggleTheme()
    wqkToggleCount.value++
    wqkAddToHistory(wqkCurrentTheme.value, '快捷键')
  }
}

// 生命周期
onMounted(() => {
  // 添加快捷键监听
  document.addEventListener('keydown', wqkHandleKeydown)
  
  // 初始化历史记录
  wqkAddToHistory(wqkCurrentTheme.value, '页面加载')
})

onUnmounted(() => {
  // 移除快捷键监听
  document.removeEventListener('keydown', wqkHandleKeydown)
})
</script>

<style scoped>
.wqk-theme-page {
  min-height: 100vh;
  background: var(--wqk-bg-primary);
  color: var(--wqk-text-primary);
  transition: background-color 0.3s ease, color 0.3s ease;
}

.wqk-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.wqk-page-header {
  padding: 3rem 0;
  text-align: center;
  border-bottom: 1px solid var(--wqk-border-color);
  background: var(--wqk-bg-secondary);
}

.wqk-page-title {
  font-size: 2.5rem;
  font-weight: 700;
  margin: 0 0 0.5rem 0;
  background: linear-gradient(135deg, var(--wqk-accent-color), #ff6b6b);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.wqk-page-subtitle {
  font-size: 1.125rem;
  color: var(--wqk-text-secondary);
  margin: 0 0 2rem 0;
}

.wqk-theme-controls {
  display: flex;
  justify-content: center;
  gap: 1rem;
}

.wqk-main-content {
  padding: 3rem 0;
}

section {
  margin-bottom: 3rem;
}

section h2 {
  font-size: 1.75rem;
  font-weight: 600;
  margin: 0 0 1.5rem 0;
  color: var(--wqk-text-primary);
}

.wqk-info-grid,
.wqk-methods-grid,
.wqk-showcase-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}

.wqk-info-card,
.wqk-method-card,
.wqk-showcase-card {
  padding: 1.5rem;
  background: var(--wqk-bg-secondary);
  border: 1px solid var(--wqk-border-color);
  border-radius: 1rem;
  transition: all 0.3s ease;
}

.wqk-info-card:hover,
.wqk-method-card:hover,
.wqk-showcase-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px var(--wqk-shadow-medium);
}

.wqk-info-card h3,
.wqk-method-card h3,
.wqk-showcase-card h3 {
  font-size: 1.125rem;
  font-weight: 600;
  margin: 0 0 1rem 0;
  color: var(--wqk-text-primary);
}

.wqk-theme-value {
  font-size: 1.5rem;
  font-weight: 700;
  margin: 0.5rem 0;
  color: var(--wqk-accent-color);
  text-transform: capitalize;
}

.wqk-theme-badge {
  display: inline-block;
  padding: 0.25rem 0.75rem;
  border-radius: 1rem;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
}

.wqk-theme-badge.light {
  background: #fff3cd;
  color: #856404;
}

.wqk-theme-badge.dark {
  background: #d1ecf1;
  color: #0c5460;
}

.wqk-btn {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 0.5rem;
  cursor: pointer;
  font-size: 0.875rem;
  font-weight: 500;
  transition: all 0.2s ease;
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.wqk-btn-primary {
  background: var(--wqk-accent-color);
  color: white;
}

.wqk-btn-primary:hover {
  background: var(--wqk-accent-hover);
  transform: translateY(-1px);
}

.wqk-btn-secondary {
  background: var(--wqk-bg-tertiary);
  color: var(--wqk-text-primary);
  border: 1px solid var(--wqk-border-color);
}

.wqk-btn-secondary:hover {
  background: var(--wqk-border-color);
}

.wqk-btn-outline {
  background: transparent;
  color: var(--wqk-text-primary);
  border: 1px solid var(--wqk-border-color);
}

.wqk-btn-outline:hover {
  background: var(--wqk-bg-secondary);
}

.wqk-btn-light {
  background: #fff3cd;
  color: #856404;
}

.wqk-btn-dark {
  background: #d1ecf1;
  color: #0c5460;
}

.wqk-manual-controls {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.wqk-shortcut-info {
  margin: 0.5rem 0;
  color: var(--wqk-text-secondary);
  font-size: 0.875rem;
}

kbd {
  padding: 0.125rem 0.25rem;
  background: var(--wqk-bg-tertiary);
  border: 1px solid var(--wqk-border-color);
  border-radius: 0.25rem;
  font-size: 0.75rem;
  font-family: monospace;
}

.wqk-status {
  display: inline-block;
  padding: 0.25rem 0.5rem;
  border-radius: 0.5rem;
  font-size: 0.75rem;
  font-weight: 600;
  background: var(--wqk-error-color);
  color: white;
}

.wqk-status.active {
  background: var(--wqk-success-color);
}

.wqk-product-card {
  display: flex;
  gap: 1rem;
  padding: 1rem;
  background: var(--wqk-bg-primary);
  border: 1px solid var(--wqk-border-light);
  border-radius: 0.75rem;
}

.wqk-product-image {
  width: 60px;
  height: 60px;
  background: linear-gradient(135deg, var(--wqk-accent-color), #ff6b6b);
  border-radius: 0.5rem;
  flex-shrink: 0;
}

.wqk-product-info h4 {
  margin: 0 0 0.25rem 0;
  font-size: 1rem;
  font-weight: 600;
}

.wqk-product-info p {
  margin: 0 0 0.5rem 0;
  color: var(--wqk-text-secondary);
  font-size: 0.875rem;
}

.wqk-price {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--wqk-accent-color);
}

.wqk-form-demo {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.wqk-input {
  padding: 0.75rem;
  border: 1px solid var(--wqk-border-color);
  border-radius: 0.5rem;
  background: var(--wqk-bg-primary);
  color: var(--wqk-text-primary);
  font-size: 0.875rem;
}

.wqk-input:focus {
  outline: none;
  border-color: var(--wqk-accent-color);
  box-shadow: 0 0 0 3px rgba(0, 122, 255, 0.1);
}

.wqk-notifications-demo {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.wqk-notification {
  padding: 0.75rem;
  border-radius: 0.5rem;
  font-size: 0.875rem;
  font-weight: 500;
}

.wqk-notification.wqk-success {
  background: rgba(52, 199, 89, 0.1);
  color: var(--wqk-success-color);
  border: 1px solid var(--wqk-success-color);
}

.wqk-notification.wqk-warning {
  background: rgba(255, 149, 0, 0.1);
  color: var(--wqk-warning-color);
  border: 1px solid var(--wqk-warning-color);
}

.wqk-notification.wqk-error {
  background: rgba(255, 59, 48, 0.1);
  color: var(--wqk-error-color);
  border: 1px solid var(--wqk-error-color);
}

.wqk-stats-demo {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  gap: 1rem;
}

.wqk-stat-item {
  text-align: center;
  padding: 1rem;
  background: var(--wqk-bg-primary);
  border: 1px solid var(--wqk-border-light);
  border-radius: 0.75rem;
}

.wqk-stat-value {
  display: block;
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--wqk-accent-color);
  margin-bottom: 0.25rem;
}

.wqk-stat-label {
  font-size: 0.75rem;
  color: var(--wqk-text-secondary);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.wqk-history-list {
  max-height: 300px;
  overflow-y: auto;
  border: 1px solid var(--wqk-border-color);
  border-radius: 0.75rem;
  background: var(--wqk-bg-secondary);
}

.wqk-history-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem 1rem;
  border-bottom: 1px solid var(--wqk-border-light);
}

.wqk-history-item:last-child {
  border-bottom: none;
}

.wqk-history-time {
  font-size: 0.75rem;
  color: var(--wqk-text-secondary);
  font-family: monospace;
  min-width: 80px;
}

.wqk-history-theme {
  padding: 0.25rem 0.5rem;
  border-radius: 0.5rem;
  font-size: 0.75rem;
  font-weight: 600;
  min-width: 80px;
  text-align: center;
}

.wqk-history-theme.light {
  background: #fff3cd;
  color: #856404;
}

.wqk-history-theme.dark {
  background: #d1ecf1;
  color: #0c5460;
}

.wqk-history-method {
  font-size: 0.875rem;
  color: var(--wqk-text-secondary);
  flex: 1;
}

.wqk-clear-btn {
  margin-top: 1rem;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .wqk-page-title {
    font-size: 2rem;
  }
  
  .wqk-info-grid,
  .wqk-methods-grid,
  .wqk-showcase-grid {
    grid-template-columns: 1fr;
  }
  
  .wqk-manual-controls {
    flex-direction: column;
  }
  
  .wqk-product-card {
    flex-direction: column;
    text-align: center;
  }
  
  .wqk-stats-demo {
    grid-template-columns: 1fr;
  }
  
  .wqk-history-item {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.5rem;
  }
}
</style>