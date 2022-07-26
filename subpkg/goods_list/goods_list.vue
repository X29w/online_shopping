<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
      <goods :goods="goods"></goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj:{
          query:'',
          cid:'',
          pagenum:1,
          pagesize:10
        },
        
        goodsList:[],
        total:0,
        isloading:false
        
      };
    },
    
    
    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.getGoodList()
    },
    
    
    methods:{
      async getGoodList(cb){
        this.isloading = true
        const {data:res} = await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
        this.isloading = false
        
        cb && cb()
        
        if(res.meta.status !== 200) return uni.$showMsg()
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
      },
      
      
      gotoDetail(item){
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id'+item.goods_id
        })
      }
    },
    
    
    onReachBottom() {
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('sir，我知道的已经全部告诉您了')
      if(this.isloading) return
      this.queryObj.pagenum += 1
      this.getGoodList()
    },
    
    
    onPullDownRefresh(){
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []
      
      this.getGoodList(()=>uni.stopPullDownRefresh())
    },
    
    

  }
</script>

<style lang="scss">

</style>
