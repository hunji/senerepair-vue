<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sendrepair:orderwheelinfo:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sendrepair:orderwheelinfo:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  </div>
</template>

<script>
  import AddOrUpdate from './orderwheelinfo-add-or-update'
  import Detail from './orderwheelinfo-detail'
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
        detailVisible: false
      }
    },
    components: {
      AddOrUpdate,
      Detail
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sendrepair/orderwheelinfo/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
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
      // 查看
      detailHandle (row, column, event) {
        this.detailVisible = true
        this.$nextTick(() => {
          this.$refs.detail.init(row.id)
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
            url: this.$http.adornUrl('/sendrepair/orderwheelinfo/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 200) {
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
      }
    }
  }
</script>
