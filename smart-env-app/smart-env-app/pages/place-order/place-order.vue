<template>
  <view class="container">
    <view class="form-item">
      <text>物品类别</text>
      <picker 
        mode="selector" 
        :range="categoryList" 
        v-model="selectedCategory"
        @change="onCategoryChange"
      >
        <view class="picker">
          {{ form.recycleType || "请选择回收类型" }}
        </view>
      </picker>
    </view>

    <view class="form-item">
      <text>联系人</text>
      <input v-model="form.contact" placeholder="请输入联系人姓名" />
    </view>

    <view class="form-item">
      <text>联系方式</text>
      <input v-model="form.phone" placeholder="请输入手机号" />
    </view>

    <view class="form-item">
      <text>上门地址</text>
      <input v-model="form.address" placeholder="请输入详细地址" />
    </view>

    <view class="form-item">
      <text>上门时间</text>
      <picker 
        mode="time" 
        @change="onTimeChange"
      >
        <view class="picker">
          {{ form.pickupTime || "请选择时间" }}
        </view>
      </picker>
    </view>

    <view class="form-item">
      <text>物品重量(kg)</text>
      <input v-model.number="form.weight" type="number" placeholder="请输入预估重量" />
    </view>

    <!-- 新增：图片上传 -->
    <view class="form-item">
      <text>物品图片</text>
      <view class="image-uploader">
        <view class="upload-btn" @click="chooseImage" v-if="!form.coverUrl">
          <text class="plus">+</text>
          <text>添加图片</text>
        </view>
        <view class="image-preview" v-else @click="chooseImage">
          <image :src="form.coverUrl" mode="aspectFill" />
          <text class="tag">点击更换</text>
        </view>
      </view>
    </view>

    <button class="submit-btn" @click="submitOrder">立即预约</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      form: {
        recycleType: "",
        contact: "",
        phone: "",
        address: "",
        pickupTime: "",
        weight: 0,
        companyId: 0
      },
      categoryList: [],
      fullCategoryData: [], // 存储完整的分类数据以便获取单价
      selectedCategory: 0
    };
  },
  async onLoad(options) {
    if (options.companyId) {
      this.form.companyId = Number(options.companyId);
    }
    try {
      // 修改点1：数据库中分类节点名为 types
      const res = await uni.request({
        url: "http://localhost:3004/types"
      });
      
      // 修改点2：只显示子分类（parentId != 0）且匹配 name 字段
      this.fullCategoryData = res.data.filter(cat => cat.parentId != 0);
      this.categoryList = this.fullCategoryData.map(cat => cat.name);
      
      console.log("加载到的回收类型：", this.categoryList);
    } catch (e) {
      console.error("获取分类失败", e);
    }
  },
  methods: {
    // 物品类型选择
    onCategoryChange(e) {
      const index = e.detail.value;
      this.form.recycleType = this.categoryList[index];
      this.selectedCategory = index;
    },
    // 时间选择
    onTimeChange(e) {
      this.form.pickupTime = e.detail.value;
    },
    // 新增：选择图片
    chooseImage() {
      uni.chooseImage({
        count: 1,
        success: (res) => {
          this.form.coverUrl = res.tempFilePaths[0];
        }
      });
    },
    async submitOrder() {
      const user = uni.getStorageSync("user");
      if (!user) {
        uni.navigateTo({ url: "/pages/login/login" });
        return;
      }

      if (!this.form.recycleType || !this.form.address || !this.form.pickupTime) {
        uni.showToast({ title: "请填写完整信息", icon: "none" });
        return;
      }

      // 根据选择的物品获取单价
      const selectedItem = this.fullCategoryData[this.selectedCategory];
      const unitPrice = selectedItem ? selectedItem.unitPrice : 0;
      const income = this.form.weight * unitPrice;

      // 修改点3：匹配 db.json 中的 orders 结构
      // 关键修改：统一 ID 格式
      const orderData = {
        userId: user.id, // 直接使用原始 ID
        companyId: this.form.companyId,
        goodTypeName: this.form.recycleType,
        goodNumber: this.form.weight,
        goodUnitPrice: unitPrice,
        pkgs: this.form.weight.toString(),
        pickupTime: this.form.pickupTime,
        pickupAddress: this.form.address,
        contact: this.form.phone,
        createby: user.name || "用户",
        state: 0, // 0 代表待处理
        createTime: new Date().toLocaleString(), // 格式化时间
        coverUrl: this.form.coverUrl || (selectedItem ? selectedItem.icon : "")
      };

      try {
        const res = await uni.request({
          url: "http://localhost:3004/orders",
          method: "POST",
          data: orderData
        });

        if (res.statusCode === 201) {
          uni.showToast({ title: "预约成功" });
          setTimeout(() => {
            uni.switchTab({ url: "/pages/order/order" });
          }, 1000);
        } else {
          uni.showToast({ title: "预约失败", icon: "none" });
        }
      } catch (e) {
        uni.showToast({ title: "网络请求失败", icon: "none" });
      }
    }
  }
};
</script>

<style scoped>
.container {
  padding: 30rpx;
}
.form-item {
  margin-bottom: 30rpx;
}
.form-item text {
  display: block;
  font-size: 28rpx;
  margin-bottom: 10rpx;
}
input, .picker {
  border: 1rpx solid #eee;
  border-radius: 8rpx;
  padding: 20rpx;
  font-size: 28rpx;
}
.submit-btn {
  background: #3cc51f;
  color: #fff;
  border-radius: 8rpx;
  margin-top: 40rpx;
  font-size: 30rpx;
}
/* 新增样式 */
.image-uploader { margin-top: 10rpx; }
.upload-btn {
  width: 200rpx; height: 200rpx; border: 1rpx dashed #ccc; border-radius: 8rpx;
  display: flex; flex-direction: column; align-items: center; justify-content: center; color: #999;
}
.plus { font-size: 60rpx; margin-bottom: 10rpx; }
.image-preview { width: 200rpx; height: 200rpx; position: relative; }
.image-preview image { width: 100%; height: 100%; border-radius: 8rpx; }
.tag {
  position: absolute; bottom: 0; width: 100%; background: rgba(0,0,0,0.5);
  color: #fff; font-size: 20rpx; text-align: center; padding: 4rpx 0;
}
</style>