<!-- src/components/AdSidebar.vue -->
<template>
  <div id="ad-union-container" class="ad-union-wrapper">
    <iframe id="ad-iframe" allow="popups" style="width:300px;height:250px;border:none;display:block;margin:0;padding:0;"></iframe>
  </div>
</template>

<script setup lang="ts">
// ... (JavaScript 代码保持不变，和之前一样)
</script>

<style scoped>
.ad-union-wrapper {
  width: 300px;
  height: 250px;
  overflow: hidden;
  position: relative;
  background: transparent; /* 透明背景，不设边框和圆角 */
  margin: 0 auto;
  padding: 0;
  line-height: 0;
  font-size: 0;
  border: none; /* 无边框 */
  border-radius: 0; /* 无圆角 */
}
.ad-union-wrapper iframe {
  width: 300px;
  height: 250px;
  border: none;
  display: block;
  margin: 0;
  padding: 0;
  vertical-align: top;
}
</style>
