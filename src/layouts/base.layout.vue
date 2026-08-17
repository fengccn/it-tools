<script lang="ts" setup>
import { NIcon, useThemeVars } from 'naive-ui';

import { RouterLink } from 'vue-router';
import { Heart, Home2, Menu2 } from '@vicons/tabler';

import { storeToRefs } from 'pinia';
import HeroGradient from '../assets/hero-gradient.svg?component';
import MenuLayout from '../components/MenuLayout.vue';
import { useStyleStore } from '@/stores/style.store';
import { config } from '@/config';
import type { ToolCategory } from '@/tools/tools.types';
import { useToolStore } from '@/tools/tools.store';
import { useTracker } from '@/modules/tracker/tracker.services';
import CollapsibleToolMenu from '@/components/CollapsibleToolMenu.vue';
import AdSidebar from '@/components/AdSidebar.vue';

const themeVars = useThemeVars();
const styleStore = useStyleStore();
const version = config.app.version;
const commitSha = config.app.lastCommitSha.slice(0, 7);

const { tracker } = useTracker();
const { t } = useI18n();

const toolStore = useToolStore();
const { favoriteTools, toolsByCategory } = storeToRefs(toolStore);

const tools = computed<ToolCategory[]>(() => [
  ...(favoriteTools.value.length > 0 ? [{ name: t('tools.categories.favorite-tools'), components: favoriteTools.value }] : []),
  ...toolsByCategory.value,
]);

// 动态计算 padding-top
const siderPaddingTop = computed(() => {
  if (styleStore.isMenuCollapsed || styleStore.isSmallScreen) {
    return '160px';
  }
  return '240px';
});
</script>

<template>
  <MenuLayout class="menu-layout" :class="{ isSmallScreen: styleStore.isSmallScreen }">
    <!-- 侧边栏内容 -->
    <template #sider>
      <div class="hero-wrapper">
        <HeroGradient class="gradient" />
        <div class="text-wrapper">
          <div class="title">
            <!-- Logo：点击打开博客 -->
            <a href="https://fengcblog.880200.xyz" target="_blank">
              <img src="https://fengc-img.880200.xyz/api/rfile/logo.png" width="70" alt="logo" />
            </a>
            <br />
            <!-- 标题文字：点击回到工具首页 -->
            <RouterLink to="/" style="color: #fff; text-decoration: none;">
              IT - TOOLS
            </RouterLink>
          </div>
          <div class="divider" />
          <div class="subtitle">
            {{ $t('home.subtitle') }}
          </div>
        </div>
      </div>

      <div class="sider-content" :style="{ paddingTop: siderPaddingTop }">
        <!-- 小屏幕时显示语言选择和暗黑切换 -->
        <div v-if="styleStore.isSmallScreen" flex flex-col items-center>
          <locale-selector w="90%" />
          
          <div flex justify-center mt-2>
            <c-button
              circle
              variant="text"
              :aria-label="styleStore.isDarkTheme ? $t('home.lightMode') : $t('home.darkMode')"
              @click="styleStore.isDarkTheme = !styleStore.isDarkTheme"
            >
              <NIcon size="22">
                <span v-if="styleStore.isDarkTheme">☀️</span>
                <span v-else>🌙</span>
              </NIcon>
            </c-button>
          </div>
        </div>

        <!-- 工具菜单 -->
        <CollapsibleToolMenu :tools-by-category="tools" />

        <!-- 页脚和广告 -->
        <div class="footer">
          <div>
            IT-Tools 2026 Provide by 
            <a href="https://fengcblog.880200.xyz" target="_blank" class="footer-link"> fengc's Blog </a>
          </div>
          <br><br><br>
          <div class="ad-container">
            <AdSidebar />
          </div>
        </div>
      </div>
    </template>

    <!-- 主内容区域 -->
    <template #content>
      <div flex items-center justify-center gap-2>
        <!-- 菜单切换按钮 -->
        <c-button
          circle
          variant="text"
          :aria-label="$t('home.toggleMenu')"
          @click="styleStore.isMenuCollapsed = !styleStore.isMenuCollapsed"
        >
          <NIcon size="25" :component="Menu2" />
        </c-button>

        <!-- 首页按钮 -->
        <c-tooltip :tooltip="$t('home.home')" position="bottom">
          <c-button to="/" circle variant="text" :aria-label="$t('home.home')">
            <NIcon size="25" :component="Home2" />
          </c-button>
        </c-tooltip>

        <!-- 开发工具按钮（保留） -->
        <c-tooltip :tooltip="$t('home.uiLib')" position="bottom">
          <c-button v-if="config.app.env === 'development'" to="/c-lib" circle variant="text" :aria-label="$t('home.uiLib')">
            <icon-mdi:brush-variant text-20px />
          </c-button>
        </c-tooltip>

        <!-- 搜索框/命令面板（保留） -->
        <command-palette />

        <!-- 语言选择器（保留） -->
        <locale-selector v-if="!styleStore.isSmallScreen" />

        <!-- 暗黑模式切换 -->
        <c-tooltip 
          :tooltip="styleStore.isDarkTheme ? $t('home.lightMode') : $t('home.darkMode')" 
          position="bottom"
        >
          <c-button
            circle
            variant="text"
            :aria-label="styleStore.isDarkTheme ? $t('home.lightMode') : $t('home.darkMode')"
            @click="styleStore.isDarkTheme = !styleStore.isDarkTheme"
          >
            <NIcon size="22">
              <span v-if="styleStore.isDarkTheme">☀️</span>
              <span v-else>🌙</span>
            </NIcon>
          </c-button>
        </c-tooltip>
      </div>
      <slot />
    </template>
  </MenuLayout>
</template>

<style lang="less" scoped>
.support-button {
  background: rgb(37, 99, 108);
  background: linear-gradient(48deg, rgba(37, 99, 108, 1) 0%, rgba(59, 149, 111, 1) 60%, rgba(20, 160, 88, 1) 100%);
  color: #fff !important;
  transition: padding ease 0.2s !important;

  &:hover {
    color: #fff;
    padding-left: 30px;
    padding-right: 30px;
  }
}

.footer {
  text-align: center;
  color: #838587;
  margin-top: 20px;
  padding: 20px 0;
}

.footer-link {
  color: #838587;
  text-decoration: none;
  transition: color 0.2s;
}

.footer-link:hover {
  color: v-bind('styleStore.isDarkTheme ? "#ffffff" : "#000000"');
}

.ad-container {
  margin-top: 12px;
  padding: 0;
  line-height: 0;
  border: none;
  border-radius: 0;
}

.sider-content {
  padding-bottom: 200px;
}

.hero-wrapper {
  position: absolute;
  display: block;
  left: 0;
  width: 100%;
  z-index: 10;
  overflow: hidden;

  .gradient {
    margin-top: -65px;
  }

  .text-wrapper {
    position: absolute;
    left: 0;
    width: 100%;
    text-align: center;
    top: 16px;
    color: #fff;

    .title {
      font-size: 25px;
      font-weight: 600;
    }

    .divider {
      width: 50px;
      height: 2px;
      border-radius: 4px;
      background-color: v-bind('themeVars.primaryColor');
      margin: 0 auto 5px;
    }

    .subtitle {
      font-size: 16px;
    }
  }
}
</style>
