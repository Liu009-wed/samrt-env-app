<template>
  <view class="container">
    <!-- 顶部搜索栏 -->
    <view class="search-bar">
      <input v-model="searchKeyword" placeholder="请输入搜索内容" @confirm="goSearch" />
    </view>

    <!-- 轮播/宣传图 -->
    <swiper class="banner" indicator-dots circular autoplay>
      <swiper-item v-for="dot in hotDots" :key="dot.id">
        <image :src="'http://localhost:3004' + dot.image" class="banner-img" />
      </swiper-item>
    </swiper>

    <!-- 快捷功能区（和示例图的4个按钮对应） -->
    <view class="function">
      <view class="item" @click="goCategory">
        <image src="/static/icon-category.png" class="icon" />
        <text>回收分类</text>
      </view>
      <view class="item" @click="goOrder">
        <image src="/static/icon-order.png" class="icon" />
        <text>旧物去向</text>
      </view>
      <view class="item" @click="goCompany">
        <image src="/static/icon-company.png" class="icon" />
        <text>附近回收</text>
      </view>
      <view class="item" @click="goShop">
        <image src="/static/icon-shop.png" class="icon" />
        <text>积分商城</text>
      </view>
    </view>

    <!-- 预约上门回收按钮 -->
    <button class="order-btn" @click="goPlaceOrder">预约上门回收</button>

    <!-- 预约流程（和示例图的4步对应） -->
    <view class="process">
      <view class="step">
        <text class="icon">🕒</text>
        <text>在线预约<br/>第1步</text>
      </view>
      <view class="step">
        <text class="icon">🏠</text>
        <text>免费上门<br/>第2步</text>
      </view>
      <view class="step">
        <text class="icon">✅</text>
        <text>订单完成<br/>第3步</text>
      </view>
      <view class="step">
        <text class="icon">⭐</text>
        <text>用户福利<br/>第4步</text>
      </view>
    </view>

    <!-- 爱心活动图片 -->
    <view class="activity">
      <text class="title">爱心活动</text>
      <image src="/static/activity.png" class="activity-img" />
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      searchKeyword: "",
      hotDots: [],
      actions: []
    }
  },
  async onLoad() {
    const [dotRes, actionRes] = await Promise.all([
      uni.request({ url: "http://localhost:3004/hotDots" }),
      uni.request({ url: "http://localhost:3004/actions" })
    ]);
    this.hotDots = dotRes.data;
    this.actions = actionRes.data;
  },
  methods: {
    goCategory() {
      uni.navigateTo({ url: "/pages/category/category" });
    },
    goOrder() {
      uni.switchTab({ url: "/pages/order/order" });
    },
    goCompany() {
      uni.switchTab({ url: "/pages/company/company" });
    },
    goShop() {
      uni.navigateTo({ url: "/pages/shop/shop" });
    },
    goSearch() {
      uni.navigateTo({ 
        url: `/pages/company-search/company-search?keyword=${this.searchKeyword}` 
      });
    },
    goPlaceOrder() {
      const user = uni.getStorageSync("user");
      if (!user) {
        uni.navigateTo({ url: "/pages/login/login" });
        return;
      }
      uni.navigateTo({ url: "/pages/place-order/place-order" });
    }
  }
};
</script>

<style scoped>
.container {
  padding: 20rpx;
}
.search-bar {
  background: #f5f5f5;
  border-radius: 8rpx;
  padding: 15rpx 20rpx;
  margin-bottom: 20rpx;
}
.banner {
  height: 300rpx;
  margin-bottom: 30rpx;
}
.banner-img {
  width: 100%;
  height: 100%;
  border-radius: 8rpx;
}
.function {
  display: flex;
  justify-content: space-around;
  margin-bottom: 40rpx;
}
.function .item {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.icon {
  width: 80rpx;
  height: 80rpx;
  margin-bottom: 10rpx;
}
.order-btn {
  background: #3cc51f;
  color: #fff;
  border-radius: 8rpx;
  margin: 40rpx 0;
}
.process {
  display: flex;
  justify-content: space-around;
  margin-bottom: 40rpx;
}
.step {
  text-align: center;
  font-size: 24rpx;
}
.activity .title {
  font-size: 28rpx;
  margin-bottom: 20rpx;
}
.activity-img {
  width: 100%;
  height: 200rpx;
  border-radius: 8rpx;
}
</style>