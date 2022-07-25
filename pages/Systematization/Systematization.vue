<template>
  <view>
    <view class="scroll-view-container">
      
      
      <!-- 左滑动区 -->
      <scroll-view scroll-y="true" :style="{height:wh+'px'}" class="left-scroll-view" :scroll-top="scrollTop">
      <block v-for="(item,i) in systematizationList" :key="i">
        <view :class="['left-scroll-view-item',i === active? 'active' : '']" @click="activeChange(i)">{{ item.cat_name }}</view>
      </block>
        
      </scroll-view>
      <!-- 右滑动区 -->
      <scroll-view scroll-y="true" :style="{height:wh+'px'}" class="right-scroll-view">
        <view class="systematization-lv2" v-for="(item2,i2) in systematizationLevel2" :key="i2">
          
          <!-- 二级分类的标题 -->
          <view class="systematization-lv2-title">/ {{ item2.cat_name }} /</view>
          <!-- 当前二级下的三级分类 -->
          <view class="systematization-lv3-list">
            <view class="systematization-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <image :src="item3.cat_icon.replace('dev','web')" mode=""></image>
              <text>{{ item3.cat_name }}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        wh:0,
        systematizationList:[],
        active:0,
        systematizationLevel2:[],
        scrollTop:0,
      }
    },
    
    onLoad(){
      const sysInfo = uni.getSystemInfoSync()
      this.wh = sysInfo.windowHeight
      
      this.getsystematizationList()
    },
    methods: {
      async getsystematizationList(){
        const {data:res} = await uni.$http.get('/api/public/v1/categories')
        
        if(res.meta.status !==200) return uni.$showMsg()
        this.systematizationList = res.message
        
        
        // 二级分类
        this.systematizationLevel2 = res.message[0].children
      },
      
      activeChange(i){
        this.active = i
        
        this.systematizationLevel2 = this.systematizationList[i].children
        this.scrollTop = this.scrollTop === 0 ? 1:0
      },
      
      // 跳转到商品
      gotoGoodsList(item){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid='+ item.cat_id
        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container{
    display: flex;
    
    .left-scroll-view{
      width: 120px;
      
      .left-scroll-view-item{
        background-color: #8a8a8a;
        line-height: 60px;
        text-align: center;
        font-size: 12px;
        color: #e6e6e6;
        
        &.active{
          position: relative;
          background-color: #FAE377;
          color: #101603;
          
          &::before{
            content: '';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #8a8a8a;
            position: absolute;
            top: 25%;
            left: 0;
          }
        }
      }
    }   
}

.systematization-lv2-title{
  font-size: 12px;
  font-weight: bold;
  text-align: center;
  padding: 15px 0px;
}

.systematization-lv3-list{
  display: flex;
  flex-wrap: wrap;
  
  
  .systematization-lv3-item{
    width: 33.3%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-bottom: 10px;
    
    image{
      width: 60px;
      height: 60px;
    }
    
    text{
      font-size: 12px
    }
  }
}
</style>
