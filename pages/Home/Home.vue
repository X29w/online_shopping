<template>
  <view>
    <!-- 搜索 -->
    <view class="search-box">
      <search @click="clicktoSearch"></search>
    </view>
    
    <!-- 轮播图 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="( item , i ) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src" ></image>
        </navigator>
      </swiper-item>
    </swiper>
    
    <!-- 分类导航 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item , i) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" mode="" class="nav-img"></image>
      </view>
    </view>
    
    <!-- 楼层 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item , i) in floorList" :key="i">
        
        <!-- 楼层标题 -->
        <image :src="item.floor_title.image_src" mode="" class="floor-title"></image>
        
        <!-- 楼层图片 -->
        <view class="floor-img-box">
          <!-- 左侧 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          
          
          <!-- 右侧 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="( item2 , i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width:item2.image_width + 'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
  
  
</template>

<script>
  import badgeMix from '@/mixins/tabbar-badge.js'

  export default {
      mixins: [badgeMix],

    data() {
      return {
        // 轮播图
          swiperList:[],
          // 分类导航
          navList:[],
          // 楼层
          floorList:[],
      };
    },
    
    onLoad(options){
       this.getSwiperList(options)
       this.getNavList(options)
       this.getFloorList(options)
    },
    methods:{
      async getSwiperList(options){
        const {data:res} = await uni.$http.get('/api/public/v1/home/swiperdata')
        if(res.meta.status !== 200)
        {
          return uni.$showMsg()
        }
        
        this.swiperList = res.message
        uni.$showMsg('很乐意为您效劳')
      },
      
      async getNavList(options){
        const {data:res} = await uni.$http.get('/api/public/v1/home/catitems')
        if( res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
      },
      
      navClickHandler(item){
        if(item.name == '分类'){
          uni.switchTab({
            url:'/pages/Systematization/Systematization'
          })
        }
      },
      
      async getFloorList(options){
        const { data:res } = await uni.$http.get('/api/public/v1/home/floordata')
        if(res.meta.status !== 200) return uni.$showMsg()
        
        res.message.forEach(floor =>{
          floor.product_list.forEach(prod =>{
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
      },
      
      
      clicktoSearch(){
        uni.navigateTo({
          url:"/subpkg/search/search"
        })
      }
    }
  }
</script>

<style lang="scss">
swiper{
  height: 330rpx;
  
  .swiper-item,
  image{
    width: 100%;
    height: 100%;
  }
  navigator{
    height: 100%;
    width: 100%;
  }
}
  .nav-list{
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;
    
    .nav-img{
      width: 128rpx;
      height: 140rpx;
    }
  }
  
  .floor-title{
    width: 100%;
    height: 60rpx;
  }
  
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  .floor-img-box{
    display: flex;
    padding: 10rpx;
    
  }
  
  .search-box{
    position: sticky;
    top: 0;
    z-index: 999;
  }
</style>
