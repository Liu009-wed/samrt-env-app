<template>
  <view class="container">
    <view class="search-bar">
      <input v-model="keyword" placeholder="请输入搜索内容" />
      <button @click="search">搜索</button>
    </view>
    <view class="result-list">
      <view class="item" v-for="item in resultList" :key="item.id">
        <image :src="'http://localhost:3004' + item.icon" class="item-img" v-if="item.icon" />
        <text>{{item.name}}</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { keyword: "", resultList: [] };
  },
  onLoad(options) {
    this.keyword = options.keyword || "";
    this.search();
  },
  methods: {
    async search() {
      const res = await uni.request({ url: "http://localhost:3004/types" });
      // 筛选出 parentId 不为 0 的具体物品，并根据关键词过滤
      this.resultList = res.data.filter(item => 
        item.parentId != 0 && item.name.includes(this.keyword)
      );
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.search-bar { display: flex; margin-bottom: 30rpx; }
.search-bar input { flex: 1; border: 1rpx solid #eee; border-radius: 8rpx; padding: 15rpx; }
.item { display: flex; align-items: center; padding: 20rpx; border-bottom: 1rpx solid #eee; }
.item-img { width: 80rpx; height: 80rpx; margin-right: 20rpx; border-radius: 8rpx; }
</style>