<template>
  <view>
    <view class="search-box"><uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar></view>
    <!-- 搜索历史 -->
    <view class="history-box" v-if="searchResults.length === 0">
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
      </view>
      <view class="history-list"><uni-tag :text="item" v-for="(item, i) in historys" :key="i" @click="gotoGoodsList(item)"></uni-tag></view>
    </view>
    <!-- 搜索建议 -->
    <view class="sugg-list" v-else>
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{ item.goods_name }}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 搜索关键词
      kw: '',
      // 防抖
      timer: null,
      // 搜索建议
      searchResults: [],
      // 搜索历史
      historyList: ['a', 'f', 'rt']
    };
  },
  computed: {
    historys() {
      return [...this.historyList].reverse();
    }
  },
  onLoad() {
    this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]');
  },
  methods: {
    input(e) {
      // console.log(e.valu);
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.kw = e;
        // console.log(this.kw);
        this.getSearchList();
      }, 500);
    },
    gotoGoodsList(kw) {
      uni.navigateTo({
        url:'/subpkg/goods_list/goods_list?query='+kw
      })
    },
    cleanHistory() {
      this.historyList = [],
      uni.setStorageSync('kw','[]')
    },
    gotoDetail(goods_id) {
      uni.navigateTo({
        url: '/subpkg/goods_detail/goods_detail?id=' + goods_id
      });
    },
    saveSearchHistory() {
      // this.historyList.push(this.kw)
      const set = new Set(this.historyList);
      set.delete(this.kw);
      set.add(this.kw);
      this.historyList = Array.from(set);

      uni.setStorageSync('kw', JSON.stringify(this.historyList));
    },
    async getSearchList() {
      if (this.kw === '') {
        this.searchResults = [];
        return;
      }
      const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw });
      // console.log(res);
      // return
      if (res.meta.status !== 200) return uni.$showMsg();
      this.searchResults = res.message;
      // console.log(this.searchResults);
      this.saveSearchHistory();
    }
  }
};
</script>

<style lang="scss">
.search-box {
  position: sticky;
  top: 0;
  z-index: 999;
}
.sugg-list {
  padding: 0 5px;
  .sugg-item {
    font-size: 12px;
    padding: 13px 0;
    border-bottom: 1px solid #efefef;
    display: flex;
    align-items: center;
    justify-content: center;
    .goods-name {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      margin-right: 3px;
    }
  }
}
.history-box {
  padding: 0 5px;
  .history-title {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 13px;
    border-bottom: 1px solid #efefef;
    height: 40px;
  }
  .history-list {
    display: flex;
    flex-wrap: wrap;
    .uni-tag {
      margin-top: 5px;
      margin-right: 5px;
    }
  }
}
</style>
