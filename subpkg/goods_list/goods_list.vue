<template>
  <view class="goods-list">
    <!-- 商品列表项 -->
    <view v-for="(item,i) in goodsList" :key="i" @click="gotoDetail(item)">
      <my-goods :item="item"></my-goods>
    </view>
  </view>
</template>
<script>
  export default {
    data() {
      return {
        //请求参数对象
        queryObj: {
          query: '',
          cid: '',
          pagenum: 1,
          pagesize: 10
        },
        //商品列表数据
        goodsList: [],
        //总数量，实现分页
        total: 0,
        //是否正在请求数据
        isloading: false
      };
    },
    onLoad(options) {
      //为请求参数赋值
      this.queryObj.query = options.query || '';
      this.queryObj.cid = options.cid || '';
      // console.log(this.queryObj);
      //调用获取商品列表数据的方法
      this.getGoodsList();
    },
    methods: {
      //获取商品列表数据的方法
      async getGoodsList(cb) {
        //打开节流阀
        this.isloading = true;
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj);
        //关闭节流阀
        this.isloading = false;
        // console.log(res);
        // console.log(res.message);
        cb && cb();
        if (res.meta.status !== 200) return uni.$showMsg();
        this.goodsList = [...this.goodsList, ...res.message.goods];
        this.total = res.message.total;
      },
      //点击跳转到商品详情页面
      gotoDetail(item) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      }
    },
    //检测触底
    onReachBottom() {
      //判断是否还有剩余数据
      if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return
      //判断是否正在请求其他数据，如果是，则不发起额外请求
      if (this.isloading) return
      //让页码值自加1
      this.queryObj.pagenum++
      this.getGoodsList()
    },
    //检测下拉刷新
    onPullDownRefresh() {
      //重置关键数据
      this.queryObj.pagenum = 1;
      this.total = 0;
      this.isloading = false;
      this.goodsList = [];
      //重新发起数据请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    } 
  }
</script>

<style lang="scss">

</style>
