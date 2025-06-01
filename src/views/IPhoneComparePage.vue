<template>
  <div class="wqk-compare-page">
    <!-- 顶部标题 -->
    <div class="wqk-header">
      <h1 class="wqk-title">iPhone 机型比较</h1>
      <p class="wqk-subtitle">选购 iPhone ›</p>
      <p class="wqk-description">如需获得选购帮助，请联系我们的 Specialist 专家 ›</p>
    </div>

    <div v-if="wqkLoading" class="wqk-loading">
      <p>加载中...</p>
    </div>
    <div v-else-if="wqkError" class="wqk-error">
      <p>{{ wqkError }}</p>
    </div>
    <div v-else class="wqk-compare-container">
      <!-- 选择器区域 -->
      <div class="wqk-selectors">
        <div v-for="(selector, index) in wqkSelectors" :key="index" class="wqk-selector-item">
          <el-select 
            v-model="selector.selected" 
            @change="wqkHandleSelectChange(index, $event)"
            class="wqk-select"
            placeholder="选择机型"
            size="large"
          >
            <el-option-group label="新款 iPhone 机型">
              <el-option
                v-for="product in wqkNewProducts"
                :key="product.id"
                :label="product.name"
                :value="product.id"
              />
            </el-option-group>
            <el-option-group label="更多 iPhone 机型">
              <el-option
                v-for="product in wqkOlderProducts"
                :key="product.id"
                :label="product.name"
                :value="product.id"
              />
            </el-option-group>
          </el-select>
        </div>
      </div>

      <!-- 产品展示区域 -->
      <div class="wqk-products-display">
        <div v-for="(selector, index) in wqkSelectors" :key="index" class="wqk-product-column">
          <div v-if="selector.selected" class="wqk-product-card">
            <!-- 产品图片 -->
            <div class="wqk-product-image" :class="{ 'animate__animated animate__flipOut': wqkAnimatingIndex === index }">
              <img :src="wqkGetImg(wqkGetSelectedProduct(selector.selected)?.image)" :alt="wqkGetSelectedProduct(selector.selected)?.name" />
            </div>
            
            <!-- 颜色选择器 -->
             <div class="wqk-color-options">
               <div 
                 v-for="color in wqkGetSelectedProduct(selector.selected)?.colors" 
                 :key="color.name"
                 class="wqk-color-dot"
                 :style="{ backgroundColor: color.hex }"
                 :class="{ active: selector.selectedColor === color.name }"
                 @click="wqkSelectColor(index, color.name)"
               ></div>
             </div>
            <p class="wqk-color-name">{{ selector.selectedColor || wqkGetSelectedProduct(selector.selected)?.colors[0]?.name }}</p>

            <!-- 产品信息 -->
            <div class="wqk-product-info">
              <p class="wqk-product-price">{{ wqkGetSelectedProduct(selector.selected)?.price }}</p>
              <p class="wqk-product-monthly">{{ wqkGetSelectedProduct(selector.selected)?.monthlyPrice }}</p>
            </div>

            <!-- 操作按钮 -->
            <div class="wqk-product-actions">
              <button class="wqk-btn wqk-btn-primary">购买</button>
              <button class="wqk-btn wqk-btn-secondary">进一步了解</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, inject, computed } from 'vue';
import { useRouter } from 'vue-router';
import 'animate.css';

// 注入axios
const $axios = inject('$axios');
const router = useRouter();

// 图片处理函数
const wqkGetImg = (imgName) => {
  if (!imgName) return '';
  return new URL(`../assets/image/${imgName}`, import.meta.url).href;
};

// 定义数据状态
const wqkProducts = ref([]);
const wqkLoading = ref(true);
const wqkError = ref(null);
const wqkAnimatingIndex = ref(-1);

// 选择器配置
const wqkSelectors = ref([
  { label: 'iPhone 16 Pro Max', selected: 'iphone-16-pro', selectedColor: null },
  { label: 'iPhone 16', selected: 'iphone-16', selectedColor: null },
  { label: 'iPhone 16e', selected: 'iphone-16e', selectedColor: null }
]);

// 计算属性：新款iPhone
const wqkNewProducts = computed(() => {
  return wqkProducts.value.filter(product => 
    ['iphone-16-pro', 'iphone-16-pro-max', 'iphone-16', 'iphone-16-plus', 'iphone-16e'].includes(product.id)
  );
});

// 计算属性：更多iPhone机型
const wqkOlderProducts = computed(() => {
  return wqkProducts.value.filter(product => 
    !['iphone-16-pro', 'iphone-16-pro-max', 'iphone-16', 'iphone-16-plus', 'iphone-16e'].includes(product.id)
  );
});

// 根据ID获取产品信息
const wqkGetSelectedProduct = (productId) => {
  return wqkProducts.value.find(product => product.id === productId);
};

// 处理选择器变化
const wqkHandleSelectChange = (index, productId) => {
  // 触发动画
  wqkAnimatingIndex.value = index;
  
  setTimeout(() => {
    wqkSelectors.value[index].selected = productId;
    wqkSelectors.value[index].selectedColor = null; // 重置颜色选择
    
    // 更新选择器的label为选中产品的名称
    const selectedProduct = wqkGetSelectedProduct(productId);
    if (selectedProduct) {
      wqkSelectors.value[index].label = selectedProduct.name;
    }
    
    wqkAnimatingIndex.value = -1;
  }, 300);
};

