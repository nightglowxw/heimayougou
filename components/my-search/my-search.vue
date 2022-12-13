<template>
  <view>
    <view class="my-search-container" :style="{ 'background-color': bgcolor }" @click="searchBoxHandler">
      <view class="my-search-box" :style="{ 'border-radius': radius+'px' }">
        <uni-icons type="search" size="17"></uni-icons>
        <text class="placeholder">搜索</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'my-search',
  props: {
    bgcolor: {
      type: String,
      default: '#c00000'
    },
    radius: {
      type: Number,
      default: 18
    }
  },
  data() {
    return {};
  },
  methods: {
    searchBoxHandler() {
      this.$emit('click')
    } 
  }
};
</script>

<style lang="scss">
.my-search-container {
  // background-color: #c00000;
  height: 50px;
  padding: 0 10px;
  display: flex;
  align-items: center;
  .my-search-box {
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #fff;
    height: 36px;
    // border-radius: 15px;
    width: 100%;
    .placeholder {
      font-size: 15px;
      margin-left: 5px;
    }
  }
}
</style>
