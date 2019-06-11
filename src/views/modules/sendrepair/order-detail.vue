<template>
  <el-dialog
    width="60%"
    :visible.sync="visible">
    <div>
      <div class="info">
        送修信息
        <span  v-if="this.dataForm.id">(送修单编号:{{this.dataForm.serialNumber}})</span>
      </div>
      <el-card class="box-card">
        <el-form disabled :inline="true" :model="dataForm" ref="dataForm" label-width="120px">
          <el-form-item label="委托单位名称" prop="trusteeName">
            <el-input v-model="dataForm.trusteeName" placeholder="委托单位名称"></el-input>
          </el-form-item>
          <el-form-item label="联系人" prop="contactName">
            <el-input v-model="dataForm.contactName" placeholder="联系人"></el-input>
          </el-form-item>
          <el-form-item label="联系人电话" prop="contactPhone">
            <el-input v-model="dataForm.contactPhone" placeholder="联系人电话"></el-input>
          </el-form-item>
          <el-form-item label="合同号" prop="contractCode">
            <el-input v-model="dataForm.contractCode" placeholder="合同号"></el-input>
          </el-form-item>
          <el-form-item label="轮对型号" prop="wheelModel">
            <el-input v-model="dataForm.wheelModel" placeholder="轮对型号"></el-input>
          </el-form-item>
          <el-form-item label="送修车间（所）" prop="workshop">
            <el-input v-model="dataForm.workshop" placeholder="送修车间（所）"></el-input>
          </el-form-item>
          <el-form-item label="送修日期" prop="sendDate">
            <el-date-picker style="width: 191px;"  v-model="dataForm.sendDate"  type="date" placeholder="送修日期">
            </el-date-picker>
          </el-form-item>
          <el-form-item label="维修地点" prop="sendPlace">
            <el-input v-model="dataForm.sendPlace" placeholder="维修地点"></el-input>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
    <div>
      <div class="info">
        流转信息
      </div>
      <el-card class="box-card">
        <el-table :data="approvalList"  border  v-loading="dataListLoading" style="width: 100%;">
          <el-table-column
            type="index"
            header-align="center"
            align="center"
            label="序号"
            width="60">
          </el-table-column>
          <el-table-column
            prop="name"
            header-align="center"
            align="center"
            label="审批人">
          </el-table-column>
          <el-table-column
            prop="createdTime"
            header-align="center"
            align="center"
            label="审批时间">
          </el-table-column>         
          <el-table-column
            prop="status"
            header-align="center"
            align="center"
            width="120"
            label="审批状态">
            <template slot-scope="scope">
              <el-tag v-if="scope.row.status" size="small">通过</el-tag>
              <el-tag v-else size="small"  type="danger">不通过</el-tag>
            </template>
          </el-table-column>
          <el-table-column
            prop="comments"
            header-align="center"
            align="center"
            show-overflow-tooltip
            label="审批意见">
          </el-table-column>
        </el-table>
      </el-card>
    </div>
    <div v-if="this.dataForm.id">
      <div class="info" >
        送修轮对信息 
      </div>
      <el-card class="box-card">
        <el-table :data="dataList"  border  v-loading="dataListLoading" @selection-change="selectionChangeHandle" style="width: 100%;">
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
    
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          trusteeName: '',
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
        dataList: [],
        approvalList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: []
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
              url: this.$http.adornUrl(`/sendrepair/order/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
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
              }
            })
          }
        })
        this.getDataList()
        this.getApprovalList()
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
            'key': this.dataForm.key,
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
      // 获取流转信息
      getApprovalList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sendrepair/approval/list'),
          method: 'get',
          params: this.$http.adornParams({
            'orderId': this.dataForm.id
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.approvalList = data.approvalList
          } else {
            this.dataList = []
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
</style>

