<template>
  <view class="container">
    <view class="form-item">
      <text>修改手机号</text>
      <input v-model="newPhone" placeholder="请输入新手机号" />
    </view>
    <view class="form-item">
      <text>修改密码</text>
      <input v-model="newPwd" placeholder="请输入新密码" type="password" />
    </view>
    <button class="btn" @click="save">保存修改</button>
  </view>
</template>

<script>
export default {
  data() {
    return { newPhone: "", newPwd: "", user: {} };
  },
  onLoad() {
    this.user = uni.getStorageSync("user");
  },
  methods: {
    async save() {
      const data = {};
      if (this.newPhone) data.phone = this.newPhone;
      if (this.newPwd) data.password = this.newPwd;
      await uni.request({
        url: `http://localhost:3004/users/${this.user.id}`,
        method: "PATCH",
        data
      });
      uni.setStorageSync("user", { ...this.user, ...data });
      uni.showToast({ title: "修改成功" });
    }
  }
};
</script>

<style scoped>
.container { padding: 30rpx; }
.form-item { display: flex; flex-direction: column; margin-bottom: 30rpx; }
.form-item text { font-size: 28rpx; margin-bottom: 10rpx; }
.form-item input { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; margin-top: 40rpx; }
</style>