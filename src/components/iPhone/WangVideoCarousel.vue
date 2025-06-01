<template>
  <section class="wqk-video-carousel-section">
    <div class="wqk-video-carousel">
      <!-- 视频卡片容器 -->
      <div class="wqk-video-cards-wrapper">
        <div 
          class="wqk-video-cards-container"
          :style="{ transform: `translateX(calc(-${wqkCurrentVideoIndex * 80}% - ${wqkCurrentVideoIndex * 20}px))` }"
        >
          <div 
            v-for="(video, index) in wqkVideos" 
            :key="index"
            class="wqk-video-card"
            :class="{ 'wqk-active': wqkCurrentVideoIndex === index }"
            @click="wqkChangeVideo(index)"
          >
            <div class="wqk-video-container">
              <video 
                :ref="el => { if(el) wqkVideoRefs[index] = el }"
                class="wqk-video-player" 
                :src="wqkGetVideo(video.src)" 
                :poster="wqkGetImg(video.poster)"
                @click.stop="wqkTogglePlay"
                preload="metadata"
              ></video>
              <div class="wqk-video-title">{{ video.title }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 视频轮播控制器 -->
    <div class="wqk-video-controls">
      <div class="wqk-play-control" @click="wqkTogglePlay">
        <div class="wqk-play-icon" v-if="!wqkIsPlaying"></div>
        <div class="wqk-pause-icon" v-else></div>
      </div>
      
      <div class="wqk-video-dots">
        <div 
          v-for="(video, index) in wqkVideos" 
          :key="index"
          class="wqk-video-dot"
          :class="{ 'wqk-active': wqkCurrentVideoIndex === index }"
          @click="wqkChangeVideo(index)"
        ></div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, computed } from 'vue';

// 定义组件属性
const props = defineProps({
  videos: {
    type: Array,
    required: true,
    default: () => []
  }
});

// 定义事件
const emit = defineEmits(['video-changed', 'play-state-changed']);

// 视频处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../../assets/image/${imgName}`, import.meta.url).href;
};

const wqkGetVideo = (videoName) => {
  return new URL(`../../assets/video/${videoName}`, import.meta.url).href;
};

// 视频播放控制
const wqkVideoRefs = ref({});
const wqkIsPlaying = ref(false);
const wqkCurrentVideoIndex = ref(0);
const wqkSlideDirection = ref('right'); // 滑动方向

// 视频数据
const wqkVideos = ref([]);

// 获取当前视频元素
const wqkCurrentVideoRef = computed(() => {
  return wqkVideoRefs.value[wqkCurrentVideoIndex.value];
});

// 初始化视频数据
onMounted(() => {
  wqkVideos.value = props.videos;
  console.log('组件挂载，视频数据:', wqkVideos.value);
});

// 监听视频数据变化
watch(() => props.videos, (newVideos) => {
  wqkVideos.value = newVideos;
  console.log('视频数据更新:', wqkVideos.value);
}, { deep: true, immediate: true });

// 切换播放/暂停
const wqkTogglePlay = () => {
  if (!wqkCurrentVideoRef.value) return;
  
  if (wqkIsPlaying.value) {
    wqkCurrentVideoRef.value.pause();
  } else {
    wqkCurrentVideoRef.value.play().catch(err => {
      console.error('视频播放失败:', err);
    });
  }
  
  wqkIsPlaying.value = !wqkIsPlaying.value;
  emit('play-state-changed', wqkIsPlaying.value);
};

// 切换视频
const wqkChangeVideo = (index) => {
  if (wqkCurrentVideoIndex.value === index) return;
  
  // 设置滑动方向
  wqkSlideDirection.value = index > wqkCurrentVideoIndex.value ? 'right' : 'left';
  
  // 如果正在播放，先暂停当前视频
  if (wqkIsPlaying.value && wqkCurrentVideoRef.value) {
    wqkCurrentVideoRef.value.pause();
    wqkIsPlaying.value = false;
    emit('play-state-changed', false);
  }
  
  wqkCurrentVideoIndex.value = index;
  emit('video-changed', index);
  
  // 重置新视频
  if (wqkVideoRefs.value[index]) {
    wqkVideoRefs.value[index].load();
  }
};

