<template>
  <view class="login-container">
    <!-- 提示登录的图标 -->
    <uni-icons type="contact-filled" size="100" color="#8a8a8a"></uni-icons>
    <!-- 登录按钮 -->
    <button type="primary" class="btn-login" open-type="getUserInfo" @click="getUserProfile">登录</button>
    <!-- 登录提示 -->
    <view class="tips-text">登录之后我才能更好的服务您</view>
  </view>
</template>

<script>
  // 1. 按需导入 mapMutations 辅助函数
  import { mapMutations, mapState } from 'vuex'
  
  
  export default {
    
      computed: {
        // 调用 mapState 辅助方法，把 m_user 模块中的数据映射到当前用组件中使用
        ...mapState('m_user', ['redirectInfo']),
      },
      methods: {
        // 调用 mapMutations 辅助方法，把 m_user 模块中的方法映射到当前组件中使用
        ...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),
      },
      
    name:"login",
    data() {
      return {
        
      };
    },
    
    
    
    methods:{
          // 2. 调用 mapMutations 辅助方法，把 m_user 模块中的 updateUserInfo 映射到当前组件中使用
          ...mapMutations('m_user', ['updateUserInfo', 'updateToken']),
          
          
      // // 获取微信用户的基本信息
      //   getUserInfo(e) {
      //     // 判断是否获取用户信息成功
      //     if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登录授权！')
      
      //     // 获取用户信息成功， e.detail.userInfo 就是用户的基本信息
      //      // 3. 将用户的基本信息存储到 vuex 中
      //           this.updateUserInfo(e.detail.userInfo)
      //     // 获取登录成功后的 Token 字符串
      //       this.getToken(e.detail)
      //   },
        
        async getUserProfile(e){
          const res = await uni.getUserProfile({
            desc:"登录",
            success:res=>{},
            fail:res=>{return}
          })
          
          // 判断是否获取用户信息成功
          if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登录授权！')
          console.log(e.detail.userInfo);
                
          // 获取用户信息成功， e.detail.userInfo 就是用户的基本信息
           // 3. 将用户的基本信息存储到 vuex 中
                this.updateUserInfo(e.detail.userInfo)
          // 获取登录成功后的 Token 字符串
            this.getToken(e.detail)
        },
        
        
        
        // 调用登录接口，换取永久的 token
        async getToken(info) {
          // 调用微信登录接口
          const [err, res] = await uni.login().catch(err => err)
          // 判断是否 uni.login() 调用失败
          if (err || res.errMsg !== 'login:ok') return uni.$showError('登录失败！')
        
          // 准备参数对象
          const query = {
            code: res.code,
            encryptedData: info.encryptedData,
            iv: info.iv,
            rawData: info.rawData,
            signature: info.signature
          }
        
          // 换取 token
          const { data: loginResult } = await uni.$http.post('/api/public/v1/users/wxlogin', query)
          if (loginResult.meta.status !== 200) return uni.$showMsg('登录失败！')
          // 2. 更新 vuex 中的 token
              this.updateToken(loginResult.message.token)
              this.navigateBack()
        },
        
        
        
        
        // 返回登录之前的页面
        navigateBack() {
          // redirectInfo 不为 null，并且导航方式为 switchTab
          if (this.redirectInfo && this.redirectInfo.openType === 'switchTab') {
            // 调用小程序提供的 uni.switchTab() API 进行页面的导航
            uni.switchTab({
              // 要导航到的页面地址
              url: this.redirectInfo.from,
              // 导航成功之后，把 vuex 中的 redirectInfo 对象重置为 null
              complete: () => {
                this.updateRedirectInfo(null)
              }
            })
          }
        },
    }
  }
</script>

<style lang="scss">
.login-container {
  // 登录盒子的样式
  height: 750rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #f8f8f8;
  position: relative;
  overflow: hidden;

  // 绘制登录盒子底部的半椭圆造型
  &::after {
    content: ' ';
    display: block;
    position: absolute;
    width: 100%;
    height: 40px;
    left: 0;
    bottom: 0;
    background-color: white;
    border-radius: 100%;
    transform: translateY(50%);
  }

  // 登录按钮的样式
  .btn-login {
    width: 90%;
    border-radius: 100px;
    margin: 15px 0;
    background-color: #1296bd;

    background-color: #1296bd;
    	color: aquamarine;
    	/* 加发光效果和下面的倒影 模糊度加到50px */
    	box-shadow: 0 0 50px #1296bd;
    	-webkit-box-reflect: below 1px 
    	linera-gradient(transparent,rgba(0,0,0,.3));
    	/* below是下倒影 1px是倒影的元素相隔的距离  最后时渐变的颜色*/
    	transition-delay: 0.4s;
  }

  // 按钮下方提示消息的样式
  .tips-text {
    font-size: 12px;
    color: gray;
  }
}
</style>