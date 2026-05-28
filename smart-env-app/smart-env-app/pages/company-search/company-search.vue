<template>
  <view class="container">
    <view class="search-bar">
      <input v-model="keyword" placeholder="请输入搜索内容" />
      <button @click="search">搜索</button>
    </view>
    <view class="list">
      <view class="item" v-for="item in list" :key="item.id" @click="goDetail(item)">
        <image :src="'http://localhost:3004' + item.coverUrl" class="company-img" />
        <view class="info">
          <view class="name">{{item.name}}</view>
          <view class="address">{{item.address}}</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { keyword: "", list: [] };
  },
  onLoad(options) {
    this.keyword = options.keyword || "";
    this.search();
  },
  methods: {
    async search() {
      // 先获取所有公司数据
      const res = await uni.request({ 
        url: 'http://localhost:3004/companys' 
      });
      const allCompanys = res.data;
      
      // 在前端进行模糊匹配，支持名称或地址搜索
      if (this.keyword) {
        this.list = allCompanys.filter(item => 
          item.name.includes(this.keyword) || 
          item.address.includes(this.keyword)
        );
      } else {
        this.list = allCompanys;
      }
    },
    goDetail(item) {
      uni.navigateTo({ url: `/pages/company-detail/company-detail?id=${item.id}` });
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.search-bar { display: flex; margin-bottom: 30rpx; }
.search-bar input { flex: 1; border: 1rpx solid #eee; border-radius: 8rpx; padding: 15rpx; }
.item { display: flex; padding: 20rpx; border-bottom: 1rpx solid #eee; }
.company-img { width: 120rpx; height: 120rpx; border-radius: 8rpx; margin-right: 20rpx; }
.info { flex: 1; }
.name { font-weight: bold; margin-bottom: 5rpx; }
.address { color: #666; }
</style>