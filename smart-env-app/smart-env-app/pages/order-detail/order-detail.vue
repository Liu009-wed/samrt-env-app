<template>
  <view class="container">
    <view class="card">
      <view class="title">订单详情</view>
      <image :src="formatImgUrl(detail.coverUrl)" class="detail-img" v-if="detail.coverUrl" mode="aspectFit" />
      <view class="info">
        <view>订单编号：{{detail.id}}</view>
        <view>回收类型：{{detail.goodTypeName}}</view>
        <view>重量/数量：{{detail.goodNumber}}</view>
        <view>预计收入：{{detail.goodNumber * detail.goodUnitPrice || 0}}元</view>
        <view>取货时间：{{detail.pickupTime}}</view>
        <view>取货地址：{{detail.pickupAddress}}</view>
        <view>联系电话：{{detail.contact}}</view>
        <view>状态：{{detail.state === 1 ? '已完成' : '待处理'}}</view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return { detail: {} };
  },
  async onLoad(options) {
    const res = await uni.request({ url: `http://localhost:3004/orders/${options.id}` });
    this.detail = res.data;
  },
  methods: {
    formatImgUrl(url) {
      if (!url) return '';
      if (url.indexOf(':') !== -1) return url;
      return 'http://localhost:3004' + url;
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.card { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; }
.title { font-size: 32rpx; font-weight: bold; margin-bottom: 20rpx; }
.detail-img { width: 100%; height: 300rpx; border-radius: 8rpx; margin-bottom: 20rpx; }
.info view { margin-bottom: 10rpx; }
</style>
