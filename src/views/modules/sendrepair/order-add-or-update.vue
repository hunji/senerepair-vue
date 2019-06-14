<template>
<div>
  <el-dialog
    width="60%"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <div>
      <div class="info">
        送修信息
        <span  v-if="this.dataForm.id">(送修单编号:{{this.dataForm.serialNumber}})</span>
      </div>
      <el-card class="box-card">
        <el-form :inline="true" :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
          <el-form-item label="委托单位名称" prop="trusteeName">
            <el-select v-model="dataForm.trusteeName" placeholder="请选择" style="width: 515px;" filterable @change="trusteeChanged">
              <el-option
                v-for="item in occInfos"
                :key="item.occ01"
                :label="item.occ18"
                :value="item.occ01"
                >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="合同号" prop="contractCode">
            <el-select v-model="dataForm.contractCode" placeholder="请选择" style="width: 515px;" filterable>
              <el-option
                v-for="item in htInfos"
                :key="item.c002"
                :label="item.c002"
                :value="item.c002"
                >
                <span>{{ item.c002 }}</span>
                <span style="color: #8492a6; font-size: 13px">:{{ item.c004 }}/{{ item.c008 }}</span>
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="联系人" prop="contactName">
            <el-input v-model="dataForm.contactName" placeholder="联系人"></el-input>
          </el-form-item>
          <el-form-item label="联系人电话" prop="contactPhone">
            <el-input v-model="dataForm.contactPhone" placeholder="联系人电话"></el-input>
          </el-form-item>
          <el-form-item label="轮对型号" prop="wheelModel">
            <el-input v-model="dataForm.wheelModel" placeholder="轮对型号"></el-input>
          </el-form-item>
          <el-form-item label="送修车间（所）" prop="workshop">
            <el-input v-model="dataForm.workshop" placeholder="送修车间（所）"></el-input>
          </el-form-item>
          <el-form-item label="送修日期" prop="sendDate">
            <el-date-picker style="width: 191px;" value-format="yyyy-MM-dd HH:mm:ss"  v-model="dataForm.sendDate"  type="date" placeholder="送修日期">
            </el-date-picker>
          </el-form-item>
          <el-form-item label="维修地点" prop="sendPlace">
            <el-input v-model="dataForm.sendPlace" placeholder="维修地点"></el-input>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
    
    <div v-if="this.dataForm.id">
      <div class="info" >
        送修轮对信息 
        <el-link type="primary"  @click="importVisible = true">批量导入</el-link>||
        <el-link type="primary"><json-excel style="display: inline;" name="轮对信息.xls" :data = "dataList">  下载轮对信息 </json-excel></el-link>     
      </div>
      <el-card class="box-card">
        <el-table :data="dataList"  border  v-loading="dataListLoading" @selection-change="selectionChangeHandle" style="width: 100%;">
          <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        type="index"
        header-align="center"
        align="center"
        label="序号"
        width="60">
      </el-table-column>
      <el-table-column
        prop="operatingInterval"
        header-align="center"
        align="center"
        label="运营区间">
      </el-table-column>
      <el-table-column
        prop="trainNumber"
        header-align="center"
        align="center"
        label="列车编号">
      </el-table-column>
      <el-table-column
        prop="trainFormation"
        header-align="center"
        align="center"
        label="列车编组">
      </el-table-column>
      <el-table-column
        prop="position"
        header-align="center"
        align="center"
        width="120"
        label="所在车、轴、位">
      </el-table-column>
      <el-table-column
        prop="wheelNum"
        header-align="center"
        align="center"
        width="120"
        label="轮对号">
      </el-table-column>
      <el-table-column
        prop="alxeNum"
        header-align="center"
        align="center"
        width="120"
        label="车轴号">
      </el-table-column>
      <el-table-column
        prop="gearboxNum"
        header-align="center"
        align="center"
        width="120"
        label="齿轮箱号">
      </el-table-column>
      <el-table-column
        prop="hasFour"
        header-align="center"
        align="center"
        width="120"
        label="有无四级修">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.hasFour" size="small">有</el-tag>
          <el-tag v-else size="small">无</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="mile"
        header-align="center"
        align="center"
        width="120"
        label="轮对运行里程">
      </el-table-column>
      <el-table-column
        prop="reason"
        header-align="center"
        align="center"
        width="120"
        label="送修原因">
      </el-table-column>
      <el-table-column
        prop="suggest"
        header-align="center"
        align="center"
        width="140"
        label="建议检修内容">
      </el-table-column>
      <el-table-column
        prop="troubleShooting"
        header-align="center"
        align="center"
        width="200"
        label="近期轮对较大故障处理清单">
      </el-table-column>
        </el-table>
      <el-pagination
        @size-change="sizeChangeHandle"
        @current-change="currentChangeHandle"
        :current-page="pageIndex"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="pageSize"
        :total="totalPage"
        layout="total, sizes, prev, pager, next, jumper">
      </el-pagination>
      </el-card>
    </div>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
  <el-dialog  title="批量导入"  :visible.sync="importVisible" width="30%">
      <span>导入模板下载:</span>
      <a  href="/static/excel/轮对信息模板.xls" target="_blank">导入模板</a>  
      <span slot="footer" class="dialog-footer">
        <el-upload
          :action="uploadUrl"
          :on-success="handleAvatarSuccess"
          :file-list="fileList">
          <el-button size="small" type="primary">点击导入</el-button>
        </el-upload>
      </span>
  </el-dialog>
