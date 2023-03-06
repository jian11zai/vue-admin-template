<template>
  <div class="supplierInfo">
    <div class="card_view">
      <div class="card_text">
        <div>供应商名称：</div>
        <el-input v-model="supName" class="view_margin_left" placeholder="请输入客户名称" :clearable="true" />
        <div class="view_margin_left">供应商等级：</div>
        <!-- <el-input v-model="supLevelSelect" class="view_margin_left" placeholder="请输入客户等级" :clearable="true" /> -->
        <el-select v-model="supLevel" placeholder="请选择">
          <el-option
            v-for="item in levelOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <el-button type="primary" class="view_margin_left" @click="searchEvent()">查询</el-button>
        <el-button type="primary" class="view_margin_left" @click="selectListItem(true,{})">新增</el-button>
      </div>
      <vxe-grid ref="xGrid" v-bind="gridOptions" @page-change="handlePageChange">
        <template #supCode_edit="{ row }">
          <vxe-input v-model="row.supCode" />
        </template>
        <template #supName_edit="{ row }">
          <vxe-input v-model="row.supName" />
        </template>
        <template #supLevel_edit="{ row }">
          <vxe-input v-model="row.supLevel" />
        </template>
        <template #contact_edit="{ row }">
          <vxe-input v-model="row.contact" />
        </template>
        <template #mobile_edit="{ row }">
          <vxe-input v-model="row.mobile" />
        </template>
        <template #email_edit="{ row }">
          <vxe-input v-model="row.email" />
        </template>
        <template #address_edit="{ row }">
          <vxe-input v-model="row.address" />
        </template>
        <template #depaName_edit="{ row }">
          <vxe-input v-model="row.depaName" />
        </template>
        <template #state_edit="{ row }">
          <vxe-input v-model="row.state" />
        </template>
        <template #operate="{ row }">
          <vxe-button type="text" status="primary" content="编辑" @click="selectListItem(false,row)" />
          <vxe-button type="text" status="primary" content="删除" @click="SupplierDel(row)" />
        </template>
      </vxe-grid>
    </div>
    <el-dialog :title="isAdd?'新增':'编辑'" width="25%" :visible.sync="isShow">
      <div class="popup_view">
        <div class="line" />
        <div class="popup">
          <div class="text_view">
            <div class="lable_view">供应商代码<span>*</span></div>
            <div class="option">
              <el-input v-model="apiItem.supCode" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">供应商名称<span>*</span></div>
            <div class="option">
              <el-input v-model="apiItem.supName" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">等级<span>*</span></div>
            <div class="option">
              <el-select v-model="apiItem.supLevel" placeholder="请选择">
                <el-option
                  v-for="item in levelOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">联系人</div>
            <div class="option">
              <el-input v-model="apiItem.contact" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">联系方式</div>
            <div class="option">
              <el-input v-model="apiItem.mobile" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">邮箱</div>
            <div class="option">
              <el-input v-model="apiItem.email" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">通讯地址</div>
            <div class="option">
              <el-input v-model="apiItem.address" placeholder="请输入内容" />
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">状态</div>
            <div class="option">
              <el-select v-model="apiItem.state" placeholder="请选择">
                <el-option
                  v-for="item in stateOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </div>
          </div>
          <div class="text_view">
            <div class="lable_view">部门<span>*</span></div>
            <div class="option">
              <el-select v-model="apiItem.depaCode" placeholder="请选择">
                <el-option v-for="item in depaOptions" :key="item.id" :label="item.depaName" :value="item.depaCode" />
              </el-select>
            </div>
          </div>
          <div class="button_view">
            <el-button type="info" plain @click="closeShow()">关 闭</el-button>
            <el-button type="primary" plain @click="submit()">提 交</el-button>
          </div>
        </div>
      </div>
      <!-- <div class=""></div> -->
    </el-dialog>
  </div>
</template>

