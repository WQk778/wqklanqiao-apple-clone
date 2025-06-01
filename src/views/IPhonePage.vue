<template>
  <div class="wqk-iphone-page">
    <!-- 产品导航栏 -->
    <WangProductNavigation />
    <!-- 顶部标题和宣传语 -->
    <section class="wqk-hero-section">
      <div class="wqk-hero-header">
        <h1 class="wqk-title">iPhone</h1>
        <p class="wqk-slogan">悉心设计，动心更称心。</p>
      </div>
    </section>

    

    <!-- 产品预览视频部分 -->
    <section class="wqk-preview-section">
      
      <div class="wqk-preview-video" :class="{ 'wqk-video-shrink': wqkShouldShrink }">
        <div class="wqk-video-container">
          <video 
            ref="wqkVideoRef"
            class="wqk-video-player" 
            :poster="wqkGetImg('iphone2.jpg')"
            @click="wqkTogglePlay"
          >
            <source :src="wqkGetVideo('iphone_preview.mp4')" type="video/mp4">
            您的浏览器不支持视频播放
          </video>
          <div v-if="!wqkIsPlaying" class="wqk-play-button" @click="wqkTogglePlay">
            <div class="wqk-play-icon"></div>
          </div>
          <div v-if="wqkIsPlaying" class="wqk-pause-button" @click="wqkTogglePlay">
            <div class="wqk-pause-icon"></div>
          </div>
        </div>
      </div>
    </section>

    <!--   -->
    <section class="wqk-features-section">
      <h2 class="wqk-section-title">来了解一下 iPhone</h2>
      <WangFeatureGrid />
    </section>

    <!-- 全系列产品展示部分 -->
    <section class="wqk-products-section">
      <h2 class="wqk-section-title">全系产品细细看</h2>
      <div class="wqk-compare-link">
        <router-link to="/iphone/compare">比较各款机型 ›</router-link>
      </div>
      <WangProductGrid />
    </section>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import WangFeatureGrid from '@/components/iPhone/WangFeatureGrid.vue';
