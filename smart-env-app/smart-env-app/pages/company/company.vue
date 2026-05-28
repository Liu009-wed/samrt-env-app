<template>
  <view class="container">
    <view class="search-bar">
      <input v-model="keyword" placeholder="请输入搜索内容" />
      <button @click="goSearch">搜索</button>
    </view>

    <view class="company-list">
      <view class="company-item" v-for="item in list" :key="item.id" @click="goDetail(item)">
        <image :src="'http://localhost:3004' + item.coverUrl" class="company-img" />
        <view class="info">
          <view class="name">{{item.name}}</view>
          <view class="address">{{item.address}}</view>
          <view class="phone">联系电话：{{item.contact}}</view>
          <view class="score">评分：{{item.score}} ⭐</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { list: [], keyword: "" };
  },
  async onLoad() {
    await this.getCompanies();
  },
  methods: {
    async getCompanies() {
      // 注意数据库表名是 companys
      const res = await uni.request({ url: "http://localhost:3004/companys" });
      this.list = res.data;
    },
    goSearch() {
      uni.navigateTo({ url: `/pages/company-search/company-search?keyword=${this.keyword}` });
    },
    goDetail(item) {
      uni.navigateTo({ url: `/pages/company-detail/company-detail?id=${item.id}` });
    },
    goOrder(item) {
      const user = uni.getStorageSync("user");
      if (!user) {
        uni.navigateTo({ url: "/pages/login/login" });
        return;
      }
      uni.navigateTo({ url: `/pages/place-order/place-order?companyId=${item.id}` });
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.search-bar { display: flex; margin-bottom: 30rpx; }
.search-bar input { flex: 1; border: 1rpx solid #eee; border-radius: 8rpx; padding: 15rpx; }
.company-item { display: flex; border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; margin-bottom: 20rpx; }
.info { flex: 1; }
.name { font-weight: bold; font-size: 30rpx; margin-bottom: 10rpx; }
.address, .time, .phone { font-size: 24rpx; color: #666; margin-bottom: 5rpx; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; align-self: center; }
</style>