<script>
import { getSupplierInfo, getDepaInfo, SupplierDel, SupplierAdd, SupplierEdit } from '@/api/baseInfoApi'
export default {
  name: 'SupplierInfo',
  data() {
    return {
      gridOptions: {
        border: true,
        resizable: true,
        keepSource: true,
        showOverflow: true,
        height: (document.documentElement.clientHeight - 200 - (document.documentElement.clientHeight * 0.05)),
        loading: false,
        pagerConfig: {
          total: 0,
          currentPage: 1,
          pageSize: 10,
          pageSizes: [10, 20, 50, 100, 200, 500]
        },
        editConfig: {
          // 设置触发编辑为手动模式
          trigger: 'manual',
          // 设置为整行编辑模式
          mode: 'row',
          // 显示修改状态和新增状态
          showStatus: true
        },
        columns: [
          { field: 'supCode', title: '供应商代码', editRender: {}, slots: { edit: 'supCode_edit' }},
          { field: 'supName', title: '供应商名称', editRender: {}, slots: { edit: 'supName_edit' }},
          { field: 'supLevel', title: '供应商等级', editRender: {}, slots: { edit: 'supLevel_edit' }},
          { field: 'contact', title: '联系人', editRender: {}, slots: { edit: 'contact_edit' }},
          { field: 'mobile', title: '联系方式', editRender: {}, slots: { edit: 'mobile_edit' }},
          { field: 'email', title: '邮箱', editRender: {}, slots: { edit: 'email_edit' }},
          { field: 'address', title: '通讯地址', editRender: {}, slots: { edit: 'address_edit' }},
          { field: 'depaName', title: '部门', editRender: {}, slots: { edit: 'depaName_edit' }},
          { field: 'state', title: '状态', editRender: {}, slots: { edit: 'state_edit' }},
          { title: '操作', width: 200, slots: { default: 'operate' }}
        ],
        data: []
      },
      isAdd: true,
      isShow: false,
      apiItem: {},
      baseItem: {
        supCode: '',
        supName: '',
        supLevel: '',
        contact: '',
        mobile: '',
        email: '',
        address: '',
        depaCode: '',
        state: ''
      },
      supName: '',
      supLevel: '',
      levelOptions: [{
        value: 'A+',
        label: 'A+'
      }, {
        value: 'A',
        label: 'A'
      }, {
        value: 'B',
        label: 'B'
      }, {
        value: 'C',
        label: 'C'
      }],
      depaOptions: [],
      stateOptions: [{
        value: 1,
        label: '启用'
      }, {
        value: 0,
        label: '冻结'
      }]
    }
  },
  created() {
    this.getData()
    getDepaInfo().then(res => {
      console.log(res)
      this.depaOptions = res.data
    })
  },
  methods: {
    getData() {
      // this.listLoading = true
      this.gridOptions.loading = true
      var params = {
        supName: this.supName,
        supLevel: this.supLevel
      }
      getSupplierInfo(params).then(res => {
        console.log(res)
        this.gridOptions.loading = false
        this.gridOptions.pagerConfig.total = res.data.length
        this.dataList = res.data
        // this.gridOptions.data = res.data
        this.getList()
      })
    },
    getList() {
      var endNum = (this.gridOptions.pagerConfig.currentPage * this.gridOptions.pagerConfig.pageSize)
      var startNum = endNum - this.gridOptions.pagerConfig.pageSize
      var list = []
      if (endNum > this.dataList.length) {
        endNum = this.dataList.length
      }
      for (var i = startNum; i < endNum; i++) {
        var item = this.dataList[i]
        list.push(item)
      }
      this.gridOptions.data = list
    },
    reGetData() {
      this.custName = ''
      this.custLevel = ''
      this.searchEvent()
    },
    searchEvent() {
      this.gridOptions.pagerConfig.currentPage = 1
      this.getData()
    },
    handlePageChange({ currentPage, pageSize }) {
      this.gridOptions.pagerConfig.currentPage = currentPage
      this.gridOptions.pagerConfig.pageSize = pageSize
      this.getList()
    },
    submit() {
      if (this.isAdd) {
        this.SupplierAdd()
      } else {
        this.SupplierEdit()
      }
    },
    SupplierAdd() {
      var check = this.checkData(this.apiItem)
      if (check) {
        // var params = this.apiItem
        var params = {
          supCode: this.apiItem.supCode ? this.apiItem.supCode : '',
          supName: this.apiItem.supName ? this.apiItem.supName : '',
          supLevel: this.apiItem.supLevel ? this.apiItem.supLevel : '',
          contact: this.apiItem.contact ? this.apiItem.contact : '',
          mobile: this.apiItem.mobile ? this.apiItem.mobile : '',
          email: this.apiItem.email ? this.apiItem.email : '',
          address: this.apiItem.address ? this.apiItem.address : '',
          depaCode: this.apiItem.depaCode ? this.apiItem.depaCode : '',
          state: this.apiItem.state ? this.apiItem.state : 1
        }
        SupplierAdd(params).then(res => {
          console.log(res)
          this.apiItem = {}
          this.isShow = false
          this.reGetData()
        })
      }
    },
    selectListItem(isAdd, row) {
      this.isAdd = isAdd
      this.apiItem = row
      this.isShow = true
    },
    closeShow() {
      this.isAdd = true
      this.apiItem = {}
      this.isShow = false
    },
    SupplierEdit() {
      var check = this.checkData(this.apiItem)
      if (check) {
        var params = this.apiItem
        SupplierEdit(params).then(res => {
          console.log(res)
          this.apiItem = {}
          this.isShow = false
          this.reGetData()
        })
      }
    },
    SupplierDel(item) {
      if (!item) {
        this.$message({
          type: 'info',
          message: '此用户异常请联系管理员'
        })
        return
      }
      var params = {
        id: item.id
      }
      this.$confirm('此操作将永久删除此客户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        SupplierDel(params).then(res => {
          console.log(res)
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.reGetData()
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    checkData(item) {
      if (!item.supCode || item.supCode === '') {
        this.$message.error('请输入供应商代码')
        return false
      } else if (!item.supName || item.supName === '') {
        this.$message.error('请输入供应商名称')
        return false
      } else if (!item.supLevel || item.supLevel === '') {
        this.$message.error('请选择等级')
        return false
      } else if (!item.depaCode || item.depaCode === '') {
        this.$message.error('请选择部门')
        return false
      } else {
        return true
      }
    }
  }
}
</script>

<style lang="less" scoped>
.supplierInfo {
    margin: 30px;

    .card_view {
        display: flex;
        flex-direction: column;

        .card_text {
            display: flex;
            flex-direction: row;
            align-items: center;
            width: 100%;
            height: 65px;
            padding-left: 15px;
            padding-right: 15px;
            padding-bottom: 15px;

            .view_margin_left {
                margin-left: 15px;
            }
        }
    }

    .popup_view {
        width: 100%;
        display: flex;
        flex-direction: column;

        .popup {
            width: 100%;
            display: flex;
            flex-direction: column;
            padding: 30px 20px 30px 20px;

            .text_view {
                display: flex;
                flex-direction: row;
                width: 100%;
                height: 50px;
                align-items: center;
                margin-bottom: 10px;

                .lable_view {
                    width: 80px;
                    font-size: 14px;
                    color: #333333;

                    span {
                        font-size: 10x;
                        color: red;
                    }
                }

                .option {
                    flex: 1;
                }
            }

            .button_view {
                width: 100%;
                height: 45px;
                display: flex;
                flex-direction: row-reverse;
            }
        }

        .line {
            width: 100%;
            height: 1px;
            background: #EEEEEE;
        }
    }
}
</style>
<style lang="less">
.card_view {
    .el-input {
        width: 200px;
        height: 45px;

        input {
            height: 100%;
        }

        .el-input__inner {
            height: 100%;
            line-height: 45px;
        }
    }
}

.el-dialog__body {
    padding: 0;
}

.popup {
    .el-button {
        width: 100px;
        margin-left: 10px;
    }
}
</style>

