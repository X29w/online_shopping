<template>
  <view class="my-container">
    <!-- 未登录 -->
    <login v-if="!token"></login>
    <!-- 登录 -->
    <userinfo v-else></userinfo>
  </view>
</template>

<script>
  
  import badgeMix from '@/mixins/tabbar-badge.js'
  // 1. 从 vuex 中按需导入 mapState 辅助函数
  import { mapState } from 'vuex'
  

  export default {
      mixins: [badgeMix],
      computed:{
         // 2. 从 m_user 模块中导入需要的 token 字符串
            ...mapState('m_user', ['token']),
      },

    data() {
      return {
        
      }
    },
    methods: {
      
    }
  }
</script>

<style lang="scss">
page,
.my-container {
  height: 100%;
}
</style>
