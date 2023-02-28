<template>
  <view>
    <!-- 使用自定义搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- 循环渲染轮播图 -->
      <swiper-item v-for="(item,i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <img :src="item.image_src" alt="">
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navClickHandler(item)">
        <img :src="item.image_src" alt="" class="nav-img">
      </view>
    </view>
    <!-- 楼层区域 -->
    <view class="floor-list">
      <!-- 楼层 item项 -->
      <view class="floor-item" v-for="(item,i) in floorList" :key="i">
        <!-- 楼层标题 -->
        <img :src="item.floor_title.image_src" alt="" class="floor-title">
        <!-- 楼层图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <img :src="item.product_list[0].image_src" alt="" :style="{width: item.product_list[0].image_width + 'rpx'}"
              mode="widthFix">
          </navigator>
          <!-- 右侧小图盒子 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2 !== 0"
              :url="item2.url">
              <img :src="item2.image_src" alt="" :style="{width: item2.image_width + 'rpx'}" mode="widthFix">
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //这是轮播图的数据列表
        swiperList: [],
        //分类导航的数据
        navList: [],
        //楼层数据
        floorList: [],
      };
    },
    onLoad() {
      //调用方法获取轮播图
      this.getSwiperList();
      //调用分类获取
      this.getNavList();
      //调用楼层获取
      this.getFloorList();
    },
    methods: {
      //获取轮播图数据的方法
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata');
        console.log(res);
        //请求失败
        if (res.meta.status !== 200) return uni.$showMsg();
        this.swiperList = res.message;
        uni.$showMsg('数据请求成功')
      },
      //获取分类导航数据的方法
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems');
        console.log(res);
        if (res.meta.status !== 200) return uni.$showMsg();
        this.navList = res.message;
      },
      //获取楼层数据的方法
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata');
        console.log(res);
        if (res.meta.status !== 200) return uni.$showMsg();

        //通过双层 forEach 循环，处理 URL 地址
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message;
      },

      //nav-item项被点击时候的处理函数
      navClickHandler(item) {
        //判断点击的是哪个nav
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },

      //进入搜索页面
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }
  
  swiper {
    height: 330rpx;

    .swiper-item,
    img {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 128rpx;
      height: 148rpx;
    }
  }

  .floor-title {
    height: 60rpx;
    width: 100%;
    display: flex;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;

    .right-img-box {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;

    }
  }
</style>
