<template>
  <view>
       <view class="goods-item">
          
          <!-- 图片展示区 -->
          <view class="goods-item-left">
            <radio :checked="goods.goods_state" color="#FAE377" v-if="showRadio" @click="radioClickHandler"></radio>
            <image :src="goods.goods_small_logo || defaultPic" class="goods-pic" mode=""></image>
          </view>
          
          <!-- 右侧文字描述 -->
          <view class="goods-item-right">
            
            <!-- 商品标题 -->
            <view class="goods-name">{{ goods.goods_name }}</view>
            <view class="goods-info-box">
              <view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
              <uni-number-box :min="1" :value="goods.goods_count" v-if="showNum" @change="numChangeHandler"></uni-number-box>
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
        default:{},
      },
      
      showRadio: {
            type: Boolean,
            // 如果外界没有指定 show-radio 属性的值，则默认不展示 radio 组件
            default: false,
          },
          
          
      showNum: {
                type: Boolean,
                default: false,
              },
    },
    name:"goods",
    data() {
      return {
        defaultPic:'https://pic.616pic.com/ys_bnew_img/00/04/81/RWvymh6QCe.jpg'
      };
    },
    
    methods:{
      // radio 组件的点击事件处理函数
        radioClickHandler() {
          // 通过 this.$emit() 触发外界通过 @ 绑定的 radio-change 事件，
          // 同时把商品的 Id 和 勾选状态 作为参数传递给 radio-change 事件处理函数
          this.$emit('radio-change', {
            // 商品的 Id
            goods_id: this.goods.goods_id,
            // 商品最新的勾选状态
            goods_state: !this.goods.goods_state
          })
        },
        
        
        
         // NumberBox 组件的 change 事件处理函数
          numChangeHandler(val) {
            // 通过 this.$emit() 触发外界通过 @ 绑定的 num-change 事件
            this.$emit('num-change', {
              // 商品的 Id
              goods_id: this.goods.goods_id,
              // 商品的最新数量
              goods_count: +val
            })
          },
    }
  }
</script>

<style lang="scss">
.goods-item{
  width: 750rpx;
    // 设置盒模型为 border-box
  box-sizing: border-box;
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;
  
  .goods-item-left{
    margin-right: 5px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    
    
    .goods-pic{
      width: 100px;
      height: 100px;
      display: block;
    }
  }
  
  
  .goods-item-right{
    display: flex;
    flex: 1;
    flex-direction: column;
    justify-content: space-between;
    
    
    .goods-name{
      font-size: 12px;
    }
    
    .goods-info-box{
          display: flex;
          align-items: center;
          justify-content: space-between;
          }
    
    .goods-price{
        color: #C00000;
        font-size: 16px;
      }
    }
  
}
</style>