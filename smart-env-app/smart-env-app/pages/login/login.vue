<template>
  <view class="container">
    <view class="logo">🌱</view>
    <view class="title">欢迎使用智慧环保</view>
    <radio-group @change="handleRoleChange">
      <label><radio value="user" :checked="role === 'user'" />普通用户</label>
      <label><radio value="company" :checked="role === 'company'" />企业用户</label>
    </radio-group>
    <input class="input" v-model="phone" placeholder="手机号" type="number" />
    <input class="input" v-model="password" placeholder="密码" type="password" />
    <button class="btn" @click="login">登录</button>
    <view class="tip" @click="goRegister">注册新用户</view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      phone: "",
      password: "",
      role: "user"
    };
  },
  methods: {
    handleRoleChange(e) {
      this.role = e.detail.value;
    },
    async login() {
      if (!this.phone || !this.password) {
        return uni.showToast({ title: "请输入账号密码", icon: "none" });
      }
      
      try {
        // 获取所有用户数据进行本地匹配
        const res = await uni.request({
          url: "http://localhost:3004/users"
        });

        const users = res.data;
        console.log("数据库用户列表：", users);

        // 查找匹配的用户，兼容 phoneNum/phone 以及 typeid/role
        const user = users.find(u => {
          // 1. 匹配手机号 (兼容 phoneNum 和 phone)
          const matchPhone = (u.phoneNum === this.phone || u.phone === this.phone);
          // 2. 匹配密码
          const matchPassword = (u.password === this.password);
          // 3. 匹配角色 (db.json 中 typeid "1"/1 是用户，"2"/2 是企业；同时也兼容 role 字段)
          let matchRole = false;
          if (this.role === "user") {
             matchRole = (u.typeid == 1 || u.role === "user");
          } else {
             matchRole = (u.typeid == 2 || u.role === "company");
          }
          
          return matchPhone && matchPassword && matchRole;
        });

        if (user) {
          // 登录成功，规范化存储用户信息
          const userInfo = {
            id: user.id,
            phone: user.phoneNum || user.phone,
            name: user.nickName || user.name,
            role: this.role,
            avatar: user.avatar
          };
          
          uni.setStorageSync("user", userInfo);
          uni.showToast({ title: "登录成功" });
          
          // 延迟跳转以便用户看到提示
          setTimeout(() => {
            uni.switchTab({ url: "/pages/index/index" });
          }, 1000);
        } else {
          uni.showToast({ title: "账号、密码或身份不匹配", icon: "none" });
        }
      } catch (error) {
        console.error("登录请求失败:", error);
        uni.showToast({ title: "服务器连接失败", icon: "none" });
      }
    },
    goRegister() {
      uni.navigateTo({ url: "/pages/register/register" });
    }
  }
};
</script>

<style scoped>
.container {
  padding: 40rpx;
}
.logo {
  text-align: center;
  font-size: 80rpx;
  margin-top: 100rpx;
  margin-bottom: 20rpx;
}
.title {
  font-size: 36rpx;
  font-weight: bold;
  text-align: center;
  margin-bottom: 60rpx;
}
.input {
  border: 1rpx solid #eee;
  border-radius: 8rpx;
  padding: 25rpx;
  margin-bottom: 30rpx;
  background-color: #f9f9f9;
}
.btn {
  background: #3cc51f;
  color: #fff;
  border-radius: 8rpx;
  margin-top: 40rpx;
  height: 90rpx;
  line-height: 90rpx;
}
.tip {
  text-align: center;
  color: #3cc51f;
  margin-top: 40rpx;
  font-size: 28rpx;
}
</style>