</div>

  
</template>

<script>
  import JsonExcel from 'vue-json-excel'
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          trusteeName: '',
          trusteeCode: '',
          contactName: '',
          contactPhone: '',
          contractId: '',
          contractCode: '',
          wheelModel: '',
          workshop: '',
          sendDate: '',
          sendPlace: '',
          createdDate: '',
          createBy: '',
          serialNumber: ''
        },
        dataRule: {
          trusteeName: [
            { required: true, message: '委托单位名称不能为空', trigger: 'blur' }
          ],
          contactName: [
            { required: true, message: '联系人不能为空', trigger: 'blur' }
          ],
          contactPhone: [
            { required: true, message: '联系人电话不能为空', trigger: 'blur' }
          ],
          contractId: [
            { required: true, message: '关联合同主键不能为空', trigger: 'blur' }
          ],
          contractCode: [
            { required: true, message: '合同号不能为空', trigger: 'blur' }
          ],
          wheelModel: [
            { required: true, message: '轮对型号不能为空', trigger: 'blur' }
          ],
          workshop: [
            { required: true, message: '送修车间（所）不能为空', trigger: 'blur' }
          ],
          sendDate: [
            { required: true, message: '送修日期不能为空', trigger: 'blur' }
          ],
          sendPlace: [
            { required: true, message: '维修地点不能为空', trigger: 'blur' }
          ]
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        importVisible: false,
        uploadUrl: '',
        fileList: [],
        occInfos: [],
        htInfos: []
      }
    },
    components: {
      JsonExcel
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.uploadUrl = window.SITE_CONFIG.baseUrl + `/sendrepair/orderwheelinfo/upload?orderId=${this.dataForm.id}&token=${this.$cookie.get('token')}`
        this.visible = true
        // 获取委托单位
        this.getOccInfos()
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sendrepair/order/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.trusteeCode = data.order.trusteeCode
                this.dataForm.trusteeName = data.order.trusteeName
                this.dataForm.contactName = data.order.contactName
                this.dataForm.contactPhone = data.order.contactPhone
                this.dataForm.contractId = data.order.contractId
                this.dataForm.contractCode = data.order.contractCode
                this.dataForm.wheelModel = data.order.wheelModel
                this.dataForm.workshop = data.order.workshop
                this.dataForm.sendDate = data.order.sendDate
                this.dataForm.sendPlace = data.order.sendPlace
                this.dataForm.createdDate = data.order.createdDate
                this.dataForm.createBy = data.order.createBy
                this.dataForm.serialNumber = data.order.serialNumber
                this.getHtInfos()
              }
            })
          } else {
            this.htInfos = []
          }
        })
        // 获取轮对信息
        this.getDataList()
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sendrepair/order/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'trusteeName': this.dataForm.trusteeName,
                'trusteeCode': this.dataForm.trusteeCode,
                'contactName': this.dataForm.contactName,
                'contactPhone': this.dataForm.contactPhone,
                'contractId': this.dataForm.contractId,
                'contractCode': this.dataForm.contractCode,
                'wheelModel': this.dataForm.wheelModel,
                'workshop': this.dataForm.workshop,
                'sendDate': this.dataForm.sendDate,
                'sendPlace': this.dataForm.sendPlace,
                'createdDate': this.dataForm.createdDate,
                'createBy': this.dataForm.createBy
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
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
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sendrepair/orderwheelinfo/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'orderId': this.dataForm.id
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 上传成功后的调用函数
      handleAvatarSuccess (data) {
        this.fileList = []
        if (data && data.code === 0) {
          this.dialogVisible = false
          this.$message({
            message: '导入成功',
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.getDataList()
            }
          })
        } else {
          this.$message.error(data.msg)
        }
      },
      // 获取客户列表
      getOccInfos () {
        this.$http({
          url: this.$http.adornUrl(`/sendrepair/userocc/info/${this.dataForm.id}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.occInfos = data.occList
          }
        })
      },
      // 获取合同列表
      getHtInfos () {
        this.htInfos = []
        this.$http({
          url: this.$http.adornUrl('/sendrepair/tabhttz/list'),
          method: 'get',
          params: this.$http.adornParams({
            'code': this.dataForm.trusteeCode
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.htInfos = data.htInfos
          }
        })
      },
      // 委托单位名称选中后的事件：根据委托单位编号获取合同名称
      trusteeChanged (value) {
        let obj = {}
        obj = this.occInfos.find((item) => {
          return item.occ01 === value
        })
        this.dataForm.trusteeName = obj.occ18
        this.dataForm.trusteeCode = obj.occ01
        this.getHtInfos()
      }
    }
  }
</script>
<style scoped>
.info{
  height: 30px;
  margin-top: 10px;
  border-bottom: 2px solid #eee;
  color: rgba(0,0,0,.85);
  font-size: 16px;
  font-weight: 500;
  margin-bottom: 16px;
}
.el-select-dropdown__item{
  max-width: 500px;
}
</style>

