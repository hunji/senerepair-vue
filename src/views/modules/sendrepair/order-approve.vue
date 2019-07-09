<template>
  <el-dialog
    width="40%"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <div>
      <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
        <el-form-item label="是否通过：" prop="status">
          <el-switch  v-model="dataForm.status"  active-text="通过" inactive-text="不通过">
          </el-switch>
        </el-form-item>
        <el-form-item label="审核意见：" prop="comments">
          <el-input type="textarea" :autosize="{ minRows: 3, maxRows: 8}" v-model="dataForm.comments" placeholder="审核意见"></el-input>
        </el-form-item>
      </el-form>
    </div>
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
      dataForm: {
        orderId: 0,
        status: '',
        comments: ''
      }
    }
  },
  methods: {
    init (id) {
      this.dataForm.orderId = id
      this.visible = true
    },
    // 表单提交
    dataFormSubmit () {
      if (!this.dataForm.status && this.dataForm.comments.length === 0) {
        this.$alert('审核不通过时需要填写审核意见！')
        return
      }
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/sendrepair/order/approve`),
            method: 'post',
            data: this.$http.adornData({
              'comments': this.dataForm.comments,
              'status': this.dataForm.status,
              'orderId': this.dataForm.orderId,
              'order': 123
            })
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 500,
                onClose: () => {
                  this.visible = false
                  this.$emit('refreshDataList')
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  }
}
</script>
