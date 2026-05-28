<template>
  <view class="container">
    <view class="title">欢迎使用智慧环保</view>
    <input class="input" v-model="phone" placeholder="手机号" />
    <input class="input" v-model="username" placeholder="昵称" />
    <input class="input" v-model="password" placeholder="密码" type="password" />
    <button class="btn" @click="register">注册</button>
    <view class="tip" @click="goLogin">已有账号，立即登录</view>
  </view>
</template>

<script>
export default {
  data() {
    return { phone: "", username: "", password: "" };
  },
  methods: {
    async register() {
      if (!this.phone || !this.username || !this.password) {
        uni.showToast({ title: "请输入完整信息", icon: "none" });
        return;
      }
      const res = await uni.request({
        url: "http://localhost:3004/users",
        method: "POST",
        data: {
          phoneNum: this.phone,
          password: this.password,
          nickName: this.username,
          typeid: 1, // 1 为普通用户
          totalIncome: 0,
          recycleCount: 0,
          points: 0,
          avatar: "/image/avatar/1.jpeg" // 默认头像
        }
      });
      if (res.statusCode === 201) {
        uni.showToast({ title: "注册成功" });
        uni.navigateTo({ url: "/pages/login/login" });
      }
    },
    goLogin() {
      uni.navigateTo({ url: "/pages/login/login" });
    }
  }
};
</script>

<style scoped>
.container { padding: 40rpx; }
.title { font-size: 32rpx; margin-bottom: 40rpx; }
.input { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; margin-bottom: 20rpx; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; margin-top: 40rpx; }
.tip { text-align: center; color: #3cc51f; margin-top: 20rpx; }
</style>