<template>
  <view>
    <view class="search-box">
      <uni-search-bar class="uni-mt-10" radius="5" placeholder="sir,我能做点什么" clearButton="auto" cancelButton="none" @confirm="search" @input="inputsth" />
    </view>
    
    <!-- 搜索建议列表 -->
    <view class="suggest-list" v-if="searchResults.length !==0">
      <view class="suggest-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{ item.goods_name }}</view>
        <uni-icons type="right" size="16"></uni-icons>
      </view>
    </view>
    
    
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="20" @click="clearHistory"></uni-icons>
      </view>
      
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        timer:null,
        kw:'',
        searchResults:[],
        historyList:[],
      };
    },
    
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods:{
      inputsth(e){
        
        clearTimeout(this.timer)
        this.timer = setTimeout(()=>{
          this.kw = e
          this.getSearchList()
        },500)
      },
      
      async getSearchList(){
        if(this.kw.length === 0 ){
          this.searchResults = []
          return 
        }
        
        const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
        if(res.meta.status !==200) return uni.$showMsg()
        this.searchResults = res.message
        
        
        
        this.saveSearchHistory()
      },
      
      gotoDetail(item){
        uni.navigateTo({
          url:"/subpkg/goods_detail/goods_detail?goods_id"+item.goods_id
        })
      },
      
      saveSearchHistory(){
        // this.historyList.push(this.kw)
        const set = new Set(this.historyList)
        set.delete(this.kw)
        set.add(this.kw)
        this.historyList = Array.from(set)
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      
      
      clearHistory(){
        this.historyList = []
        uni.setStorageSync('kw','[]')
      },
      
      gotoGoodsList(kw){
        uni.navigateTo({
          url:"/subpkg/goods_list/goods_list?query="+kw
        })
      }
    },
    
    
    computed:{
      histories(){
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
.search-box{
  position: sticky;
  top: 0;
  z-index: 999;
  }
   .suggest-list{
     padding: 0 5px;
     .suggest-item{
       display: flex;
       align-items: center;
       justify-content: space-between;
       font-size: 12px;
       padding: 13px 0;
       border-bottom: 1px solid #efefef;
       .goods-name{
         white-space: nowrap;
         overflow: hidden;
         text-overflow: ellipsis;
       }
     }
  
}


.history-box{
  padding: 0 5px;
  .history-title{
    display: flex;
    justify-content: space-between;
    height: 40px;
    align-items: center;
    font-size: 13px;
    border-bottom: 1px solid #efefef;
  }
  .history-list{
    display: flex;
    flex-wrap: wrap;
    
    .uni-tag{
      margin-top: 5px;
      margin-right: 5px;
      background-color: #fff;
      color: #1296db;
      border: 1px solid #1296db;
    }
  }
}
</style>
