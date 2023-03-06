<template>
  <div id="BdTest">
    <el-form>
      <el-form-item>
        <el-button type="primary" size="small" @click="winShow">下载</el-button>
      </el-form-item>
    </el-form>
    <vxe-table
      ref="xTable2"
      border
      resizable
      row-id="id"
      :loading="loading"
      :max-height="500"
      highlight-hover-row
      :data="tableData"
      :checkbox-config="{
        highlight: true,
        highlight: true,
        reserve: true,
        range: true,
      }"
      @checkbox-change="handleSelectionChange"
      @checkbox-all="handleSelectionChange"
    >
      <vxe-column type="seq" title="序号" width="60" />
      <vxe-table-column
        v-for="(config, index) in tableHead"
        :key="index"
        sortable
        :type="config.mold"
        :field="config.field"
        :title="config.title"
        :visible="config.visible"
        :fixed="config.fixed"
        :width="config.width"
        :min-width="config.minWidth"
        :filters="config.filters"
      />
    </vxe-table>
    <vxe-pager
      background
      size="small"
      :loading="loading"
      :current-page="currentPage"
      :page-size="pageSize"
      :total="recordsCount"
      :page-sizes="[
        10,
        50,
        100,
        { label: '大量数据', value: 1000 },
        { label: '全量数据', value: recordsCount },
      ]"
      :layouts="[
        'PrevPage',
        'JumpNumber',
        'NextPage',
        'FullJump',
        'Sizes',
        'Total',
      ]"
      @page-change="handleSizeChange"
    />
    <el-dialog
      :title="title"
      :visible.sync="refixPCBADialogVisible"
      width="35%"
      append-to-body
      :before-close="refixPCBADialogClose"
    >
      <el-form label-width="120px">
        <el-form-item label="QN号:">
          <el-input
            v-model="fieldobj.groupCode"
            placeholder="请输入内容"
            style="width: 250px"
            clearable
          />
        </el-form-item>
        <el-form-item label="QN号:">
          <el-input
            v-model="fieldobj.groupName"
            placeholder="请输入内容"
            style="width: 250px"
            clearable
          />
        </el-form-item>
        <el-form-item label="QN号:">
          <el-input
            v-model="fieldobj.groupDesc"
            placeholder="请输入内容"
            style="width: 250px"
            clearable
          />
        </el-form-item>
        <el-form-item label="QN号:">
          <el-input
            v-model="fieldobj.pdcCode"
            placeholder="请输入内容"
            style="width: 250px"
            clearable
          />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" size="small" @click="refixPCBAExcute">提交</el-button>
          <el-button
            type="primary"
            size="small"
            @click="refixPCBADialogClose"
          >关闭</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
import { GroupAdd } from '@/api/baseInfoApi'
export default {
  name: 'BdTest',
  data() {
    return {
      fieldobj: {
        groupCode: '',
        groupName: '',
        groupDesc: '',
        pdcCode: ''
      },
      tableHead: [
        { field: 'newSn', title: '返修后SN', width: 150 },
        { field: 'oldSn', title: '返修前SN', width: 150 },
        { field: 'newSn', title: '返修后SN', width: 150 },
        { field: 'oldSn', title: '返修前SN', width: 150 },
        { field: 'newSn', title: '返修后SN', width: 150 },
        { field: 'oldSn', title: '返修前SN', width: 150 }
      ],
      tableData: [],
      // 当前页
      currentPage: 1,
      recordsCount: 0,
      // 每页条数
      pageSize: 10,
      loading: false,
      columns2: [],
      dataList: [],
      dialogVisible: false,
      refixPCBADialogVisible: false,
      upLoadDialogV: false,
      TabularDataB: [],
      title: '',
      multipleSelection: []
    }
  },
  methods: {
    winShow() {
      this.refixPCBADialogVisible = true
    },
    refixPCBADialogClose() {
      this.refixPCBADialogVisible = false
    },
    refixPCBAExcute() {
      GroupAdd(this.fieldobj)
        .then(res => {
          console.log(res)
        })
    },
    handleSelectionChange({ records, reserves }) {
      var list = [...records, ...reserves]
      this.multipleSelection = []
      list.map((item) => {
        this.multipleSelection.push(item.ordId)
      })
    },
    handleSizeChange({ currentPage, pageSize }) {
      this.currentPage = currentPage
      this.pageSize = pageSize
      this.tableData = this.dataList.slice(
        (this.currentPage - 1) * this.pageSize,
        this.currentPage * this.pageSize
      )
    },
    handleClose(done) {
      done()
    }
  }
}
</script>
<style scoped></style>
