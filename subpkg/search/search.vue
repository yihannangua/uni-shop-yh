<template>
  <view>
    <!-- 搜索输入框 -->
    <view class="search-box">
      <uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>
    </view>
    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResult.length !== 0">
      <!-- 搜索建议项 -->
      <view class="sugg-item" v-for="(item,i) in searchResult" :key="i" @click="gotoDetail(item)">
        <!-- 建议项内容 -->
        <view class="goods-name">
          {{item.goods_name}}
        </view>
        <!-- 建议项箭头 -->
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="20" @click="clearHistory"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in histories" :key="i" @click="gotoGoodsList(item)">
        </uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //延时器
        timer: null,
        //搜索关键词
        kw: '',
        //搜索结果列表
        searchResult: [],
        //搜索关键词的历史记录
        historyList: ['a','app']
      };
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw')|| '[]');
    },
    methods: {
      //input输入事件的函数
      input(e) {
        clearTimeout((this.timer))
        this.timer = setTimeout(() => {
          // console.log(e);
          this.kw = e;
          this.getSearchList();
        }, 500)
      },
      //获取搜索结果列表
      async getSearchList() {
        //判断搜索关键词是否为空
        if (this.kw.length === 0) {
          this.searchResult = [];
          return
        }
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResult = res.message;
        //查询结果不为空，才保存搜索关键词
        if (this.searchResult.length === 0) return
        this.saveSearchHistory();
      },
      //跳转商品详情页
      gotoDetail(item) {
        // console.log(item.goods_id);
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      //将搜索关键词存入历史记录
      saveSearchHistory() {
        // this.historyList.push(this.kw);
        //将Array数组转化为set对象
        const set = new Set(this.historyList);
        //调用 Set 对象的 delete 方法，移除对应的元素,为了保证新输入的位置在前
        set.delete(this.kw);
        // //调用 Set 对象的 add 方法，向 Set 中添加元素
        set.add(this.kw);
        console.log(set);
        // //将 Set 对象转化为 Array 数组
        this.historyList = Array   .from(set); 
        //调用 uni.setStorageSync(key,value) 将历史记录持久化存储到本地
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      //清空搜索历史记录
      clearHistory() {
        this.historyList = [];
        uni.setStorageSync('kw','[]');
      },
      //打开商品列表
      gotoGoodsList(item) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?query=' + this.kw 
        })
      }
    },
    //定义计算属性
    computed: {
      histories() {
        return [...this.historyList].reverse() 
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

  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;

      .goods-name {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
    }
  }

  .history-box {
    padding: 0 5px;

    .history-title {
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;

      .uni-tag {
        margin-top: 5px;
        margin-right: 5px;
        background-color: #efefef;
        border: none;
        color: black;
      }
    }


  }
</style>
