<template>
  <transition name="fade">
    <router-view></router-view>
  </transition>
</template>

<script>
  export default {
    mounted () {
      if (this.isMobile()) {
        // 手机登录，隐藏左侧导航栏
        console.log('手机端')
        this.$store.commit('common/updateSidebarFold', true)
      } else {
        console.log('电脑端')
      }
    },
    methods: {
      isMobile () {
        return navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i)
      }
    }
  }
</script>
<style>
body .el-table th.gutter{
  display: table-cell!important;
}
</style>
