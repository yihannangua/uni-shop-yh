<template>
  <view>
    <view class="my-search-container" :style="{ 'background-color': bgcolor}" @click="searchBoxHandler">
      <view class="my-search-box" :style="{ 'border-radius': radius + 'px'}">
        <!-- 使用uni-ui 提供的图表组件 -->
        <uni-icons type="search" size="17"></uni-icons>
        <text class="placeholder">搜索</text>
      </view>
    </view>
  </view>
</template>

<script> 
  export default {
    name:"my-search",
    props: {
      //背景颜色
        bgcolor: {
           type: String,
           default: '#c00000'
        },
      //圆角尺寸
        radius: {
          type: Number,
          default: 18 //px
        }
    },
    data() {
      return {
        
      };
    },
    methods: {
      searchBoxHandler() {
        this.$emit('click'); 
      }
    }
  }
</script>

<style lang="scss">
  .my-search-container {
    height: 50px;
    // background-color: #c00000;
    display: flex;
    align-items: center;
    .my-search-box {
      width: 100%;
      height: 36px;
      background-color: #fff;
      margin: 0 10px ;
      padding-left:10px ;
      // border-radius:18px ;
      .placeholder {
        padding-left: 5px;
        line-height: 36px;
      }
    }
  }
</style>