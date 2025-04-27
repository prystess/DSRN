<template>
  <div class="section-container">
    <div class="section-header">
      <div class="header-icon">
        <slot name="icon"></slot>
        <slot name="title"></slot>
      </div>
    </div>

    <div v-if="showTotal" class="total-count">Total:{{ totalItems }}</div>

    <div class="scrollable-content">
      <slot></slot>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: {
    type: String,
    required: true,
  },
  totalItems: {
    type: Number,
    default: 0,
  },
  showTotal: {
    type: Boolean,
    default: true,
  },
});
</script>

<style scoped>
@font-face {
  font-family: "SFProDisplayBold";  /* 自定义字体名称 */
  src: url("/src/assets/font/SFPRODISPLAYMEDIUM.OTF") format("opentype"); /* 字体文件路径 */
  font-weight: bold;               /* 字体粗细 */
  font-style: normal;              /* 字体样式 */
}

.section-container {
  width: 100%;
  height: 100%;
  border-radius: 10px;     /* 保留原圆角 */
  border: 1px solid transparent; /* 渐变边框必须透明 */

  display: flex;
  flex-direction: column;  /* 保持原有flex结构 */
  overflow: hidden;        /* 保持内容不溢出 */

  /* 渐变边框 + 背景透明区域 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
              linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea) border-box;

  /* 玻璃模糊效果 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  overflow: hidden;
}

.section-header {
  display: flex;
  align-items: center;
  padding: 1.5rem;
  padding-bottom: 0.5rem;
}

.header-icon {
  color: #b429ff;
  margin-right: 0.5rem;
}

.section-title {
  color: #b429ff;
  font-size: 1.5rem;
  font-weight: bold;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.total-count {
  color: #b429ff;
  padding: 0 1.5rem;
  margin-bottom: 1rem;
  font-size: 1.2rem;              /* 设置字体大小 */
  font-weight: bold;              /* 设置字体粗细 */
  font-family: "SFProDisplayBold", sans-serif;  /* 使用自定义字体 */
  color: #ffffff;                 /* 设置字体颜色 */
  letter-spacing: 1px;            /* 可选：字母间距 */
}

.scrollable-content {
  flex: 1;
  overflow-y: auto;
  padding: 0 1.5rem 1.5rem;
}

/* Scrollbar styling */
.scrollable-content::-webkit-scrollbar {
  width: 6px;
}

.scrollable-content::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
}

.scrollable-content::-webkit-scrollbar-thumb {
  background: #b429ff;
  border-radius: 3px;
}
</style>
