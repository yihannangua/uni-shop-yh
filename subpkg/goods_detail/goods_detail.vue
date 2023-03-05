<template>
  <view v-if="goods_info.goods_name" class="goods-container">
    <!-- 轮播图 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
      <swiper-item v-for="(item,i) in goods_info.pics" :key="i">
        <img :src="item.pics_big" alt="" @click="preview(i)">
      </swiper-item>
    </swiper>
    <!-- 商品信息区域 -->
    <view class="goods-info-box">
      <!-- 商品价格 -->
      <view class="price">
        ￥{{goods_info.goods_price}}
      </view>
      <!-- 商品信息主体 -->
      <view class="goods-info-body">
        <!-- 商品名称 -->
        <view class="goods-name">
          {{goods_info.goods_name}}
        </view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="24" color="gray"></uni-icons>
          <text style="color: gray; font-size: 12px;">收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">
        快递：免运费
      </view>
    </view>
    <!-- 商品图文区域 -->
    <rich-text :nodes="goods_info.goods_introduce"></rich-text>
    <!-- 商品导航组件区域 -->
    <view class="goods_nav">
      <!-- fill 控制右侧按钮样式 -->
      <!-- options 左侧按钮的配置项 -->
      <!-- buttonGroup 右侧按钮的配置项 -->
      <!-- click 左侧按钮的点击事件处理函数 -->
      <!-- buttonClick 右侧按钮的点击事件处理函数 -->
      <uni-goods-nav :options="options" :fill="true" :button-group="buttonGroup" @click="onClick"
        @buttonClick="buttonClick" />
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //商品详情数据
        goods_info: {},
        //左侧按钮组的配置对象
        options: [{
          icon: 'shop',
          text: '店铺',
          infoBackgroundColor: '#007aff',
          infoColor: "red"
        }, {
          icon: 'cart',
          text: '购物车',
          info: 11
        }],
        buttonGroup: [{
            text: '加入购物车',
            backgroundColor: '#ff0000',
            color: '#fff'
          },
          {
            text: '立即购买',
            backgroundColor: '#ffa200',
            color: '#fff'
          }
        ]
      };
    },
    onLoad(option) {
      //获取商品id
      const goods_id = option.goods_id
      //调用获取商品详情数据的方法
      this.getGoodsDetail(goods_id);
    },
    methods: {
      //获取商品详情数据
      async getGoodsDetail(goods_id) {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/detail', {
          goods_id
        });
        console.log(res);
        if (res.meta.status !== 200) return uni.$showMsg();

        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
          .replace(/webp /g, 'jpg')

        this.goods_info = res.message;
        uni.$showMsg('数据请求成功')
      },
      //实现轮播图的预览效果
      preview(i) {
        //调用uni.previewImage()方法预览图片
        uni.previewImage({
          //预览时，默认显示图片的索引
          current: i,
          //所有图片url地址的数组
          urls: this.goods_info.pics.map(x => x.pics_big)
        })
      },

      onClick(e) {
        // console.log(e);
        if (e.content.text === '购物车') {
          //切换到购物车页面
          uni.switchTab({
            url: '/pages/cart/cart'
          })
        }
      }
    }
  }
</script>

<style lang="scss">
  .goods-container {
    padding-bottom: 50px;
  }

  swiper {
    height: 750rpx;

    img {
      width: 100%;
      height: 100%;
    }
  }

  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    .price {
      font-size: 20px;
      color: #C00000;
      margin: 10px 0;
    }

    .goods-info-body {
      display: flex;

      .goods-name {
        width: 85%;
        font-size: 16px;
        border-right: 1px solid #efefef;
        text-overflow: -o-ellipsis-lastline;
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        line-clamp: 2;
        -webkit-box-orient: vertical;
      }

      .favi {
        flex: 1;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
      }
    }

    .yf {
      padding: 5px 0;
      color: gray;
      font-size: 14px;
    }

  }

  .goods_nav {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }
</style>
