<template>
  <view>
       <view class="goods-item">
          
          <!-- 图片展示区 -->
          <view class="goods-item-left">
            <image :src="goods.goods_small_logo || defaultPic" class="goods-pic" mode=""></image>
          </view>
          
          <!-- 右侧文字描述 -->
          <view class="goods-item-right">
            
            <!-- 商品标题 -->
            <view class="goods-name">{{ goods.goods_name }}</view>
            <view class="goods-info-box">
              <view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
            </view>
          </view>
        </view>
  </view>
</template>

<script>
  export default {
    filters:{
      tofixed(num){
        return Number(num).toFixed(2)
      }
    },
    props:{
      goods:{
        type:Object,
        default:{}
      }
    },
    name:"goods",
    data() {
      return {
        defaultPic:'https://pic.616pic.com/ys_bnew_img/00/04/81/RWvymh6QCe.jpg'
      };
    }
  }
</script>

<style lang="scss">
.goods-item{
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;
  
  .goods-item-left{
    margin-right: 5px;
    
    
    .goods-pic{
      width: 100px;
      height: 100px;
      display: block;
    }
  }
  
  
  .goods-item-right{
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    .goods-name{
      font-size: 12px;
    }
    
    .goods-info-box{
      .goods-price{
        color: #C00000;
        font-size: 16px;
      }
    }
  }
}
</style>