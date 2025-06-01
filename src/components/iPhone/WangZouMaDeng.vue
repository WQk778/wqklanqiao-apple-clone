<template>
  <div class="wqk-carousel-section">
    <div class="wqk-carousel-title">
      <h2>超广角摄像头，</h2>
      <h2>能着眼小细节，能放眼大视野。</h2>
    </div>
    <div class="wqk-carousel-container">
      <el-carousel 
        ref="wqkCarouselRef"
        :interval="0" 
        type="card" 
        height="500px"
        :autoplay="false"
        indicator-position="none"
        arrow="never"
        v-model="wqkCurrentIndex"
      >
        <el-carousel-item v-for="(item, index) in wqkCarouselItems" :key="index">
          <div class="wqk-carousel-card">
            <img :src="wqkGetImg(item.image)" :alt="item.title">
            <div class="wqk-carousel-content">
              <h3>{{ item.title }}</h3>
              <p>{{ item.description }}</p>
            </div>
          </div>
        </el-carousel-item>
      </el-carousel>
      
      <!-- 自定义轮播点 -->
      <div class="wqk-custom-indicators">
        <button 
          v-for="(item, index) in wqkCarouselItems" 
          :key="index"
          :class="['wqk-indicator-btn', { 'wqk-active': wqkCurrentIndex === index }]"
          @click="wqkHandleIndicatorClick(index)"
        >
          {{ item.label }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 当前轮播索引
const wqkCurrentIndex = ref(0);
const wqkCarouselRef = ref(null);

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../../assets/image/${imgName}`, import.meta.url).href;
};

// 轮播图数据
const wqkCarouselItems = ref([
  {
    title: '超广角视野',
    description: '120° 视野，捕捉更多细节',
    image: 'QW.jpg',
    label: '微距'
  },
  {
    title: '夜间模式',
    description: '低光环境下拍摄清晰照片',
    image: 'people.jpg',
    label: '0.5倍'
  },
  {
    title: '智能HDR',
    description: '智能优化照片细节和色彩',
    image: 'girl.jpg',
    label: '1倍'
  },
  {
    title: '人像模式',
    description: '专业级背景虚化效果',
    image: 'face.jpg',
    label: '2倍'
  }
]);

// 处理指示器点击
const wqkHandleIndicatorClick = (index) => {
  wqkCurrentIndex.value = index;
  if (wqkCarouselRef.value) {
    wqkCarouselRef.value.setActiveItem(index);
  }
};
</script>

<style scoped>
.wqk-carousel-section {
  padding: 3rem 0;
  background-color: #fff;
  width: 100%;
}

.wqk-carousel-title {
  text-align: center;
  margin-bottom: 3rem;
}

.wqk-carousel-title h2 {
  font-size: 2.2rem;
  font-weight: 600;
  margin: 0.5rem 0;
  color: #1d1d1f;
}

.wqk-carousel-container {
  max-width: 1400px;
  margin: 0 auto;
  position: relative;
  padding: 0 2rem;
}

.wqk-carousel-card {
  height: 100%;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
  background-color: #fff;
  position: relative;
  transform: scale(0.95);
  transition: transform 0.3s ease;
}

.wqk-carousel-card:hover {
  transform: scale(1);
}

.wqk-carousel-card img {
  width: 100%;
  height: 75%;
  object-fit: cover;
}

.wqk-carousel-content {
  padding: 1.5rem;
  text-align: center;
  height: 25%;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.wqk-carousel-content h3 {
  font-size: 1.4rem;
  margin-bottom: 0.8rem;
  color: #1d1d1f;
  font-weight: 600;
}

.wqk-carousel-content p {
  font-size: 1rem;
  color: #86868b;
  line-height: 1.4;
}

/* 自定义轮播指示器样式 */
.wqk-custom-indicators {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 2rem;
  gap: 0;
  background-color: #f5f5f7;
  border-radius: 25px;
  padding: 6px;
  width: fit-content;
  margin-left: auto;
  margin-right: auto;
}

.wqk-indicator-btn {
  padding: 10px 18px;
  border: none;
  background-color: transparent;
  color: #1d1d1f;
  font-size: 1rem;
  font-weight: 500;
  border-radius: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
  min-width: 70px;
}

.wqk-indicator-btn:hover {
  background-color: rgba(0, 0, 0, 0.05);
}

.wqk-indicator-btn.wqk-active {
  background-color: #1d1d1f;
  color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

/* 自定义 Element Plus 走马灯样式 */
:deep(.el-carousel__item) {
  border-radius: 16px;
}

:deep(.el-carousel__item--card) {
  border-radius: 16px;
  width: 60%;
}

:deep(.el-carousel__item.is-active) {
  z-index: 2;
}

/* 响应式设计 */
@media (max-width: 1024px) {
  .wqk-carousel-container {
    max-width: 1000px;
    padding: 0 1rem;
  }
  
  .wqk-carousel-title h2 {
    font-size: 2rem;
  }
}

@media (max-width: 768px) {
  .wqk-carousel-section {
    padding: 2rem 0;
  }
  
  .wqk-carousel-title h2 {
    font-size: 1.6rem;
  }
  
  .wqk-carousel-container {
    padding: 0 0.5rem;
  }
  
  .wqk-indicator-btn {
    padding: 8px 14px;
    font-size: 0.9rem;
    min-width: 60px;
  }
  
  .wqk-carousel-content h3 {
    font-size: 1.2rem;
  }
  
  .wqk-carousel-content p {
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .wqk-carousel-title h2 {
    font-size: 1.3rem;
  }
  
  .wqk-carousel-content {
    padding: 1rem;
  }
  
  .wqk-carousel-content h3 {
    font-size: 1rem;
  }
  
  .wqk-carousel-content p {
    font-size: 0.8rem;
  }
  
  .wqk-indicator-btn {
    padding: 6px 12px;
    font-size: 0.8rem;
    min-width: 50px;
  }
  
  .wqk-custom-indicators {
    margin-top: 1.5rem;
  }
}
</style>