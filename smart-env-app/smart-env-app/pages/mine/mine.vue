<template>
  <view class="container">
    <!-- 1. 顶部用户卡片（改成示例的蓝色渐变+签到按钮） -->
    <view class="user-card">
      <view class="avatar">👤</view>
      <view class="user-info">
        <view class="username">{{ user.name || "测试用户" }}</view>
        <view class="phone">{{ user.phone || "13800138000" }}</view>
      </view>
      <view class="sign-btn">
        <text>签到</text>
        <text class="arrow">></text>
      </view>
    </view>

    <!-- 2. 统计数据（和示例一致的三栏布局） -->
    <view class="stats">
      <view class="stat-item">
        <view class="num">{{ user.totalIncome || 0 }}</view>
        <view class="label">累计收益</view>
      </view>
      <view class="stat-item">
        <view class="num">{{ user.recycleCount || 0 }}</view>
        <view class="label">回收次数</view>
      </view>
      <view class="stat-item">
        <view class="num">{{ user.points || 100 }}</view>
        <view class="label">积分</view>
      </view>
    </view>

    <!-- 3. 功能菜单（在你原有基础上，补全示例的5个选项） -->
    <view class="menu-list">
      <view class="menu-item">
        <text class="icon">☆</text>
        <text class="text">积分记录</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item">
        <text class="icon">☒</text>
        <text class="text">兑换记录</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item">
        <text class="icon">♾</text>
        <text class="text">我的贡献</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item">
        <text class="icon">♡</text>
        <text class="text">收入记录</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item" @click="goShop">
        <text class="icon">🎁</text>
        <text class="text">积分商城</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item" @click="goSetting">
        <text class="icon">⚙️</text>
        <text class="text">设置</text>
        <text class="arrow">></text>
      </view>
      <view class="menu-item" @click="logout">
        <text class="icon">🚪</text>
        <text class="text">退出登录</text>
        <text class="arrow">></text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      user: {}
    };
  },
  onShow() {
    const localUser = uni.getStorageSync("user") || {};
    if (localUser.id) {
      this.getUserData(localUser.id);
    }
  },
  methods: {
    async getUserData(id) {
      try {
        const res = await uni.request({
          url: `http://localhost:3004/users/${id}`
        });
        if (res.data) {
          // 规范化数据，兼容 nickName/name 和 phoneNum/phone
          this.user = {
            ...res.data,
            name: res.data.nickName || res.data.name,
            phone: res.data.phoneNum || res.data.phone
          };
        }
      } catch (e) {
        this.user = uni.getStorageSync("user") || {};
      }
    },
    goShop() {
      uni.navigateTo({ url: "/pages/shop/shop" });
    },
    goSetting() {
      uni.navigateTo({ url: "/pages/setting/setting" });
    },
    logout() {
      uni.removeStorageSync("user");
      uni.switchTab({ url: "/pages/index/index" });
    }
  }
};
</script>

<style scoped>
.container {
  background-color: #f5f5f5;
  min-height: 100vh;
}

/* 用户卡片：改成示例的蓝色渐变背景 */
.user-card {
  background: linear-gradient(to right, #4a90e2, #357abd);
  color: white;
  display: flex;
  align-items: center;
  padding: 40rpx 30rpx;
}
.avatar {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
  background: white;
  color: #4a90e2;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 40rpx;
  margin-right: 30rpx;
}
.user-info {
  flex: 1;
}
.username {
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 10rpx;
}
.phone {
  font-size: 26rpx;
  opacity: 0.9;
}
.sign-btn {
  display: flex;
  align-items: center;
}
.sign-btn text {
  font-size: 26rpx;
  margin-right: 10rpx;
}
.arrow {
  font-size: 30rpx;
}

/* 统计数据：和用户卡片同色，三栏均分 */
.stats {
  background: linear-gradient(to right, #4a90e2, #357abd);
  color: white;
  display: flex;
  justify-content: space-around;
  padding: 30rpx 0;
}
.stat-item {
  text-align: center;
}
.num {
  font-size: 34rpx;
  font-weight: bold;
  margin-bottom: 10rpx;
}
.label {
  font-size: 24rpx;
  opacity: 0.9;
}

/* 菜单列表：和示例一致的分割线+箭头 */
.menu-list {
  background: white;
  margin-top: 20rpx;
}
.menu-item {
  display: flex;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #eee;
}
.icon {
  font-size: 30rpx;
  margin-right: 20rpx;
}
.text {
  flex: 1;
  font-size: 28rpx;
}
.arrow {
  font-size: 30rpx;
  color: #999;
}
</style>