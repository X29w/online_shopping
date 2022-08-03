<template>
  <view class="cart-container" v-if="cart.length !== 0">
    <my-address></my-address>
    <!-- 购物车商品列表的标题区域 -->
    <view class="cart-title">
      <!-- 左侧的图标 -->
      <uni-icons type="shop" size="18"></uni-icons>
      <!-- 描述文本 -->
      <text class="cart-title-text">您喜欢的东西我都帮您整理好了</text>
    </view>
    
    <!-- 商品列表区域 -->
<uni-swipe-action>
  <block v-for="(goods, i) in cart" :key="i">
    <uni-swipe-action-item :right-options="options" @click="swipeActionClickHandler(goods)">
      <goods :goods="goods" :show-radio="true" :show-num="true" @radio-change="radioChangeHandler" @num-change="numberChangeHandler"></goods>
    </uni-swipe-action-item>
    
  </block>
</uni-swipe-action>



  <settle></settle>

  </view>
  
  
  <!-- 空白购物车区域 -->
   <view class="empty-cart" v-else>
      <image src="/static/signal/数据正确.png" class="empty-img"></image>
      <text class="tip-text">这里有待探索呀</text> 
    </view>
</template>

<script>
  import { mapGetters } from 'vuex'
  import badgeMix from '@/mixins/tabbar-badge.js'
  import { mapState,mapMutations } from 'vuex'

  export default {
    mixins: [badgeMix],
    computed: {
      // 将 m_cart 模块中的 total 映射为当前页面的计算属性
      ...mapGetters('m_cart', ['total']),
      ...mapState('m_cart', ['cart']),
    },
    
    
    onShow() {
       // 在页面刚展示的时候，设置数字徽标
       this.setBadge()
    },


    data() {
      return {
        options: [{
              text: 'Delete', // 显示的文本内容
              style: {
                // backgroundColor: '#5A90BE' // 按钮的背景颜色
                backgroundColor: '#8a8a8a'
              }
            }]
      }
    },

    methods: {
        ...mapMutations('m_cart', ['updateGoodsState','updateGoodsCount','removeGoodsById']),
        setBadge() {
            // 调用 uni.setTabBarBadge() 方法，为购物车设置右上角的徽标
            uni.setTabBarBadge({
               index: 2, // 索引
               text: this.total + '' // 注意：text 的值必须是字符串，不能是数字
            })
         },
         

         
         radioChangeHandler(e){
           this.updateGoodsState(e)
         },
         
         
         
         numberChangeHandler(e) {
           this.updateGoodsCount(e)
         },
         
         
         swipeActionClickHandler(goods) {
            this.removeGoodsById(goods.goods_id)
         }
    },
    

  }
</script>

<style lang="scss">
.cart-title {
  height: 40px;
  display: flex;
  align-items: center;
  font-size: 14px;
  padding-left: 5px;
  border-bottom: 1px solid #efefef;
  
  .cart-title-text {
    color: #1296bd;
    margin-left: 10px;
  }
}

.cart-container {
  padding-bottom: 50px;
}

.empty-cart {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 150px;

  .empty-img {
    width: 90px;
    height: 90px;
  }

  .tip-text {
    font-size: 12px;
    color: gray;
    margin-top: 15px;
  }
}
</style>
