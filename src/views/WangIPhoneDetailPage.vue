<template>
  <div class="wqk-iphone-detail-page">
   
    <!-- 公告栏 -->
    <div class="wqk-announcement-bar">
      <p>即迄至 9 月 15 日，购买新 iPhone 享限时优惠，同时还有 24 个月免息分期。<span class="wqk-more-link">更多 ›</span></p>
    </div>
    
    <!-- 顶部标题和宣传语 -->
    <section class="wqk-hero-section" v-if="wqkProduct">
      <div class="wqk-hero-header">
        <h1 class="wqk-title">{{ wqkProduct.name }}</h1>
      </div>
      <div class="wqk-hero-image">
        <img src="../assets/image/touming.png" :alt="wqkProduct.name" />
      </div>
      <div class="wqk-hero-actions">
        <button class="wqk-buy-button">购买</button>
        <button class="wqk-learn-button">了解更多</button>
      </div>
    </section>

    <!-- 视频展示部分 -->
    <section class="wqk-video-section">
      <h2 class="wqk-section-title">视频展示</h2>
      <WangVideoCarousel 
        :videos="wqkVideos" 
        @video-changed="wqkHandleVideoChanged" 
        @play-state-changed="wqkHandlePlayStateChanged"
      />
    </section>

    <!-- 图片切换部分 -->
    <section class="wqk-image-switch-section" v-if="wqkProduct">
      <h2 class="wqk-section-title">定睛细看</h2>
      
      <div class="wqk-image-container">
        <img 
          :key="wqkCurrentImageIndex" 
          :src="wqkGetImg(wqkImages[wqkCurrentImageIndex].src)" 
          :alt="wqkImages[wqkCurrentImageIndex].alt" 
          class="wqk-detail-image animate__animated animate__fadeInLeft"
        />
      </div>
      
      <!-- 颜色选择按钮组 -->
      <div class="wqk-color-selector" v-if="wqkProduct && wqkProduct.colors">
        <div class="wqk-color-options">
          <div 
            v-for="(color, index) in wqkProduct.colors" 
            :key="index"
            class="wqk-color-option"
            :class="{ 'wqk-color-active': wqkCurrentColorIndex === index }"
            :style="{ backgroundColor: color.hex }"
            @click="wqkHandleColorChange(index)"
          >
            <span class="wqk-color-name">{{ color.name }}</span>
          </div>
        </div>
      </div>
      
      <!-- Element Plus Switch 开关 -->
      <div class="wqk-switch-container">
        <div class="wqk-switch-options">
          <span :class="{ 'wqk-active': !wqkSwitchValue }">{{ wqkImages[0].label }}</span>
          <el-switch
            v-model="wqkSwitchValue"
            class="wqk-custom-switch"
            @change="wqkHandleSwitchChange"
          />
          <span :class="{ 'wqk-active': wqkSwitchValue }">{{ wqkImages[1].label }}</span>
        </div>
      </div>
    </section>

    <!-- 紫色视频展示部分 -->
    <section class="wqk-video-showcase-section">
      <h2 class="wqk-video-showcase-title">相机控制全在握，一触、一滑、一按，一气呵成。</h2>
      <div class="wqk-video-container">
        <video 
          ref="wqkVideoPlayer" 
          class="wqk-showcase-video" 
          :src="wqkGetVideo('zise.mp4')" 
          loop
          muted
          playsinline
          @click="wqkToggleVideoPlay"
        ></video>
        <div class="wqk-video-replay-btn" @click="wqkReplayVideo">
          <i class="wqk-replay-icon"></i>
        </div>
      </div>
    </section>
    
    <!-- 走马灯组件 -->
    <WangZouMaDeng />
    
    <!-- 产品展示组件 -->
    <WangProductShowcase />
    
    <!-- 底部比较按钮 -->
    <WangCompareButton />
    
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, inject, watch } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import WangProductNavigation from '@/components/iPhone/WangProductNavigation.vue';
import WangVideoCarousel from '@/components/iPhone/WangVideoCarousel.vue';
import WangZouMaDeng from '@/components/iPhone/WangZouMaDeng.vue';
import WangProductShowcase from '@/components/iPhone/WangProductShowcase.vue';
import WangCompareButton from '@/components/iPhone/WangCompareButton.vue';

