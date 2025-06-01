<template>
  <div class="wqk-product-grid">
    <div v-if="wqkLoading" class="wqk-loading">
      <p>加载中...</p>
    </div>
    <div v-else-if="wqkError" class="wqk-error">
      <p>{{ wqkError }}</p>
    </div>
    <div v-else class="wqk-products">
      <div v-for="product in wqkProducts" :key="product.id" class="wqk-product-item">
        <div class="wqk-product-image">
          <img :src="wqkGetImg(product.image)" :alt="product.name" />
        </div>
        <div class="wqk-product-colors">
          <div 
            v-for="(color, index) in product.colors" 
            :key="index"
            class="wqk-color-dot"
            :style="{ backgroundColor: color.hex }"
            :title="color.name"
          ></div>
        </div>
        <h3 class="wqk-product-name">{{ product.name }}</h3>
        <p class="wqk-product-tagline">{{ product.tagline }}</p>
        <div class="wqk-product-price">
          <p class="wqk-monthly-price" v-if="product.monthlyPrice">RMB {{ product.monthlyPrice }}/月起或 RMB {{ product.price }} 起*</p>
          <p class="wqk-price" v-else>RMB {{ product.price }} 起*</p>
        </div>
        <div class="wqk-product-actions">
          <router-link :to="`/iphone/${product.id}`" class="wqk-btn wqk-btn-primary">进一步了解</router-link>
          <router-link to="/store" class="wqk-btn wqk-btn-secondary">购买 ›</router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';
import WangProductCard from './WangProductCard.vue';
import { wqkUseErrorHandler } from '../../composables/wqkUseErrorHandler';

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../../assets/image/${imgName}`, import.meta.url).href;
};

// 响应式数据
const wqkProducts = ref([]);
const wqkLoading = ref(false);

// 使用错误处理组合函数
const { wqkError, wqkHandleError, wqkClearError, wqkCheckDataEmpty } = wqkUseErrorHandler();

// 获取产品数据
const wqkFetchProducts = async () => {
  try {
    wqkLoading.value = true;
    wqkClearError();
    const response = await axios.get('http://localhost:3000/iphones');
    wqkProducts.value = response.data;
    
    // 检查数据是否为空
    wqkCheckDataEmpty(response.data, 'iPhone产品数据为空', true);
    
    wqkLoading.value = false;
  } catch (error) {
    wqkHandleError(error, '获取iPhone产品数据失败，请检查网络连接', true);
    wqkLoading.value = false;
  }
};

// 组件挂载时获取数据
onMounted(() => {
  wqkFetchProducts();
});
</script>

<style scoped>
.wqk-product-grid {
  max-width: 1200px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.wqk-loading, .wqk-error {
  text-align: center;
  padding: 2rem;
  font-size: 1.1111rem;
  color: #1d1d1f;
}

.wqk-products {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 2rem;
}

.wqk-product-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 1.5rem;
  border-radius: 1rem;
  background-color: #fff;
  transition: transform 0.3s ease;
}

.wqk-product-item:hover {
  transform: translateY(-5px);
}

.wqk-product-image {
  width: 100%;
  height: 200px;
  margin-bottom: 1rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.wqk-product-image img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.wqk-product-colors {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.wqk-color-dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  cursor: pointer;
  border: 1px solid #e1e1e1;
}

.wqk-product-name {
  font-size: 1.2963rem; /* 7rem/5.4 */
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: #1d1d1f;
}

.wqk-product-tagline {
  font-size: 0.8889rem; /* 4.8rem/5.4 */
  color: #86868b;
  margin-bottom: 1rem;
}

.wqk-product-price {
  margin-bottom: 1.5rem;
}

.wqk-monthly-price, .wqk-price {
  font-size: 0.9259rem; /* 5rem/5.4 */
  color: #1d1d1f;
}

.wqk-product-actions {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  width: 100%;
}

.wqk-btn {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border-radius: 1.5rem;
  font-size: 0.9259rem;
  font-weight: 500;
  text-decoration: none;
  text-align: center;
  transition: all 0.3s ease;
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
}

.wqk-btn-secondary:hover {
  text-decoration: underline;
}

/* 响应式设计 */
@media (max-width: 1024px) {
  .wqk-products {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 640px) {
  .wqk-products {
    grid-template-columns: 1fr;
  }
}
</style>