<template>
  <view class="search-container" @click="searchBoxHandler">
    <view class="search-box">
      <uni-icons type="search" size="30" color="#1296db"></uni-icons>
      <text class="placeholder" style="color: #1296db;">搜索</text>
      
    </view>
  </view>
</template>

<script>
  export default {
    name:"search",
    props:{
      bgcolor:{
        type:String,
        default:'#515151'
      },
      
      radius:{
        type:Number,
        default:18
      }
    },
    data() {
      return {
        
      };
    },
    
    methods:{
      searchBoxHandler(){
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
.search-container{
  height: 50px;
  background-color: #515151;
  display: flex;
  align-items: center;
  padding: 0 10px;
  
  
  .search-box{
    height: 36px;
    background-color: #ebeef2;
    border-radius: 18px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    
    
    .placeholder{
      font-size: 15px;
      margin-left: 5px;
    }
  }
}
</style>