import WangProductGrid from '@/components/iPhone/WangProductGrid.vue';
import WangProductNavigation from '@/components/iPhone/WangProductNavigation.vue';

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../assets/image/${imgName}`, import.meta.url).href;
};

// 视频处理函数
const wqkGetVideo = (videoName) => {
  return new URL(`../assets/video/${videoName}`, import.meta.url).href;
};

// 视频播放控制
const wqkVideoRef = ref(null);
const wqkIsPlaying = ref(false);
const wqkShouldShrink = ref(false);

// 切换播放/暂停
const wqkTogglePlay = () => {
  if (!wqkVideoRef.value) return;
  
  if (wqkIsPlaying.value) {
    wqkVideoRef.value.pause();
  } else {
    wqkVideoRef.value.play();
  }
  
  wqkIsPlaying.value = !wqkIsPlaying.value;
};

// 滚动处理函数
const wqkHandleScroll = () => {
  const wqkScrollTop = window.pageYOffset || document.documentElement.scrollTop;
  wqkShouldShrink.value = wqkScrollTop > 150; // 调整为150px的滚动触发点
};

// 组件挂载时添加滚动监听
onMounted(() => {
  window.addEventListener('scroll', wqkHandleScroll);
});

// 组件卸载时移除滚动监听
onUnmounted(() => {
  window.removeEventListener('scroll', wqkHandleScroll);
});
</script>

<style scoped>
.wqk-iphone-page {
  width: 100%;
  overflow-x: hidden;
  transition: background-color var(--wqk-transition-normal);
}

:root.dark .wqk-iphone-page {
  background-color: var(--wqk-bg-primary);
}

.wqk-hero-section {
  text-align: center;
  padding: 4rem 1rem;
  background-color:white;
  transition: background-color var(--wqk-transition-normal);
}

:root.dark .wqk-hero-section {
  background-color: var(--wqk-bg-secondary);
}

.wqk-title {
  font-size: 3.7037rem; /* 20rem/5.4 */
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: #1d1d1f;
  transition: color var(--wqk-transition-normal);
}

:root.dark .wqk-title {
  color: var(--wqk-text-primary);
}

.wqk-slogan {
  font-size: 1.4815rem; /* 8rem/5.4 */
  color: #1d1d1f;
  margin-bottom: 2rem;
  transition: color var(--wqk-transition-normal);
}

:root.dark .wqk-slogan {
  color: var(--wqk-text-primary);
}

.wqk-hero-image {
  max-width: 1200px;
  margin: 0 auto;
}

.wqk-hero-image img {
  width: 100%;
  height: auto;
}

.wqk-preview-section {
  width: 100%;
  background-color: white;
  padding: 2rem 0;
  position: relative;
  overflow: hidden;
  transition: background-color var(--wqk-transition-normal);
}

:root.dark .wqk-preview-section {
  background-color: var(--wqk-bg-primary);
}

.wqk-preview-video {
  width: 100%;
  max-width: 100%;
  margin: 0 auto;
  transition: all 0.8s cubic-bezier(0.215, 0.610, 0.355, 1.000);
}

.wqk-video-shrink {
  max-width: 80%;
  transform: translateY(-15px) scale(0.95);
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

.wqk-video-container {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%; /* 16:9 宽高比 */
  border-radius: 12px;
  overflow: hidden;
  cursor: pointer;
}

.wqk-video-player {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.wqk-play-button,
.wqk-pause-button {
  position: absolute;
  bottom: 20px;
  right: 20px;
  width: 40px;
  height: 40px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.3s ease;
}

.wqk-play-button:hover,
.wqk-pause-button:hover {
  background-color: rgba(255, 255, 255, 0.4);
}

.wqk-play-icon {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 8px 0 8px 12px;
  border-color: transparent transparent transparent #ffffff;
  margin-left: 3px;
}

.wqk-pause-icon {
  width: 16px;
  height: 16px;
  display: flex;
  justify-content: space-between;
}

.wqk-pause-icon:before,
.wqk-pause-icon:after {
  content: "";
  width: 5px;
  height: 16px;
  background-color: #ffffff;
}

.wqk-hero-header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 2rem;
}

.wqk-title {
  font-size: 3.7037rem; /* 20rem/5.4 */
  font-weight: 600;
  color: #1d1d1f;
  margin-bottom: 0; /* 移除底部边距 */
}

.wqk-slogan {
  font-size: 1.4815rem; /* 8rem/5.4 */
  color: #1d1d1f;
  margin-bottom: 0; /* 移除底部边距 */
}

/* 响应式设计 */
@media (max-width: 768px) {
  .wqk-hero-header {
    flex-direction: column;
    gap: 0.5rem;
  }
  
  .wqk-title {
    font-size: 2.5926rem; /* 14rem/5.4 */
  }
  
  .wqk-slogan {
    font-size: 1.1111rem; /* 6rem/5.4 */
  }
}
.wqk-features-section {
  padding: 4rem 1rem;
  background-color: #fff;
  text-align: center;
  transition: background-color var(--wqk-transition-normal);
}

:root.dark .wqk-features-section {
  background-color: var(--wqk-bg-primary);
}

.wqk-section-title {
  font-size: 2.5926rem; /* 14rem/5.4 */
  font-weight: 600;
  margin-bottom: 2rem;
  color: #1d1d1f;
  transition: color var(--wqk-transition-normal);
}

:root.dark .wqk-section-title {
  color: var(--wqk-text-primary);
}

.wqk-products-section {
  padding: 4rem 1rem;
  background-color: #f5f5f7;
  text-align: center;
  transition: background-color var(--wqk-transition-normal);
}

:root.dark .wqk-products-section {
  background-color: var(--wqk-bg-secondary);
}

.wqk-compare-link {
  margin-bottom: 2rem;
}

.wqk-compare-link a {
  color: #0066cc;
  text-decoration: none;
  font-size: 1.1111rem; /* 6rem/5.4 */
  transition: color var(--wqk-transition-normal);
}

:root.dark .wqk-compare-link a {
  color: var(--wqk-accent-color);
}

.wqk-compare-link a:hover {
  text-decoration: underline;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .wqk-title {
    font-size: 2.5926rem; /* 14rem/5.4 */
  }
  
  .wqk-slogan {
    font-size: 1.1111rem; /* 6rem/5.4 */
  }
  
  .wqk-section-title {
    font-size: 1.8519rem; /* 10rem/5.4 */
  }
  
  .wqk-play-button,
  .wqk-pause-button {
    width: 60px;
    height: 60px;
  }
  
  .wqk-play-icon {
    border-width: 10px 0 10px 20px;
  }
  
  .wqk-pause-icon {
    width: 20px;
    height: 20px;
  }
  
  .wqk-pause-icon:before,
  .wqk-pause-icon:after {
    width: 7px;
    height: 20px;
  }
}
</style>