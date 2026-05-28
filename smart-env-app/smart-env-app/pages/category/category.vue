<template>
  <view class="container">
    <view class="search-bar">
      <input v-model="keyword" placeholder="请输入搜索内容" />
      <button @click="goSearch">搜索</button>
    </view>
    <view class="category-list">
      <view class="category-item" v-for="cat in categories" :key="cat.id">
        <view class="cat-title">{{cat.name}}</view>
        <view class="cat-items">
          <view class="item" v-for="item in cat.children" :key="item.id">
            <image :src="'http://localhost:3004' + item.icon" class="item-img" v-if="item.icon" />
            <view class="item-name">{{item.name}}</view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { 
      categories: [], 
      categoryDetails: [],
      keyword: "" 
    };
  },
  async onLoad() {
    // 请求统一的 types 接口
    const res = await uni.request({ url: "http://localhost:3004/types" });
    const allTypes = res.data;

    // 1. 筛选出所有大类 (parentId 为 0)
    const parentCategories = allTypes.filter(t => t.parentId == 0);

    // 2. 将子类按 parentId 归类到父类下
    this.categories = parentCategories.map(parent => {
      return {
        ...parent,
        children: allTypes.filter(t => t.parentId == parent.id)
      };
    });
  },
  methods: {
    goSearch() {
      uni.navigateTo({ url: `/pages/category-result/category-result?keyword=${this.keyword}` });
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.search-bar { display: flex; margin-bottom: 30rpx; }
.search-bar input { flex: 1; border: 1rpx solid #eee; border-radius: 8rpx; padding: 15rpx; }
.category-item { margin-bottom: 30rpx; }
.cat-title { font-weight: bold; margin-bottom: 15rpx; color: #333; }
.cat-items { display: flex; flex-wrap: wrap; }
.item {
  width: 160rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20rpx;
}
.item-img {
  width: 100rpx;
  height: 100rpx;
  border-radius: 8rpx;
  margin-bottom: 10rpx;
}
.item-name {
  font-size: 24rpx;
  text-align: center;
}
</style>