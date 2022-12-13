<template>
  <view>
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <view class="scroll-view-container">
      <!-- 左 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{ height: wh + 'px' }">
        <block v-for="(item, i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChange(i)">{{ item.cat_name }}</view>
        </block>
      </scroll-view>
      <!-- 右 -->
      <scroll-view class="right-scroll-view" scroll-y :style="{ height: wh + 'px' }" :scroll-top="scrollTop">
        <view class="right-scroll-view-item" v-for="(item2, i2) in cateLevel2">
          <view class="cate-lv2-title">/ {{ item2.cat_name }} /</view>
          <!-- 三级分类 -->
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- <image :src="item3.cat_icon"></image> -->
              <image></image>
              <text>{{ item3.cat_name }}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 窗口可用高度
      wh: 0,
      // 分类数据列表
      cateList: [],
      // 选中项
      active: 0,
      // 二级分类列表
      cateLevel2: [],
      // 滚动条
      scrollTop: 0
    };
  },
  onLoad() {
    const sysInfo = uni.getSystemInfoSync();
    this.wh = sysInfo.windowHeight-50;
    this.getCateList();
  },
  methods: {
    async getCateList() {
      const { data: res } = await uni.$http.get('/api/public/v1/categories');
      if (res.meta.status !== 200) return uni.$showMsg();
      this.cateList = res.message;
      this.cateLevel2 = res.message[0].children;
    },
    activeChange(i) {
      this.active = i;
      this.cateLevel2 = this.cateList[i].children;
      this.scrollTop = this.screenTop ? 0 : 1;
    },
    gotoGoodsList(item3) {
      uni.navigateTo({
        url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
      });
    },
    gotoSearch() {
      uni.navigateTo({
        url:'/subpkg/search/search'
      })
    }
  }
};
</script>

<style lang="scss">
.scroll-view-container {
  display: flex;
  .left-scroll-view {
    width: 120px;
    .left-scroll-view-item {
      line-height: 60px;
      background-color: #f7f7f7;
      text-align: center;
      font-size: 12px;
      &.active {
        background-color: #fff;
        position: relative;
        &::before {
          content: '';
          display: block;
          background-color: #c00000;
          width: 3px;
          height: 30px;
          position: absolute;
          top: 50%;
          left: 0;
          transform: translateY(-50%);
        }
      }
    }
  }
}
.cate-lv2-title {
  font-size: 12px;
  font-weight: bold;
  text-align: center;
  padding: 15px 0;
}
.cate-lv3-list {
  display: flex;
  flex-wrap: wrap;
  .cate-lv3-item {
    width: 33.3%;
    margin-bottom: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
    image {
      width: 60px;
      height: 60px;
    }
    text {
      font-size: 12px;
    }
  }
}
.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}
</style>