// 选择颜色
const wqkSelectColor = (index, colorName) => {
  wqkSelectors.value[index].selectedColor = colorName;
};

// 跳转到详情页
const wqkNavigateToDetail = (productId) => {
  router.push(`/iphone/${productId}`);
};

// 获取产品数据
const wqkFetchProducts = async () => {
  try {
    wqkLoading.value = true;
    const response = await $axios.get('/iphones');
    wqkProducts.value = response.data;
    wqkLoading.value = false;
  } catch (error) {
    console.error('获取产品数据失败:', error);
    wqkError.value = '获取产品数据失败，请稍后再试';
    wqkLoading.value = false;
  }
};

// 组件挂载时获取数据
onMounted(() => {
  wqkFetchProducts();
});
</script>

<style scoped>
.wqk-compare-page {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 1rem;
  background-color: #fff;
}

/* 顶部标题区域 */
.wqk-header {
  text-align: center;
  padding: 3rem 0 2rem;
  border-bottom: 1px solid #d2d2d7;
  margin-bottom: 2rem;
}

.wqk-title {
  font-size: 2.7778rem; /* 15rem/5.4 */
  font-weight: 600;
  color: #1d1d1f;
  margin-bottom: 1rem;
}

.wqk-subtitle {
  font-size: 1.1111rem; /* 6rem/5.4 */
  color: #0071e3;
  margin-bottom: 0.5rem;
  cursor: pointer;
}

.wqk-subtitle:hover {
  text-decoration: underline;
}

.wqk-description {
  font-size: 1.0185rem; /* 5.5rem/5.4 */
  color: #0071e3;
  cursor: pointer;
}

.wqk-description:hover {
  text-decoration: underline;
}

.wqk-loading, .wqk-error {
  text-align: center;
  padding: 2rem;
  font-size: 1.1111rem;
  color: #1d1d1f;
}

/* 比较容器 */
.wqk-compare-container {
  padding: 2rem 0;
}

/* 选择器区域 */
.wqk-selectors {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
  margin-bottom: 3rem;
  padding: 0 2rem;
}

.wqk-selector-item {
  display: flex;
  justify-content: center;
}

.wqk-select {
  width: 100%;
  max-width: 300px;
}

.wqk-select .el-input__wrapper {
  border-radius: 8px;
  border: 1px solid #d2d2d7;
  padding: 12px 16px;
  font-size: 1.0185rem;
}

.wqk-select .el-input__wrapper:hover {
  border-color: #0071e3;
}

.wqk-select-label {
  font-weight: 500;
  color: #1d1d1f;
}

/* 产品展示区域 */
.wqk-products-display {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
  padding: 0 2rem;
}

.wqk-product-column {
  display: flex;
  justify-content: center;
}

.wqk-product-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  width: 100%;
  max-width: 350px;
}

/* 产品图片 */
.wqk-product-image {
  width: 280px;
  height: 350px;
  margin-bottom: 1.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.3s ease;
}

.wqk-product-image img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

/* 颜色选择器 */
.wqk-color-options {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.wqk-color-dot {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid #d2d2d7;
  cursor: pointer;
  transition: all 0.2s ease;
}

.wqk-color-dot:hover,
.wqk-color-dot.active {
  border-color: #0071e3;
  transform: scale(1.1);
}

.wqk-color-name {
  font-size: 0.9259rem;
  color: #86868b;
  margin-bottom: 1rem;
}

/* 产品信息 */
.wqk-product-info {
  margin-bottom: 1.5rem;
}

.wqk-product-price {
  font-size: 1.1111rem;
  font-weight: 600;
  color: #1d1d1f;
  margin-bottom: 0.25rem;
}

.wqk-product-monthly {
  font-size: 0.9259rem;
  color: #86868b;
}

/* 操作按钮 */
.wqk-product-actions {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  width: 100%;
}

.wqk-btn {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border-radius: 20px;
  font-size: 0.9259rem;
  font-weight: 500;
  text-decoration: none;
  text-align: center;
  transition: all 0.3s ease;
  border: none;
  cursor: pointer;
}

.wqk-btn-primary {
  background-color: #0071e3;
  color: #fff;
}

.wqk-btn-primary:hover {
  background-color: #0077ed;
}

.wqk-btn-secondary {
  background-color: transparent;
  color: #0071e3;
  border: 1px solid #0071e3;
}

.wqk-btn-secondary:hover {
  background-color: #0071e3;
  color: #fff;
}

/* 动画效果 */
.animate__flipOut {
  animation-duration: 0.3s;
}

/* 响应式设计 */
@media (max-width: 1024px) {
  .wqk-selectors,
  .wqk-products-display {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .wqk-product-image {
    width: 240px;
    height: 300px;
  }
}

@media (max-width: 768px) {
  .wqk-selectors,
  .wqk-products-display {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
  
  .wqk-product-image {
    width: 200px;
    height: 250px;
  }
  
  .wqk-title {
    font-size: 2.2222rem;
  }
}
</style>