<script setup lang="ts">
import { RouterView, useRoute } from 'vue-router';
import { NGlobalStyle, NMessageProvider, NNotificationProvider, darkTheme } from 'naive-ui';
import { darkThemeOverrides, lightThemeOverrides } from './themes';
import { layouts } from './layouts';
import { useStyleStore } from './stores/style.store';
import AdSidebar from './components/AdSidebar.vue'; // 导入广告组件

const route = useRoute();
const layout = computed(() => route?.meta?.layout ?? layouts.base);
const styleStore = useStyleStore();

const theme = computed(() => (styleStore.isDarkTheme ? darkTheme : null));
const themeOverrides = computed(() => (styleStore.isDarkTheme ? darkThemeOverrides : lightThemeOverrides));

const { locale } = useI18n();

syncRef(
  locale,
  useStorage('locale', locale),
);
</script>

<template>
  <n-config-provider :theme="theme" :theme-overrides="themeOverrides">
    <NGlobalStyle />
    <NMessageProvider placement="bottom">
      <NNotificationProvider placement="bottom-right">
        <!-- 使用 layout 组件渲染主内容 -->
        <component :is="layout">
          <RouterView />
        </component>
        
        <!-- 在全局添加广告组件（固定定位在左下角） -->
        <div class="global-ad-container">
          <AdSidebar />
        </div>
      </NNotificationProvider>
    </NMessageProvider>
  </n-config-provider>
</template>

<style>
body {
  min-height: 100%;
  margin: 0;
  padding: 0;
}

html {
  height: 100%;
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
}

/* 全局广告容器样式 - 固定定位在左下角 */
.global-ad-container {
  position: fixed;
  bottom: 20px;
  left: 20px;
  z-index: 1000;
  /* 确保广告在内容之上 */
  background: var(--bg-card, #ffffff);
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  padding: 8px;
  border: 1px solid var(--border-color, #e0e0e0);
}

/* 在移动设备上隐藏或调整位置 */
@media (max-width: 768px) {
  .global-ad-container {
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
    width: 300px;
    /* 固定宽度 */
  }
}

@media (max-width: 480px) {
  .global-ad-container {
    width: 280px;
    /* 更小屏幕适配 */
  }
}
</style>
