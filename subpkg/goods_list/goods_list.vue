<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)"><my-goods :goods="goods"></my-goods></view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 节流阀
      isloading: false,
      // 商品列表数据
      goodsList: [],
      // 总数量
      total: 0,
      // 查询参数对象
      queryObj: {
        // 查询关键词
        query: '',
        // 商品分类id
        cid: '',
        // 页码值
        pagenum: 1,
        // 每页显示多少条数据
        pagesize: 10
      }
    };
  },
  onLoad(options) {
    // console.log(options);
    this.queryObj.query = options.query || '';
    this.queryObj.cid = options.cid || '';
    this.getGoodsList();
  },
  onReachBottom() {
    // 判断数据是否还有下一页的数据
    if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！');

    // 节流
    if (this.isloading) return;

    this.queryObj.pagenum += 1;
    this.getGoodsList();
  },
  onPullDownRefresh() {
    this.queryObj.pagenum = 1;
    this.total = 0;
    this.isloading = false;
    this.goodsList = [];

    this.getGoodsList(() => uni.stopPullDownRefresh());
  },
  methods: {
    gotoDetail(item) {
      uni.navigateTo({
        url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
      });
    },
    async getGoodsList(cb) {
      // 打开节流阀
      this.isloading = true;

      // 发起请求
      const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj);

      // 关闭节流阀
      this.isloading = false;

      cb && cb();

      // 响应处理
      if (res.meta.status !== 200) return uni.$showMsg();
      this.goodsList = [...this.goodsList, ...res.message.goods];
      // this.goodsList = res.message.goods
      this.total = res.message.total;
    }
  }
};
</script>

<style lang="scss"></style>
