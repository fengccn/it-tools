<template>
  <div ref="adContainer" class="ad-sidebar-wrapper">
    <iframe
      ref="adIframe"
      allow="popups"
      class="ad-iframe"
    ></iframe>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const adContainer = ref(null);
const adIframe = ref(null);

const adDomain = 'conventionalresponse.com';
let isAdClick = false;

// 1. 替换为你给出的最新广告脚本字符串
const adScriptCode = `
(function(zqoqry){
var d = document,
    s = d.createElement('script'),
    l = d.scripts[d.scripts.length - 1];
s.settings = zqoqry || {};
s.src = "//conventionalresponse.com/b/XOVQsdd.G_lK0LYjWece/Zeymk9Xu/ZvU/ltkOP/TJc/z/MnjQUDx_NXjmUxtoNjznMsyRNpTtE/2/O/Q-";
s.async = true;
s.referrerPolicy = 'no-referrer-when-downgrade';
l.parentNode.insertBefore(s, l);
})({})
`;

onMounted(() => {
  // ---- 1. 将广告脚本注入隔离的 iframe 中（禁止飘出与污染父页面） ----
  if (adIframe.value) {
    const iframeDoc = adIframe.value.contentDocument || adIframe.value.contentWindow.document;
    iframeDoc.open();
    iframeDoc.write(`
      <!DOCTYPE html>
      <html>
        <head>
          <meta charset="UTF-8">
          <style>
            html, body { margin: 0; padding: 0; width: 300px; height: 250px; overflow: hidden; background: transparent; }
            * { margin: 0; padding: 0; box-sizing: border-box; }
          </style>
        </head>
        <body>
          <div id="ad-placeholder"></div>
          <script>${adScriptCode}<\/script>
        </body>
      </html>
    `);
    iframeDoc.close();
  }

  // ---- 2. 监听用户是否对广告区域进行了主动点击 ----
  if (adContainer.value) {
    const markClick = () => {
      isAdClick = true;
      setTimeout(() => { isAdClick = false; }, 800);
    };
    adContainer.value.addEventListener('click', markClick, true);
    adContainer.value.addEventListener('mousedown', markClick, true);
  }

  // ---- 3. 拦截未被授权的弹窗行为 (window.open) ----
  const origOpen = window.open;
  window.open = function (...args) {
    try {
      const stack = new Error().stack || '';
      if (isAdClick) {
        return origOpen.apply(this, args);
      }
      if (stack.indexOf(adDomain) > -1 || stack.indexOf('conventionalresponse') > -1) {
        console.log('[广告防护] 已阻止拦截到的非主动弹窗');
        return null;
      }
    } catch (e) {}
    return origOpen.apply(this, args);
  };
});
</script>

<style scoped>
.ad-sidebar-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px 0;
  margin-top: auto; /* 在 flex 布局下将广告压到底部 */
  overflow: hidden;
}

.ad-iframe {
  width: 300px;
  height: 250px;
  border: none;
  display: block;
  background: transparent;
}
</style>
