<template>
  <div class="card-container">
    <!-- 提供整个hover-area的插槽 -->

    <!-- 默认的hover-area实现 -->
    <div
      class="hover-area"
      @mouseenter="isHovered = true"
      @mouseleave="isHovered = false"
      :style="resolvedStyle"
    >
      <!-- 允许外部通过具名插槽传入整个卡片 -->
      <slot name="card">
        <n-card class="hover-card">
          <template #header>
            <div class="card-title">卡片标题</div>
          </template>
          <div class="card-content">这是一个居中显示的卡片，当鼠标悬浮时会从右侧展开操作按钮。</div>
          <template #footer>
            <div class="card-footer">最后更新：今天</div>
          </template>
        </n-card>
      </slot>

      <!-- 按钮容器绝对定位在卡片右侧 -->
      <div class="action-buttons" :class="{ 'buttons-visible': isHovered }">
        <slot name="hover-area">
          <n-button circle secondary class="action-button">
            <template #icon>
              <n-icon><EditOutlined /></n-icon>
            </template>
          </n-button>
          <n-button circle secondary class="action-button">
            <template #icon>
              <n-icon><ShareAltOutlined /></n-icon>
            </template>
          </n-button>
          <n-button circle secondary class="action-button">
            <template #icon>
              <n-icon><DeleteOutlined /></n-icon>
            </template>
          </n-button>
          <n-button circle secondary class="action-button">
            <template #icon>
              <n-icon><MoreOutlined /></n-icon>
            </template>
          </n-button>
        </slot>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, type ComputedRef, type PropType, type StyleValue, computed } from 'vue'
import { NCard, NButton, NIcon } from 'naive-ui'
import { EditOutlined, ShareAltOutlined, DeleteOutlined, MoreOutlined } from '@vicons/antd'

// 修改：prop 名称更改为 cardStyle 以更清晰地表示其用途
const props = defineProps({
  cardStyle: {
    type: [String, Object] as PropType<StyleValue | ComputedRef<string>>,
    default: '',
  },
})

// 添加计算属性来解析可能的 ComputedRef 值
const resolvedStyle = computed(() => {
  if (props.cardStyle && typeof props.cardStyle === 'object' && 'value' in props.cardStyle) {
    return props.cardStyle.value
  }
  return props.cardStyle
})

const isHovered = ref(false)
</script>

<style scoped>
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 悬浮区域包含卡片和按钮，但不影响布局 */
.hover-area {
  position: relative;
  display: inline-block; /* 使元素宽度适应内容 */
}

.hover-card {
  transition: all 0.3s;
  /* 卡片本身不需要特殊定位，保持正常流布局 */
}

.card-title {
  font-weight: bold;
  font-size: 16px;
}

.card-content {
  padding: 8px 0;
}

.card-footer {
  font-size: 12px;
  color: rgba(0, 0, 0, 0.45);
}

.action-buttons {
  position: absolute;
  top: 0;
  left: 100%; /* 定位在卡片右侧 */
  height: 100%;
  display: flex;

  gap: 8px;
  padding-left: 8px; /* 与卡片保持一定距离 */
  transition: all 0.3s;
  opacity: 0;
  transform: translateX(-20px);
  pointer-events: none; /* 默认情况下禁用鼠标事件 */
}

.buttons-visible {
  opacity: 1;
  transform: translateX(0);
  pointer-events: auto; /* 显示时启用鼠标事件 */
}

.action-button {
  transition: transform 0.2s;
}

.action-button:hover {
  transform: scale(1.1);
}
</style>
