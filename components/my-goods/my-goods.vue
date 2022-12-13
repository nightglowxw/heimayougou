<template>
  <view>
    <view class="goods-item">
      <view class="goods-item-left"><image :src="goods.goods_small_logo || '../../static/logo.png'" class="goods-pic"></image></view>
      <view class="goods-item-right">
        <view class="goods-name">{{ goods.goods_name }}</view>
        <view class="goods-info-box">
          <view class="goods-price">¥{{ goods.goods_price | tofixed }}</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'my-goods',
  props: {
    goods: {
      type: Object,
      defaul: {}
    }
  },
  filters: {
    tofixed(num) {
      return Number(num).toFixed(2);
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss">
.goods-item {
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;
  .goods-item-right {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    .goods-name {
      font-size: 13px;
    }
    .goods-price {
      font-size: 16px;
      color: #c00000;
    }
  }
  .goods-item-left {
    margin-right: 5px;
    .goods-pic {
      width: 100px;
      height: 100px;
      display: block;
    }
  }
}
</style>
