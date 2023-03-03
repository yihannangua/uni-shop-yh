<template>
  <view class="goods-item">
    <!-- 左侧商品大图 -->
    <view class="goods-item-left">
      <img :src="item.goods_small_logo || defaultPic" alt="" class="goods-pic">
    </view>
    <!-- 右侧商品信息 -->
    <view class="goods-item-right">
      <!-- 商品标题 -->
      <view class="goods-title">
        {{item.goods_name}}
      </view>
      <!-- 商品价格 -->
      <view class="goods-price">
        ￥{{item.goods_price | toFixed}}
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name: "my-goods",
    props: {
      item: {
        type: Object,
        default: {}
      }
    },
    data() {
      return {
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png',
        }
    },
    filters: {
      toFixed(num) {
        return Number(num).toFixed(2)
      }
    }
  }
</script>

<style lang="scss">
.goods-item {
      display: flex;
      justify-content: space-between;
      background-color: #fff;
      margin-bottom: 5px;

      .goods-item-left {
        .goods-pic {
          width: 100px;
          height: 100px;
        }
      }

      .goods-item-right {
        display: flex;
        flex-direction: column;
        justify-content: space-between;

        .goods-title {
          padding: 5px;
          font-size: 12px;
        }

        .goods-price {
          padding: 5px;
          color: #c00000;
        }
      }
    }
</style>
