<template>
  <view class="container">
    <view class="header">当前积分：{{user.points || 0}}</view>
    <view class="goods-list">
      <view class="goods-item" v-for="item in goods" :key="item.id">
        <view class="name">{{item.name}}</view>
        <view class="points">所需积分：{{item.points}}</view>
        <button class="btn" @click="exchange(item)" :disabled="user.points < item.points">兑换</button>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      user: {},
      goods: [
        { id: 1, name: "环保帆布袋", points: 50 },
        { id: 2, name: "可降解垃圾袋", points: 100 },
        { id: 3, name: "绿植盆栽", points: 200 }
      ]
    };
  },
  onShow() {
    this.user = uni.getStorageSync("user");
  },
  methods: {
    async exchange(item) {
      if (this.user.points < item.points) {
        uni.showToast({ title: "积分不足", icon: "none" });
        return;
      }
      const newPoints = this.user.points - item.points;
      await uni.request({
        url: `http://localhost:3004/users/${this.user.id}`,
        method: "PATCH",
        data: { points: newPoints }
      });
      uni.setStorageSync("user", { ...this.user, points: newPoints });
      uni.showToast({ title: "兑换成功" });
      this.user.points = newPoints;
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.header { font-size: 32rpx; margin-bottom: 30rpx; }
.goods-item { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; margin-bottom: 20rpx; display: flex; justify-content: space-between; align-items: center; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; padding: 10rpx 20rpx; }
.btn[disabled] { background: #ccc; }
</style>