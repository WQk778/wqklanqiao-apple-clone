<template>
  <div class="wqk-feature-section">
    <div class="wqk-feature-grid" ref="wqkFeatureGrid">
      <div 
        v-for="(feature, index) in wqkFeatures" 
        :key="index" 
        class="wqk-feature-item"
        @click="wqkShowFeatureDetail(feature)"
      >
        <div class="wqk-feature-image">
          <img :src="wqkGetImg(feature.image)" :alt="feature.title" />
          <div class="wqk-feature-content">
            <h3>{{ feature.title }}</h3>
            <p v-for="(desc, i) in feature.description" :key="i">{{ desc }}</p>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 轮播控制按钮 - 图片样式的左右箭头 -->
    <div class="wqk-carousel-navigation">
      <button 
        class="wqk-nav-button wqk-nav-prev"
        @click="wqkPrevPage"
        :disabled="wqkCurrentPage === 0"
      >
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
          <path d="M15 18L9 12L15 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
      <button 
        class="wqk-nav-button wqk-nav-next"
        @click="wqkNextPage"
        :disabled="wqkCurrentPage === wqkTotalPages - 1"
      >
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
          <path d="M9 18L15 12L9 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, onUnmounted } from 'vue';
import { ElMessageBox } from 'element-plus';

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../../assets/image/${imgName}`, import.meta.url).href;
};

// 特性数据
const wqkFeatures = ref([
  {
    title: '超先进摄像头系统',
    description: ['拍照、拍视频', '出手成片'],
    image: 'camera_.jpg',
    detail: '搭载全新的超先进摄像头系统，支持4K视频录制和夜间模式拍摄，让您随时随地捕捉精彩瞬间。',
    detailImage: 'modal_movies.jpg',
    extraContent: '全新的超先进摄像头系统采用了最新的传感器技术，提供更好的低光照表现和更高的动态范围。支持智能HDR 4，可以智能识别场景并优化照片效果。'
  },
  {
    title: '芯片和电池续航',
    description: ['一直飙一直快'],
    image: 'battery_.jpg',
    detail: '采用最新A系列芯片，性能提升20%，同时优化电池管理系统，单次充电可使用长达18小时。',
    detailImage: 'modal_supersmart.jpg',
    extraContent: '最新的A系列芯片采用5纳米工艺制程，集成了更多的神经网络引擎，可以更快地处理机器学习任务，同时能耗更低，为您提供更长久的电池续航时间。'
  },
  {
    title: '创新',
    description: ['设计到位', '耐看又耐用'],
    image: 'environment_.jpg',
    detail: '全新设计理念，采用航空级铝合金材质，轻薄坚固，同时保持经典外观，经久耐用。',
    detailImage: 'modal_ease.jpg',
    extraContent: '我们的设计团队精心打造每一个细节，从材料选择到工艺流程，确保每一台设备都能达到最高的品质标准。采用可回收材料，减少对环境的影响。'
  },
  {
    title: '环保',
    description: ['回收利用', '循环再循环'],
    image: 'innovation_.jpg',
    detail: '100%可回收材料制造，减少碳排放，支持旧设备回收计划，为地球环保贡献力量。',
    detailImage: 'modal_innovation.jpg',
    extraContent: '我们致力于到2030年实现碳中和目标，所有产品包装均使用可再生材料，并且我们的回收计划可以让您的旧设备得到妥善处理，减少电子垃圾。'
  },
  // 新增的三个卡片
  {
    title: '隐私',
    description: ['守护数据安全', '强大又好用'],
    image: 'privacy_.jpg',
    detail: '采用先进的数据加密技术和隐私保护措施，确保您的个人信息安全，同时提供便捷的隐私设置选项。',
    detailImage: 'privacy_.jpg',
    extraContent: '我们的隐私保护系统让您完全掌控自己的数据，应用追踪透明度功能让您清楚了解哪些应用在收集您的数据，并可以随时关闭不需要的追踪。'
  },
  {
    title: 'iPhone 个性化设置',
    description: ['彻底底', '很自己'],
    image: 'personalize_.jpg',
    detail: '锁定屏幕、主屏幕，处处都能自定。你常用的控制项可替换到锁定屏幕上，也可选一个分配给操作按钮；主屏幕上的图标和小组件随你调颜色，或让 iOS 选个与墙纸相配的色调。',
    detailImage: 'sIphone.png',
    extraContent: '全新的锁屏小组件让您无需解锁即可查看重要信息，支持多种锁屏样式切换，让您的iPhone更具个性化。动态岛功能为您提供实时活动更新，无缝融入使用体验。'
  },
  {
    title: '安全无忧',
    description: ['实用安全功能', '有备无患'],
    image: 'safety_.jpg',
    detail: '内置多重安全保障功能，包括紧急SOS、碰撞检测和卫星通信，在紧急情况下提供及时帮助。',
    detailImage: 'safety_.jpg',
    extraContent: '碰撞检测功能可以在检测到严重车祸时自动联系紧急服务，卫星SOS功能即使在没有蜂窝网络的地区也能发送紧急求助信息，为您的安全提供全方位保障。'
  }
]);

// 显示特性详情的方法
const wqkShowFeatureDetail = (feature) => {
  // 创建包含文字在上、图片在下的HTML
  const detailContent = `
    <div class="wqk-detail-container">
      <div class="wqk-detail-text">
        <p class="wqk-detail-main">${feature.detail}</p>
        <p class="wqk-detail-extra">${feature.extraContent}</p>
      </div>
      <div class="wqk-detail-image">
        <img src="${wqkGetImg(feature.detailImage)}" alt="${feature.title}" />
      </div>
    </div>
  `;

  ElMessageBox.alert(
    detailContent,
    `iPhone ${feature.title}`,
    {
      confirmButtonText: '了解更多',
      dangerouslyUseHTMLString: true, // 允许使用HTML内容
      customClass: 'wqk-feature-message-box',
      callback: () => {
        console.log('用户点击了解更多按钮');
      }
    }
  );
};

// 轮播功能相关变量
const wqkFeatureGrid = ref(null);
const wqkCurrentPage = ref(0);
const wqkVisibleItems = ref(4); // 一页显示4个项目
const wqkAutoplayInterval = ref(null);

// 计算总页数
const wqkTotalPages = computed(() => {
  return Math.ceil(wqkFeatures.value.length / wqkVisibleItems.value);
});

// 跳转到指定页面
const wqkGoToPage = (pageIndex) => {
  wqkCurrentPage.value = pageIndex;
  if (wqkFeatureGrid.value) {
    const scrollAmount = pageIndex * wqkFeatureGrid.value.clientWidth;
    wqkFeatureGrid.value.scrollTo({
      left: scrollAmount,
      behavior: 'smooth'
    });
  }
};

// 上一页
const wqkPrevPage = () => {
  if (wqkCurrentPage.value > 0) {
    wqkGoToPage(wqkCurrentPage.value - 1);
  }
};

// 下一页
const wqkNextPage = () => {
  if (wqkCurrentPage.value < wqkTotalPages.value - 1) {
    wqkGoToPage(wqkCurrentPage.value + 1);
  }
};

// 自动播放
const wqkStartAutoplay = () => {
  wqkAutoplayInterval.value = setInterval(() => {
    if (wqkCurrentPage.value < wqkTotalPages.value - 1) {
      wqkNextPage();
    } else {
      wqkGoToPage(0); // 回到第一页
    }
  }, 5000); // 5秒切换一次
};

// 停止自动播放
const wqkStopAutoplay = () => {
  if (wqkAutoplayInterval.value) {
    clearInterval(wqkAutoplayInterval.value);
    wqkAutoplayInterval.value = null;
  }
};

// 监听滚动事件，更新当前页
const wqkHandleScroll = () => {
  if (wqkFeatureGrid.value) {
    const scrollPosition = wqkFeatureGrid.value.scrollLeft;
    const pageWidth = wqkFeatureGrid.value.clientWidth;
    const currentPage = Math.round(scrollPosition / pageWidth);
    wqkCurrentPage.value = currentPage;
  }
};

// 组件挂载时初始化
onMounted(() => {
  if (wqkFeatureGrid.value) {
    wqkFeatureGrid.value.addEventListener('scroll', wqkHandleScroll);
  }
  wqkStartAutoplay();
});

// 组件卸载时清理
onUnmounted(() => {
  if (wqkFeatureGrid.value) {
    wqkFeatureGrid.value.removeEventListener('scroll', wqkHandleScroll);
  }
  wqkStopAutoplay();
});
</script>

<style scoped>
.wqk-feature-section {
  position: relative;
  width: 100%;
  margin-bottom: 3rem;
}

.wqk-feature-grid {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE and Edge */
  gap: 1.5rem;
  padding: 1rem;
  max-width: 1200px;
  margin: 0 auto;
}

.wqk-feature-grid::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}

.wqk-feature-item {
  flex: 0 0 calc(25% - 1.125rem);
  scroll-snap-align: start;
  border-radius: 18px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  cursor: pointer;
  position: relative;
  height: 400px; /* 设置固定高度 */
}

.wqk-feature-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

.wqk-feature-image {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

.wqk-feature-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.wqk-feature-content {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  padding: 1.5rem;
  text-align: left;
  color: white;
  z-index: 2;
  background: linear-gradient(to bottom, rgba(0,0,0,0.7) 0%, rgba(0,0,0,0.3) 50%, rgba(0,0,0,0) 100%);
}

.wqk-feature-content h3 {
  font-size: 1.5rem;
  margin-bottom: 0.5rem;
  color: white;
  text-shadow: 0 1px 3px rgba(0,0,0,0.5);
}

.wqk-feature-content p {
  font-size: 1rem;
  color: rgba(255, 255, 255, 0.9);
  margin: 0.25rem 0;
  text-shadow: 0 1px 2px rgba(0,0,0,0.5);
}

.wqk-carousel-navigation {
  position: relative;
  display: flex;
  justify-content: flex-end;
  margin-top: 1.5rem;
  margin-right: 1rem;
}

.wqk-nav-button {
  width: 2.5rem;
  height: 2.5rem;
  border-radius: 50%;
  background-color: #fff;
  border: none;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  margin: 0 0.5rem;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.wqk-nav-button:hover {
  background-color: #f5f5f7;
  transform: translateY(-2px);
}

.wqk-nav-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.wqk-nav-button svg {
  width: 1.25rem;
  height: 1.25rem;
}

/* 自定义MessageBox样式 */
.wqk-feature-message-box {
  border-radius: 20px;
  overflow: hidden;
  width: 80%;
  max-width: 700px;
}

.wqk-feature-message-box .el-message-box__header {
  background-color: white;
  padding: 15px 20px;
  position: relative;
}

.wqk-feature-message-box .el-message-box__title {
  font-size: 18px;
  font-weight: 600;
}

.wqk-feature-message-box .el-message-box__headerbtn {
  position: absolute;
  top: 15px;
  right: 15px;
  width: 30px;
  height: 30px;
  background-color: #333;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
}

.wqk-feature-message-box .el-message-box__headerbtn .el-message-box__close {
  color: white;
  font-size: 16px;
  font-weight: bold;
}

.wqk-feature-message-box .el-message-box__headerbtn:hover {
  background-color: #555;
}

.wqk-feature-message-box .el-message-box__content {
  padding: 0;
  font-size: 16px;
  line-height: 1.6;
}

.wqk-feature-message-box .el-message-box__btns {
  padding: 10px 20px 20px;
}

.wqk-feature-message-box .el-button {
  border-radius: 20px;
  padding: 10px 24px;
  font-size: 14px;
  background-color: #0071e3;
  color: white;
  border: none;
}

.wqk-feature-message-box .el-button:hover {
  background-color: #0077ed;
  opacity: 0.9;
}

/* 详情内容样式 */
.wqk-detail-container {
  display: flex;
  flex-direction: column;
}

.wqk-detail-text {
  padding: 20px 20px 15px;
}

.wqk-detail-main {
  font-size: 16px;
  margin-bottom: 15px;
  color: #1d1d1f;
}

.wqk-detail-extra {
  font-size: 14px;
  color: #86868b;
  line-height: 1.5;
  margin-bottom: 10px;
}

.wqk-detail-image {
  width: 100%;
  height: auto;
  overflow: hidden;
  margin-bottom: 20px;
}

.wqk-detail-image img {
  width: 100%;
  height: auto;
  object-fit: cover;
  display: block;
}

@media (min-width: 768px) {
  .wqk-detail-text {
    padding: 25px 25px 15px;
  }
  
  .wqk-detail-image {
    padding: 0 25px 25px;
  }
  
  .wqk-detail-main {
    font-size: 18px;
  }
  
  .wqk-detail-extra {
    font-size: 16px;
  }
}

@media (max-width: 1200px) {
  .wqk-feature-item {
    flex: 0 0 calc(33.33% - 1rem);
    height: 350px;
  }
}

@media (max-width: 992px) {
  .wqk-feature-item {
    flex: 0 0 calc(50% - 0.75rem);
    height: 300px;
  }
}

@media (max-width: 576px) {
  .wqk-feature-item {
    flex: 0 0 calc(100% - 0.5rem);
    height: 250px;
  }
  
  .wqk-feature-content h3 {
    font-size: 1.3rem;
  }
  
  .wqk-feature-content p {
    font-size: 0.9rem;
  }
  
  .wqk-nav-button {
    width: 1.75rem;
    height: 1.75rem;
  }
  
  .wqk-nav-button svg {
    width: 0.75rem;
    height: 0.75rem;
  }
}
</style>