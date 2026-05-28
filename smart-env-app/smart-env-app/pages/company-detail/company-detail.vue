<template>
  <view class="container">
    <view class="card">
      <view class="name">{{detail.name}}</view>
      <view class="address">地址：{{detail.address}}</view>
      <view class="phone">联系电话：{{detail.contact}}</view>
      <view class="intro" v-if="detail.introduction">简介：{{detail.introduction}}</view>
      <button class="btn" @click="goOrder">预约上门回收</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { detail: {} };
  },
  async onLoad(options) {
    const res = await uni.request({ url: `http://localhost:3004/companys/${options.id}` });
    this.detail = res.data;
  },
  methods: {
    goOrder() {
      const user = uni.getStorageSync("user");
      if (!user) { uni.navigateTo({ url: "/pages/login/login" }); return; }
      uni.navigateTo({ url: `/pages/place-order/place-order?companyId=${this.detail.id}` });
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.card { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; }
.name { font-size: 36rpx; font-weight: bold; margin-bottom: 20rpx; }
.address, .time, .phone { font-size: 28rpx; margin-bottom: 10rpx; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; margin-top: 30rpx; }
</style>