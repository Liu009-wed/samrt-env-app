<template>
  <view class="container">
    <view class="tab-bar">
      <text :class="{ active: currentTab === index }" 
            v-for="(tab, index) in tabs" :key="index" 
            @click="switchTab(index)">{{ tab }}</text>
    </view>

    <view class="order-list">
      <view class="order-item" v-for="item in orderList" :key="item.id" @click="goDetail(item)">
        <view class="header">
          <text>下单时间：{{ item.createTime }}</text>
          <text class="status">{{ item.state == 1 ? '已完成' : '待处理' }}</text>
        </view>
        <view class="content">
          <image :src="formatImgUrl(item.coverUrl)" class="order-img" v-if="item.coverUrl" />
          <view class="info">
            <view>回收类型：{{ item.goodTypeName }}</view>
            <view>重量/数量：{{ item.goodNumber || item.pkgs }}</view>
            <view>联系电话：{{ item.contact }}</view>
            <view>取货地址：{{ item.pickupAddress }}</view>
          </view>
        </view>
        <view class="btn-group">
          <button class="btn" v-if="!item.state || item.state == 0" @click.stop="cancelOrder(item.id)">取消订单</button>
          <button class="btn" v-if="user.role === 'company' && (!item.state || item.state == 0)" @click.stop="acceptOrder(item.id)">接单</button>
        </view>
      </view>
      
      <!-- 空订单提示 -->
      <view class="empty" v-if="orderList.length === 0">
        暂无订单
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      tabs: ["待处理", "已完成"], // 简化标签以匹配数据库的 state (0 和 1)
      currentTab: 0,
      orderList: [],
      user: {}
    };
  },
  onShow() {
    this.user = uni.getStorageSync("user") || {};
    if (!this.user.id) {
      uni.navigateTo({ url: "/pages/login/login" });
      return;
    }
    this.getOrders();
  },
  methods: {
    async getOrders() {
      try {
        const res = await uni.request({
          url: "http://localhost:3004/orders"
        });
        
        let list = res.data;
        console.log("当前登录用户ID:", this.user.id);
        console.log("数据库中所有订单:", list);

        // 关键修改：使用 String() 强制转换后再比较，确保 ID 匹配成功
        list = list.filter(item => {
          const matchUser = String(item.userId) === String(this.user.id);
          return matchUser;
        });

        // 根据状态过滤
        if (this.currentTab === 0) {
          this.orderList = list.filter(item => item.state == 0 || !item.state).reverse();
        } else {
          this.orderList = list.filter(item => item.state == 1).reverse();
        }

        console.log("过滤后的订单列表:", this.orderList);
      } catch (e) {
        uni.showToast({ title: "加载失败", icon: "none" });
      }
    },
    switchTab(index) {
      this.currentTab = index;
      this.getOrders();
    },
    formatImgUrl(url) {
      if (!url) return '';
      // 如果是完整的网络路径或本地临时路径(包含 ://)，则直接返回
      if (url.indexOf(':') !== -1) return url;
      // 否则拼接服务器地址
      return 'http://localhost:3004' + url;
    },
    goDetail(item) {
      uni.navigateTo({ url: `/pages/order-detail/order-detail?id=${item.id}` });
    },
    async updateOrderState(id, newState) {
      await uni.request({
        url: `http://localhost:3004/orders/${id}`,
        method: "PATCH",
        data: { state: newState }
      });
      uni.showToast({ title: "操作成功" });
      this.getOrders();
    },
    async cancelOrder(id) {
      await uni.request({
        url: `http://localhost:3004/orders/${id}`,
        method: "DELETE" // 模拟取消订单通常是删除或改状态，这里改为改状态为 -1 或删除
      });
      uni.showToast({ title: "已取消" });
      this.getOrders();
    },
    async acceptOrder(id) {
      await uni.request({
        url: `http://localhost:3004/orders/${id}`,
        method: "PATCH",
        data: { state: 1 } // 0 待处理 -> 1 已完成
      });
      uni.showToast({ title: "已接单" });
      this.getOrders();
    },
    async completeOrder(id) {
      await uni.request({
        url: `http://localhost:3004/orders/${id}`,
        method: "PATCH",
        data: { state: 1 }
      });
      uni.showToast({ title: "已完成" });
      this.getOrders();
    }
  }
};
</script>

<style scoped>
.container { padding: 20rpx; }
.tab-bar { display: flex; justify-content: space-around; margin-bottom: 30rpx; border-bottom: 1rpx solid #eee; }
.tab-bar text { padding: 20rpx; }
.tab-bar .active { color: #3cc51f; border-bottom: 3rpx solid #3cc51f; }
.order-item { border: 1rpx solid #eee; border-radius: 8rpx; padding: 20rpx; margin-bottom: 20rpx; }
.header { display: flex; justify-content: space-between; margin-bottom: 10rpx; }
.status { color: #3cc51f; }
.content { display: flex; margin-top: 20rpx; }
.order-img { width: 140rpx; height: 140rpx; border-radius: 8rpx; margin-right: 20rpx; }
.info { flex: 1; }
.btn-group { margin-top: 20rpx; display: flex; justify-content: flex-end; }
.btn { background: #3cc51f; color: #fff; border-radius: 8rpx; padding: 10rpx 20rpx; margin-left: 10rpx; }
.empty { text-align: center; padding: 40rpx; color: #999; }
</style>