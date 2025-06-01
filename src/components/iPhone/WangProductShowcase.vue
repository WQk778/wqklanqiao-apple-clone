<template>
  <div class="wqk-product-showcase">
    <div class="wqk-showcase-container">
      <div 
        v-for="product in wqkProducts" 
        :key="product.id"
        class="wqk-product-card"
      >
        <!-- 产品图片 -->
        <div class="wqk-product-image">
          <img :src="wqkGetImg(product.image)" :alt="product.name" />
        </div>
        
        <!-- 颜色指示器 -->
        <div class="wqk-color-indicators">
          <div 
            v-for="(color, index) in product.colors" 
            :key="index"
            class="wqk-color-dot"
            :style="{ backgroundColor: color.hex }"
            :class="{ 'wqk-active': index === 0 }"
          ></div>
        </div>
        
        <!-- 产品信息 -->
        <div class="wqk-product-info">
          <h3 class="wqk-product-name">{{ product.name }}</h3>
          <p class="wqk-product-price">{{ product.price }}</p>
          <p class="wqk-monthly-price">{{ product.monthlyPrice }}</p>
        </div>
        
        <!-- 操作按钮 -->
        <div class="wqk-product-actions">
          <div class="wqk-action-text">当前机型</div>
          <div class="wqk-action-buttons">
            <button class="wqk-btn wqk-btn-link">{{ product.actions.primary }} ›</button>
            <button class="wqk-btn wqk-btn-primary">{{ product.actions.secondary }}</button>
          </div>
          <button class="wqk-btn wqk-btn-link">{{ product.actions.compare }} ›</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, inject } from 'vue';

// 注入axios
const $axios = inject('$axios');

// 产品数据
const wqkProducts = ref([]);
const wqkLoading = ref(true);
const wqkError = ref(null);

// 图片处理函数
const wqkGetImg = (imgName) => {
  return new URL(`../../assets/image/${imgName}`, import.meta.url).href;
};

// 获取产品展示数据
const wqkFetchProductShowcase = async () => {
  try {
    wqkLoading.value = true;
    const response = await $axios.get('/productShowcase');
    if (response.data) {
      wqkProducts.value = response.data;
    }
  } catch (error) {
    console.error('获取产品展示数据失败:', error);
    wqkError.value = '加载产品信息失败';
  } finally {
    wqkLoading.value = false;
  }
};

// 组件挂载时获取数据
onMounted(() => {
  wqkFetchProductShowcase();
});
</script>

<style scoped>
.wqk-product-showcase {
  padding: 4rem 2rem;
  background-color: #f5f5f7;
  width: 100%;
  display: flex;
  justify-content: center;
}

.wqk-showcase-container {
  max-width: 1200px;
  width: 100%;
  background-color: #fff;
  border-radius: 20px;
  padding: 3rem 2rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 3rem;
  align-items: start;
}

.wqk-product-card {
  background-color: transparent;
  border-radius: 18px;
  padding: 2rem;
  text-align: center;
  transition: transform 0.3s ease;
  position: relative;
  border: 1px solid #e5e5e7;
}

.wqk-product-card:hover {
  transform: translateY(-5px);
  border-color: #d1d1d6;
}

.wqk-product-image {
  margin-bottom: 1.5rem;
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.wqk-product-image img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.wqk-color-indicators {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

.wqk-color-dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  border: 2px solid transparent;
  transition: all 0.3s ease;
  cursor: pointer;
}

.wqk-color-dot.wqk-active {
  border-color: #1d1d1f;
  transform: scale(1.2);
}

.wqk-color-dot:hover {
  transform: scale(1.1);
}

.wqk-product-info {
  margin-bottom: 2rem;
}

.wqk-product-name {
  font-size: 1.8rem;
  font-weight: 600;
  color: #1d1d1f;
  margin-bottom: 0.5rem;
}

.wqk-product-price {
  font-size: 1.1rem;
  color: #1d1d1f;
  margin-bottom: 0.3rem;
  font-weight: 500;
}

.wqk-monthly-price {
  font-size: 0.9rem;
  color: #86868b;
  margin-bottom: 0;
}

.wqk-product-actions {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.wqk-action-text {
  font-size: 0.9rem;
  color: #86868b;
  font-weight: 500;
}

.wqk-action-buttons {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.wqk-btn {
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
  text-decoration: none;
  display: inline-block;
}

.wqk-btn-primary {
  background-color: #0071e3;
  color: #fff;
  padding: 10px 20px;
}

.wqk-btn-primary:hover {
  background-color: #0077ed;
  transform: scale(1.05);
}

.wqk-btn-link {
  background-color: transparent;
  color: #0071e3;
  border: none;
  padding: 8px 0;
}

.wqk-btn-link:hover {
  text-decoration: underline;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .wqk-product-showcase {
    padding: 2rem 1rem;
  }
  
  .wqk-showcase-container {
    grid-template-columns: 1fr;
    gap: 2rem;
  }
  
  .wqk-product-card {
    padding: 1.5rem;
  }
  
  .wqk-product-image {
    height: 250px;
  }
  
  .wqk-product-name {
    font-size: 1.5rem;
  }
  
  .wqk-action-buttons {
    flex-direction: column;
    gap: 0.5rem;
  }
}

@media (max-width: 480px) {
  .wqk-product-card {
    padding: 1rem;
  }
  
  .wqk-product-image {
    height: 200px;
  }
  
  .wqk-product-name {
    font-size: 1.3rem;
  }
}
</style>