// 组件卸载时清理资源
onUnmounted(() => {
  // 暂停所有视频并清理资源
  Object.values(wqkVideoRefs.value).forEach(videoEl => {
    if (videoEl) {
      videoEl.pause();
      videoEl.src = '';
    }
  });
});
</script>

<style scoped>
/* 视频轮播区域样式 */
.wqk-video-carousel-section {
  padding: 4rem 0;
  background-color: #f5f5f7;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.wqk-video-carousel {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
}

/* 视频卡片容器包装器 - 控制可见区域 */
.wqk-video-cards-wrapper {
  width: 100%;
  overflow: hidden;
  padding: 20px 0;
}

/* 视频卡片容器 */
.wqk-video-cards-container {
  display: flex;
  width: 100%;
  transition: transform 0.5s ease;
  padding: 0 10%;
}

/* 视频卡片 */
.wqk-video-card {
  flex: 0 0 80%;
  width: 80%;
  padding: 0 10px;
  box-sizing: border-box;
  transition: all 0.3s ease;
  transform: scale(0.9);
  opacity: 0.7;
  cursor: pointer;
}

.wqk-video-card.wqk-active {
  transform: scale(1);
  opacity: 1;
  z-index: 2;
}

.wqk-video-container {
  width: 100%;
  border-radius: 20px;
  overflow: hidden;
  position: relative;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  aspect-ratio: 16/9;
}

/* 确保第一个视频卡片的圆角正常显示 */
.wqk-video-card:first-child .wqk-video-container {
  border-radius: 20px;
  overflow: hidden;
}

.wqk-video-card:first-child .wqk-video-player {
  border-radius: 20px;
}

.wqk-video-player {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.wqk-video-title {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: linear-gradient(to top, rgba(0,0,0,0.7), transparent);
  color: white;
  padding: 1rem;
  font-size: 1.2rem;
  text-align: center;
}

/* 视频控制器样式 */
.wqk-video-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f5f5f7;
  border-radius: 30px;
  padding: 10px 20px;
  margin: 20px auto;
  max-width: 300px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.wqk-play-control {
  width: 48px;
  height: 48px;
  background-color: #f0f0f0;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  margin-right: 20px;
}

.wqk-play-control:hover {
  background-color: #e0e0e0;
  transform: scale(1.05);
}

.wqk-play-icon {
  width: 0;
  height: 0;
  border-top: 8px solid transparent;
  border-left: 12px solid #000;
  border-bottom: 8px solid transparent;
  margin-left: 3px;
}

.wqk-pause-icon {
  position: relative;
  width: 10px;
  height: 14px;
}

.wqk-pause-icon:before,
.wqk-pause-icon:after {
  content: '';
  position: absolute;
  width: 3px;
  height: 14px;
  background-color: #000;
  top: 0;
}

.wqk-pause-icon:before {
  left: 0;
}

.wqk-pause-icon:after {
  right: 0;
}

.wqk-video-dots {
  display: flex;
  align-items: center;
  gap: 12px;
}

.wqk-video-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #999;
  cursor: pointer;
  transition: all 0.2s ease;
}

.wqk-video-dot:hover {
  background-color: #666;
}

.wqk-video-dot.wqk-active {
  background-color: #007AFF;
  width: 12px;
  height: 12px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .wqk-video-card {
    flex: 0 0 90%;
    width: 90%;
  }
  
  .wqk-video-controls {
    padding: 8px 15px;
  }
  
  .wqk-play-control {
    width: 36px;
    height: 36px;
    margin-right: 15px;
  }
  
  .wqk-video-dot {
    width: 6px;
    height: 6px;
  }
  
  .wqk-video-dot.wqk-active {
    width: 10px;
    height: 10px;
  }
}
</style>