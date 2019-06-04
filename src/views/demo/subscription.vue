<template>
  <el-container>
    <el-main>
      <el-tabs tab-position="left" value="3">
        <el-tab-pane label="个人基本信息" name="1">个人基本信息</el-tab-pane>
        <el-tab-pane label="账号设置" name="2">账号设置</el-tab-pane>
        <el-tab-pane label="我的订阅" name="3">
          <ul>
            <li v-for="item in 4" v-bind:key="item">
              <div class="goods-image" style="width:120px;float:left">
                <img src="~@/assets/img/1.jpg" class="image">
              </div>
              <div data-v-e4d24c94="" class="update-info" style="float:right">
                <span>初一数学一 </span>
                已更新 42 个 | 已学习 <span>11</span>个
                <el-button type="primary" @click="getStudy()">开始学习</el-button>
              </div>
              <div style="clear:both"></div>
            </li>
          </ul>
        </el-tab-pane>
      </el-tabs>
    </el-main>
  </el-container>
</template>
<script>
export default {
  methods: {
    getStudy () {
      this.$router.push({ name: 'demo-study' })
    }
  }
}
</script>

<style scoped>
li {
  list-style-type:none;
  margin-bottom: 10px;
  border-bottom: solid 1px gray;
  }
</style>

