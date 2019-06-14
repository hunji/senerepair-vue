<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sendrepair:order:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sendrepair:order:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"	
      @row-dblclick="detailHandle"
      style="width: 100%;">
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
        prop="serialNumber"
        header-align="center"
        align="center"
        label="送修单据号"
        width="200">
      </el-table-column>
      <el-table-column
        prop="trusteeName"
        header-align="center"
        align="center"
        label="委托单位名称"
        show-overflow-tooltip
        width="200">
      </el-table-column>
      <el-table-column
        prop="contractCode"
        header-align="center"
        align="center"
        label="合同号"
        show-overflow-tooltip
        width="200">
      </el-table-column>
      <el-table-column
        prop="contactName"
        header-align="center"
        align="center"
        label="联系人"
        width="80">
      </el-table-column>
      <el-table-column
        prop="wheelModel"
        header-align="center"
        align="center"
        label="轮对型号"
        width="120">
      </el-table-column>
      <el-table-column
        prop="workshop"
        header-align="center"
        align="center"
        label="送修车间（所）"
        width="120">
      </el-table-column>
      <el-table-column
        prop="sendDate"
        header-align="center"
        align="center"
        label="送修日期"
        width="120">
        <template slot-scope="scope">
        <span style="margin-left: 10px">{{ scope.row.sendDate.substr(0,10) }}</span>
      </template>
      </el-table-column>
      <el-table-column
        prop="sendPlace"
        header-align="center"
        align="center"
        label="维修地点"
        width="120">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="200%"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" v-if="isAuth('sendrepair:order:initiate') && (scope.row.status==='Created'||scope.row.status==='Refused')" size="small" @click="initiateHandle(scope.row.id)">发起</el-button>
          <el-button type="text" v-if="isAuth('sendrepair:order:approve')  && (scope.row.status==='Initiated')" size="small" @click="approveHandle(scope.row.id)">审核</el-button>
          <el-button type="text" v-if="isAuth('sendrepair:order:update') && (scope.row.status==='Created'||scope.row.status==='Refused')"  size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" v-if="isAuth('sendrepair:order:delete') && (scope.row.status==='Created'||scope.row.status==='Refused')" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
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
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <detail v-if="detailVisible"  ref="detail"></detail>
    <approve v-if="aproveVisible"  ref="approve" @refreshDataList="getDataList"></approve>
  </div>
</template>

<script>
  import AddOrUpdate from './order-add-or-update'
  import Detail from './order-detail'
  import Approve from './order-approve'
  export default {
    data () {
      return {
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        detailVisible: false,
        aproveVisible: false
      }
    },
    components: {
      AddOrUpdate,
      Detail,
      Approve
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sendrepair/order/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
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
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sendrepair/order/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
       // 查看
      detailHandle (row, column, event) {
        this.detailVisible = true
        this.$nextTick(() => {
          this.$refs.detail.init(row.id)
        })
      },
      // 审批
      approveHandle (id) {
        this.aproveVisible = true
        this.$nextTick(() => {
          this.$refs.approve.init(id)
        })
      },
      // 发起送修单
      initiateHandle (id) {
        this.$confirm(`确定对发起送修单?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/sendrepair/order/initiate/${id}`),
            method: 'post',
            data: this.$http.adornData()
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>