// 注入axios
const $axios = inject('$axios');
const route = useRoute();
const router = useRouter();

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../assets/image/${imgName}`, import.meta.url).href;
};

// 视频处理函数
const wqkGetVideo = (videoName) => {
  return new URL(`../assets/video/${videoName}`, import.meta.url).href;
};

// 定义数据状态
const wqkProduct = ref(null);
const wqkLoading = ref(true);
const wqkError = ref(null);

// 视频数据
const wqkVideos = ref([
  { src: 'iphone_preview.mp4', poster: 'DM_001.jpg', title: '设计概览' },
  { src: 'iPhone_16_Plus.mp4', poster: 'DM_002.jpg', title: '摄像头系统' },
  { src: 'iPhone_162.mp4', poster: 'DM_003.jpg', title: '性能表现' }
]);

// 视频状态处理函数
const wqkHandleVideoChanged = (index) => {
  console.log('视频已切换到:', index);
};

const wqkHandlePlayStateChanged = (isPlaying) => {
  console.log('播放状态已更改:', isPlaying);
};

// 图片数据
const wqkImages = ref([
  { src: 'touming2.png', alt: 'iPhone 正面视图', label: '6.3 英寸' },
  { src: 'touming3.png', alt: 'iPhone 背面视图', label: '6.9 英寸' }
]);

// 图片切换控制
const wqkCurrentImageIndex = ref(0);
const wqkSwitchValue = ref(false);

// 颜色切换控制
const wqkCurrentColorIndex = ref(0);
const wqkColorImages = ref({
  'iPhone 16 Pro': {
    '自然钛金属': { front: 'ziran.jpg', back: 'pink.png' },
    '白色钛金属': { front: 'white.jpg', back: 'touming2.png' },
    '黑色钛金属': { front: 'black.jpg', back: 'hei.png' }
  },
  'iPhone 16': {
    '蓝色': { front: 'blue.jpg', back: 'whiteXG.png' },
    '粉色': { front: 'pink.jpg', back: 'touming2.png' },
    '黄色': { front: 'caise.jpg', back: 'lanse.png' },
    '黑色': { front: 'black.jpg', back: 'hei.png' }
  },
  'iPhone 16e': {
    '黑色': { front: 'black.jpg', back: 'black_back.png' }
  },
  'iPhone 15': {
    '粉色': { front: 'pink.jpg', back: 'pink_back.png' },
    '黄色': { front: 'caise.jpg', back: 'yellow_back.png' },
    '绿色': { front: 'green.jpg', back: 'green_back.png' },
    '蓝色': { front: 'blue.jpg', back: 'blue_back.png' },
    '黑色': { front: 'black.jpg', back: 'black_back.png' }
  }
});

// 处理颜色切换
const wqkHandleColorChange = (index) => {
  // 先移除动画类
  const imageElement = document.querySelector('.wqk-detail-image');
  if (imageElement) {
    imageElement.classList.remove('animate__fadeInLeft');
  }
  
  // 延迟切换图片并重新添加动画
  setTimeout(() => {
    wqkCurrentColorIndex.value = index;
    
    // 更新图片源
    if (wqkProduct.value && wqkProduct.value.colors) {
      const colorName = wqkProduct.value.colors[index].name;
      const productName = wqkProduct.value.name;
      
      console.log(`切换颜色: ${productName} - ${colorName}`);
      
      if (wqkColorImages.value[productName] && wqkColorImages.value[productName][colorName]) {
        const images = wqkColorImages.value[productName][colorName];
        wqkImages.value[0].src = images.front;
        wqkImages.value[1].src = images.back;
        console.log(`已更新图片: 正面=${images.front}, 背面=${images.back}`);
      } else {
        console.warn(`未找到对应颜色图片: ${productName} - ${colorName}`);
        // 使用默认图片
        wqkImages.value[0].src = 'touming.png';
        wqkImages.value[1].src = 'touming2.png';
      }
    }
    
    // 重新添加动画类
    setTimeout(() => {
      const newImageElement = document.querySelector('.wqk-detail-image');
      if (newImageElement) {
        newImageElement.classList.add('animate__fadeInLeft');
      }
    }, 50);
  }, 100);
};

// 处理开关切换
const wqkHandleSwitchChange = (value) => {
  // 先移除动画类
  const imageElement = document.querySelector('.wqk-detail-image');
  if (imageElement) {
    imageElement.classList.remove('animate__fadeInLeft');
  }
  
  // 延迟切换图片并重新添加动画
  setTimeout(() => {
    wqkCurrentImageIndex.value = value ? 1 : 0;
    
    // 重新添加动画类
    setTimeout(() => {
      const newImageElement = document.querySelector('.wqk-detail-image');
      if (newImageElement) {
        newImageElement.classList.add('animate__fadeInLeft');
      }
    }, 50);
  }, 100);
};

// 视频播放器引用
const wqkVideoPlayer = ref(null);
const wqkIsVideoPlaying = ref(false);

// 切换视频播放/暂停
const wqkToggleVideoPlay = () => {
  if (!wqkVideoPlayer.value) return;
  
  if (wqkVideoPlayer.value.paused) {
    wqkVideoPlayer.value.play();
    wqkIsVideoPlaying.value = true;
  } else {
    wqkVideoPlayer.value.pause();
    wqkIsVideoPlaying.value = false;
  }
};

// 重播视频
const wqkReplayVideo = () => {
  if (!wqkVideoPlayer.value) return;
  
  wqkVideoPlayer.value.currentTime = 0;
  wqkVideoPlayer.value.play();
  wqkIsVideoPlaying.value = true;
};

// 获取产品数据
const wqkFetchProductDetail = async (productId) => {
  try {
    wqkLoading.value = true;
    const response = await $axios.get(`/iphones?id=${productId}`);
    if (response.data && response.data.length > 0) {
      wqkProduct.value = response.data[0];
      // 重置颜色索引
      wqkCurrentColorIndex.value = 0;
      
      // 初始化颜色相关的图片
      const productName = wqkProduct.value.name;
      const firstColorName = wqkProduct.value.colors[0].name;
      
      console.log(`加载产品: ${productName}, 初始颜色: ${firstColorName}`);
      
      // 检查是否有对应的颜色图片
      if (wqkColorImages.value[productName] && wqkColorImages.value[productName][firstColorName]) {
        const images = wqkColorImages.value[productName][firstColorName];
        wqkImages.value[0].src = images.front;
        wqkImages.value[1].src = images.back;
        console.log(`初始化图片: 正面=${images.front}, 背面=${images.back}`);
      } else {
        console.warn(`未找到初始颜色图片: ${productName} - ${firstColorName}`);
        // 使用默认图片
        wqkImages.value[0].src = 'touming.png';
        wqkImages.value[1].src = 'touming2.png';
      }
    } else {
      wqkError.value = '未找到产品信息';
      setTimeout(() => {
        router.push('/iphone');
      }, 2000);
    }
  } catch (error) {
    console.error('获取产品详情失败:', error);
    wqkError.value = '加载产品信息失败';
  } finally {
    wqkLoading.value = false;
  }
};

// 监听路由参数变化
watch(
  () => route.params.id,
  (newId) => {
    if (newId) {
      wqkFetchProductDetail(newId);
    }
  }
);

// 组件挂载时获取数据和初始化视频
onMounted(() => {
  if (route.params.id) {
    wqkFetchProductDetail(route.params.id);
  }
  
  // 初始化视频播放
  setTimeout(() => {
    if (wqkVideoPlayer.value) {
      wqkVideoPlayer.value.play().catch(err => {
        console.error('视频自动播放失败:', err);
      });
      wqkIsVideoPlaying.value = true;
    }
  }, 1000);
});

// 组件卸载时清理资源
onUnmounted(() => {
  // 停止视频播放
  if (wqkVideoPlayer.value) {
    wqkVideoPlayer.value.pause();
    wqkVideoPlayer.value.currentTime = 0;
  }
});
</script>

<style scoped>
.wqk-iphone-detail-page {
  width: 100%;
  margin: 0;
  padding: 0;
  color: #1d1d1f;
  font-family: 'SF Pro Display', 'SF Pro Icons', 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

/* 紫色视频展示部分样式 */
.wqk-video-showcase-section {
  margin: 6rem 0;
  text-align: center;
  width: 100%;
  overflow: hidden;
}

.wqk-video-showcase-title {
  font-size: 2.2rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 3rem;
  line-height: 1.4;
  padding: 0 1rem;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

.wqk-video-container {
  position: relative;
  width: 100vw;
  margin-left: calc(-50vw + 50%);
  overflow: hidden;
}

.wqk-showcase-video {
  width: 100%;
  display: block;
  cursor: pointer;
  height: auto;
}

.wqk-video-replay-btn {
  position: absolute;
  top: 30px;
  right: 30px;
  width: 60px;
  height: 60px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.25);
  z-index: 10;
}

.wqk-video-replay-btn:hover {
  background-color: rgba(255, 255, 255, 1);
  transform: scale(1.1);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.wqk-replay-icon {
  width: 24px;
  height: 24px;
  border: 3px solid #333;
  border-radius: 50%;
  position: relative;
}

.wqk-replay-icon::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 8px 0 8px 10px;
  border-color: transparent transparent transparent #333;
  transform: translate(-50%, -50%) rotate(-45deg);
  transform-origin: 25% 50%;
}

/* 导航栏样式 */
.wqk-nav-bar {
  background-color: #1d1d1f;
  color: #f5f5f7;
  height: 44px;
  width: 100%;
  position: sticky;
  top: 0;
  z-index: 100;
}

.wqk-nav-container {
  max-width: 1440px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  padding: 0 22px;
}

.wqk-logo {
  font-size: 1.2rem;
  font-weight: 600;
}

.wqk-nav-links {
  display: flex;
  gap: 30px;
}

.wqk-nav-link {
  color: #f5f5f7;
  text-decoration: none;
  font-size: 0.8rem;
  opacity: 0.8;
  transition: opacity 0.3s;
}

.wqk-nav-link:hover {
  opacity: 1;
}

/* 公告栏样式 */
.wqk-announcement-bar {
  background-color: #7272da;
  color: #1d1d1f;
  text-align: center;
  padding: 12px 0;
  font-size: 0.8rem;
}

.wqk-more-link {
  color: #0066cc;
  cursor: pointer;
}

/* 英雄区域样式 */
.wqk-hero-section {
  padding: 60px 20px;
  text-align: center;
  background: linear-gradient(180deg, #1e3a8a 0%, #3b82f6 20%, #60a5fa 40%, #93c5fd 60%, #dbeafe 80%, #f0f9ff 100%);
  color: #fff;
  position: relative;
  overflow: hidden;
  min-height: 80vh;
}

.wqk-hero-header {
  z-index: 2;
  margin-bottom: 2rem;
}

.wqk-title {
  font-size: 3rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.wqk-subtitle {
  font-size: 1.5rem;
  font-weight: 400;
  margin-bottom: 1.5rem;
  opacity: 0.9;
}

.wqk-hero-image {
  position: relative;
  z-index: 1;
  max-width: 800px;
  margin: 0 auto;
  transition: transform 0.5s ease;
}

.wqk-hero-image img {
  width: 100%;
  height: auto;
  display: block;
}

.wqk-hero-actions {
  margin-top: 2rem;
  display: flex;
  justify-content: center;
  gap: 1rem;
  z-index: 2;
  position: relative;
}

.wqk-buy-button,
.wqk-learn-button {
  padding: 12px 24px;
  border-radius: 30px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
}

.wqk-buy-button {
  background-color: #0071e3;
  color: white;
}

.wqk-buy-button:hover {
  background-color: #0077ed;
  transform: scale(1.05);
}

.wqk-learn-button {
  background-color: rgba(255, 255, 255, 0.1);
  color: white;
  backdrop-filter: blur(10px);
}

.wqk-learn-button:hover {
  background-color: rgba(255, 255, 255, 0.2);
  transform: scale(1.05);
}

/* 视频部分样式 */
.wqk-video-section {
  padding: 60px 20px;
  text-align: center;
  background-color: #f5f5f7;
}

.wqk-section-title {
  font-size: 2.5rem;
  font-weight: 600;
  margin-bottom: 2rem;
  color: #1d1d1f;
}

/* 图片切换部分样式 */
.wqk-image-switch-section {
  padding: 60px 20px;
  text-align: center;
  background-color: white;
  color: #060202;
}

.wqk-image-container {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto 2rem;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  height: 600px;
}

/* 移除以下CSS样式 */
/*
.wqk-fade-enter-active,
.wqk-fade-leave-active {
  transition: opacity 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.wqk-fade-enter-from,
.wqk-fade-leave-to {
  opacity: 0;
}
*/

.wqk-detail-image {
  width: auto;
  height: auto;
  max-width: 90%;
  max-height: 90%;
  object-fit: contain;
  animation-duration: 0.8s;
  min-height: 400px;
}

/* 颜色选择器样式 */
.wqk-color-selector {
  margin-bottom: 3rem;
  padding-bottom: 1rem;
}

.wqk-color-options {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
  padding-bottom: 2rem;
}

.wqk-color-option {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  cursor: pointer;
  position: relative;
  border: 2px solid transparent;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 2rem;
}

.wqk-color-option:hover {
  transform: scale(1.1);
}

.wqk-color-active {
  border-color: #0071e3;
  transform: scale(1.1);
}

.wqk-color-name {
  position: absolute;
  bottom: -35px;
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
  font-size: 0.8rem;
  opacity: 0;
  transition: opacity 0.3s ease;
  color: #1d1d1f;
  font-weight: 500;
  background-color: rgba(255, 255, 255, 0.9);
  padding: 2px 6px;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.wqk-color-option:hover .wqk-color-name,
.wqk-color-active .wqk-color-name {
  opacity: 1;
}

/* 开关容器样式 */
.wqk-switch-container {
  margin: 2rem auto;
  max-width: 300px;
}

.wqk-switch-options {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.wqk-switch-options span {
  font-size: 0.9rem;
  color: #86868b;
  transition: color 0.3s ease;
}

.wqk-switch-options span.wqk-active {
  color: #1d1d1f;
  font-weight: 500;
}

.wqk-custom-switch {
  --el-switch-on-color: #0071e3;
}

/* 产品规格部分样式 */
.wqk-specs-section {
  padding: 60px 20px;
  text-align: center;
  background-color: #f5f5f7;
}

.wqk-specs-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 2rem;
  max-width: 1200px;
  margin: 0 auto 3rem;
}

.wqk-spec-item {
  background-color: white;
  padding: 1.5rem;
  border-radius: 18px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.wqk-spec-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.1);
}

.wqk-spec-item p {
  font-size: 1rem;
  line-height: 1.5;
  color: #1d1d1f;
}

.wqk-product-actions {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.wqk-btn {
  padding: 12px 24px;
  border-radius: 30px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-decoration: none;
  display: inline-block;
}

.wqk-btn-primary {
  background-color: #0071e3;
  color: white;
}

.wqk-btn-primary:hover {
  background-color: #0077ed;
  transform: scale(1.05);
}

.wqk-btn-secondary {
  color: #0071e3;
}

.wqk-btn-secondary:hover {
  text-decoration: underline;
  transform: scale(1.05);
}

/* 响应式调整 */
@media (max-width: 768px) {
  .wqk-title {
    font-size: 2rem;
  }
  
  .wqk-subtitle {
    font-size: 1.2rem;
  }
  
  .wqk-section-title {
    font-size: 1.8rem;
  }
  
  .wqk-image-container {
    height: 450px;
  }
  
  .wqk-specs-container {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .wqk-hero-actions {
    flex-direction: column;
    align-items: center;
  }
  
  .wqk-buy-button,
  .wqk-learn-button {
    width: 100%;
    max-width: 250px;
  }
  
  .wqk-image-container {
    height: 350px;
  }
}
</style>