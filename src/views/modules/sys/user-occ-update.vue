<template>
  <el-dialog
    :close-on-click-modal="false"
    width="60%"
    :visible.sync="visible">
    <el-transfer 
      filterable 
      v-model="occList" 
      :data="allInfos" 
      :props="{
        key: 'occ01',
        label: 'occ18'
      }"
      :titles="['客户列表','已关联']" ></el-transfer>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        allInfos: [],
        occList: [],
        id: 0
      }
    },
    methods: {
      init (id) {
        this.id = id
        // 分两步：
        // 1)先获取到全部的客户列表
        // 2)获取
        this.$http({
          url: this.$http.adornUrl(`/sendrepair/userocc/info/${this.id}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.occList = data.occList.map(function (obj, index) {
              return obj.occ01
            })
            console.log(this.occList)
          }
        }).then(() => {
          if (this.id) {
            this.$http({
              url: this.$http.adornUrl('/sendrepair/occ/list'),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.allInfos = data.occInfos
              }
            })
          }
        }).then(() => {
          this.visible = true
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$http({
          url: this.$http.adornUrl(`/sendrepair/userocc/save`),
          method: 'post',
          data: this.$http.adornData({
            'userId': this.id,
            'occList': this.occList
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1000,
              onClose: () => {
                this.visible = false
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      }
    }
  }
</script>
<style>
.el-transfer-panel{
  width: 300px;
}
</style>

