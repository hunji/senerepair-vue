<template>
  <el-dialog
    width="80%"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <div class="info">
        轮对信息
    </div>
    <el-card class="box-card">
      <el-form :model="dataForm" disabled=""
      ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px" class="content">
      <el-col :span="8">
        <el-form-item label="列车编号" prop="trainNumber">
          <el-input v-model="dataForm.trainNumber" placeholder="列车编号"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="列车编组" prop="trainFormation">
          <el-input v-model="dataForm.trainFormation" placeholder="列车编组"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="车、轴、位" prop="position">
          <el-input v-model="dataForm.position" placeholder="所在车、轴、位"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="16">
        <el-form-item label="选择选修单据" prop="srOrderId">
           <el-input v-model="dataForm.srOrderId" placeholder="运营区间"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
          <el-form-item label="运营区间" prop="operatingInterval">
          <el-input v-model="dataForm.operatingInterval" placeholder="运营区间"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="轮对号" prop="wheelNum">
          <el-input v-model="dataForm.wheelNum" placeholder="轮对号"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="车轴号" prop="alxeNum">
          <el-input v-model="dataForm.alxeNum" placeholder="车轴号"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="齿轮箱号" prop="gearboxNum">
          <el-input v-model="dataForm.gearboxNum" placeholder="齿轮箱号"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="运行里程" prop="mile">
          <el-input v-model="dataForm.mile" placeholder="轮对运行里程"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="有无四级修" prop="hasFour">
          <el-select v-model="dataForm.hasFour" placeholder="请选择" style="width: 100%;">
            <el-option key="1" label="有" :value="true"></el-option>
            <el-option key="0" label="无" :value="false"></el-option>
          </el-select>
        </el-form-item>
      </el-col>
      <el-col :span="8" v-show="dataForm.hasFour">
        <el-form-item label="公里数" prop="fourMile" >
          <el-input v-model="dataForm.fourMile" placeholder="四级修时走行公里数"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="6">
        <el-form-item label="左端是否带轴承" prop="hasLeftBearing">
        <el-select v-model="dataForm.hasLeftBearing" placeholder="请选择" style="width: 100%;">
          <el-option key="1" label="是" :value="true"></el-option>
          <el-option key="0" label="否" :value="false"></el-option>
        </el-select>
        </el-form-item> 
      </el-col>
      <el-col :span="6" v-show="dataForm.hasLeftBearing">
        <el-form-item label="左轴承运行里程" prop="liftBearingMile">
          <el-input v-model="dataForm.liftBearingMile" placeholder="运行里程数" style="width: 100%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="6">
        <el-form-item label="右端是否带轴承" prop="hasRightBearing" >
        <el-select v-model="dataForm.hasRightBearing" placeholder="请选择" style="width: 100%;">
          <el-option key="1" label="是" :value="true"></el-option>
          <el-option key="0" label="否" :value="false"></el-option>
        </el-select>
        </el-form-item>
      </el-col>
      <el-col :span="6" v-show="dataForm.hasRightBearing">
        <el-form-item label="右轴承运行里程" prop="rightBearingMile">
          <el-input v-model="dataForm.rightBearingMile" placeholder="运行里程数" style="width: 100%;"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="12">
        <el-form-item label="送修原因" prop="reason">
          <el-input style="width: 100%;"  type="textarea" v-model="dataForm.reason" placeholder="送修原因"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="12">
        <el-form-item label="建议检修内容" prop="suggest">
          <el-input  style="width: 100%;"  type="textarea" v-model="dataForm.suggest" placeholder="建议检修内容"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="24">
        <el-form-item label="近期轮对较大故障处理清单" prop="troubleShooting">
          <el-input  style="width: 100%;" :rows="3"  type="textarea" v-model="dataForm.troubleShooting" placeholder="近期轮对较大故障处理清单"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="24">
        <el-form-item label="备注" prop="remark">
          <el-input style="width: 100%;" :rows="3"  type="textarea" v-model="dataForm.remark" placeholder="备注"></el-input>
        </el-form-item>
      </el-col>
    </el-form>
    </el-card>
    
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          operatingInterval: '',
          trainNumber: '',
          trainFormation: '',
          position: '',
          wheelNum: '',
          alxeNum: '',
          gearboxNum: '',
          hasFour: false,
          fourMile: '',
          mile: '',
          reason: '',
          suggest: '',
          troubleShooting: '',
          hasLeftBearing: false,
          hasRightBearing: false,
          liftBearingMile: '',
          rightBearingMile: '',
          remark: '',
          createBy: '',
          createdDate: '',
          srOrderId: ''
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sendrepair/orderwheelinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.srOrderId = data.orderWheelInfo.srOrderId
                this.dataForm.operatingInterval = data.orderWheelInfo.operatingInterval
                this.dataForm.trainNumber = data.orderWheelInfo.trainNumber
                this.dataForm.trainFormation = data.orderWheelInfo.trainFormation
                this.dataForm.position = data.orderWheelInfo.position
                this.dataForm.wheelNum = data.orderWheelInfo.wheelNum
                this.dataForm.alxeNum = data.orderWheelInfo.alxeNum
                this.dataForm.gearboxNum = data.orderWheelInfo.gearboxNum
                this.dataForm.hasFour = data.orderWheelInfo.hasFour
                this.dataForm.fourMile = data.orderWheelInfo.fourMile
                this.dataForm.mile = data.orderWheelInfo.mile
                this.dataForm.reason = data.orderWheelInfo.reason
                this.dataForm.suggest = data.orderWheelInfo.suggest
                this.dataForm.troubleShooting = data.orderWheelInfo.troubleShooting
                this.dataForm.hasLeftBearing = data.orderWheelInfo.hasLeftBearing
                this.dataForm.hasRightBearing = data.orderWheelInfo.hasRightBearing
                this.dataForm.liftBearingMile = data.orderWheelInfo.liftBearingMile
                this.dataForm.rightBearingMile = data.orderWheelInfo.rightBearingMile
                this.dataForm.remark = data.orderWheelInfo.remark
              }
            })
          }
        })
      }
    }
  }
</script>
<style scoped>
.content{
  margin: 0 20px;
}
.info{
  height: 30px;
  margin-top: 10px;
  border-bottom: 2px solid #eee;
  color: rgba(0,0,0,.85);
  font-size: 16px;
  font-weight: 500;
  margin-bottom: 16px;
}
</style>

