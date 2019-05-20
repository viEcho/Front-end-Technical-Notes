页面截图：
![Image text](https://github.com/viEcho/Front-end-Technical-Notes/blob/master/imgs/distinct_sel.png)
<template>
  <div id='gainDetails'>
    <div class='tabletitle'>获取列表
    </div>
    <div class='search_box'>
      <el-form :inline="true" ref="formInline" label-position="right" :model="formInline" class="form-inline">
        <el-form-item label="信息资源名称" prop="resourceName">
          <el-input disabled="disabled" v-model="formInline.resourceName" placeholder='请输入信息资源名称'></el-input>
        </el-form-item>
        <div  v-for="(row, i) in formInline.rows" :key="i">
            <el-form-item label="数据项" :prop="'rows.'+i+'.enName'" :rules="[{ required: true, message: '数据项不能为空',trigger: 'change'}]">
              <el-select v-model="row.enName" placeholder="请选择" @change="buildColumnDatas(row.enName,row.index)">
                <el-option :label="item.enName+'-'+item.cnName" :value="item.enName" :disabled="item.isDisabled" v-for="(item,index) in row.fieldList" :key="index"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="查询值" :prop="'rows.'+i+'.fieldValue'" :rules="[{ required: true, message: '查询值不能为空',trigger: 'blur'}]">
              <el-input v-if='row.dataType == 1' name='valueEnd'
                v-model='row.fieldValue' placeholder='请输入文本'></el-input>
              <el-date-picker v-if='row.dataType == 2'
                v-model='row.fieldValue' value-format='yyyy-MM-dd HH:mm:ss'
                type='datetime'></el-date-picker>
              <el-input v-if='row.dataType == 3' name='valueEnd'
                v-model='row.fieldValue' type='number'
                onkeyup="this.value=this.value.replace(/[^(\-)0-9]/g,'')"
                placeholder='请输入整数'></el-input>
              <el-input v-if='row.dataType == 4' name='valueEnd'
                v-model='row.fieldValue' onkeyup="this.value=this.value.replace(/[^\-?\d.]/g,'')"
                placeholder='请输入整数或小数'></el-input>
              <el-date-picker v-if='row.dataType == 5'
                v-model='row.fieldValue' value-format='yyyy-MM-dd'
                type='date'></el-date-picker>
            </el-form-item>
            <el-form-item>
              <span v-if="row.index == 0" class="icon iconfont icon-jiahao" @click="copyOneRow()"></span>
              <span v-if="row.index > 0" class="icon iconfont icon-jianhao" @click="cancleOneRow(row.index)"></span>
            </el-form-item>
        </div >
        <el-form-item class="search_btns">
          <el-button plain @click="getTableData">查询</el-button>
          <el-button class="reset" plain @click="reBack">取消</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div v-if="isTableShow">
      <div class="table_box">
        <div class="table_title">
          <span>获取结果</span>
        </div>
        <el-table :data="tableData" border style="width: 100%" v-loading='tableLoading'>
          <el-table-column v-for="(item, index) in tableHeader" :prop="item.prop" :label="item.label" :key="index" align="center" show-overflow-tooltip>
          </el-table-column>
        </el-table>
      </div>
      <div class="paging">
          <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNumber"
          :page-sizes="[5, 10, 20, 50,100]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
          </el-pagination>
      </div>
    </div>

  </div>
</template>
<script>
import {queryFiledListHqOrBd, getObtainResult} from '@/apis/dataExchange/exchangeServer.js'
import storeUserId from '@/store/configStore.js'
export default {
  name: 'gainDetails',
  data () {
    return {
      formInline: {sourceName: '', rows: []},
      index: 0,
      selectedField: [],
      selectList: [],
      selectMap: {},
      fieldList: [],
      pageSize: 10,
      pageNumber: 1,
      total: 0,
      tableLoading: false,
      isTableShow: false,
      tableHeader: [],
      tableData: []
    }
  },
  watch: {
  },
  methods: {
    handleSizeChange (val) {
      this.pageSize = val
      this.getTableData()
    },
    handleCurrentChange (val) {
      this.pageNumber = val
      this.getTableData()
    },
    // 构建查询参数
    buildQueryParams () {
      let data = {}
      data.pageNumber = this.pageNumber
      data.pageSize = this.pageSize
      data.resourceType = 1
      data.searchType = 1
      data.createdBy = this.$route.query.createdBy
      data.isDataCenter = this.formInline.isDataCenter
      data.resourceId = this.formInline.resourceId
      data.tableName = this.formInline.tableName
      data.userId = storeUserId.state.userId
      this.fieldList.forEach((val1, index1) => {
        this.formInline.rows.forEach((val2, index2) => {
          if (index1 === index2) {
            val1.fieldValue = val2.fieldValue
          }
        })
      })
      data.fieldList = this.fieldList
      return data
    },
    buildTableData (data) {
      if (data.fieldCnNames && data.fieldEnNames) {
        let fieldCnNames = data.fieldCnNames.toString()
        let fieldEnNames = data.fieldEnNames.toString()
        let tableHeader = fieldCnNames.split(',')
        let tableBody = fieldEnNames.split(',')
        let columnHeaderList = []
        tableHeader.forEach((val1, index1) => {
          tableBody.forEach((val2, index2) => {
            let newMap = {}
            if (index1 === index2) {
              newMap.label = val1
              newMap.prop = val2
              columnHeaderList.push(newMap)
            }
          })
        })
        this.tableHeader = columnHeaderList
        let newColumnList = []
        // 构建动态表格数据
        if (data.rows.length > 0) {
          data.rows.forEach((item, i) => {
            let newColumnMap = {}
            columnHeaderList.forEach((val, index) => {
              let columnProp = val.prop
              let columnLabel = val.label
              newColumnMap[columnProp] = item[columnProp]
              newColumnMap[columnLabel] = columnLabel
            })
            newColumnList.push(newColumnMap)
          })
        }
        this.tableData = newColumnList
        this.isTableShow = true
      }
    },
    getTableData () {
      this.$refs['formInline'].validate((valid) => {
        if (valid) {
          let data = this.buildQueryParams()
          getObtainResult(data).then(res => {
            if (res.code === 100) {
              this.buildTableData(res.data)
            } else {
              this.$message.error(res.message)
            }
          }).catch(() => {
            this.$message.error('服务器出错!')
          })
        } else {
          return false
        }
      })
    },
    reBack () {
      this.$confirm('确定要离开当前页面吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$router.push('/exchangeServerManage')
      })
    },
    // 下拉onChange事件
    buildColumnDatas (val, index) {
      this.fieldList.splice(index, 1)
      if (!val) return
      let obj = {}
      obj = this.selectList.find(item => {
        return item.enName === val
      })
      let queryMap = {}
      queryMap.fieldEnName = obj.enName
      queryMap.fieldCnName = obj.cnName
      queryMap.dataType = obj.dataType
      this.fieldList.push(queryMap)
      this.selectMap = obj
      this.formInline.rows.forEach((val1, index1) => {
        if (index === index1) {
          val1.dataType = obj.dataType
          val1.fieldValue = ''
        }
      })
      console.log('change')
      console.log(this.selectedField[index])
      // 存储下拉改变前字段
      sessionStorage.setItem('changeBeforeField', this.selectedField[index])
      // 构建选中字段集
      this.selectedField[index] = val
      this.buildWholeDatas(index)
    },
    // 构建整个数据集
    buildWholeDatas (index) {
      // 组装整个显示或隐藏的下拉
      this.selectedField.forEach((val, index) => {
        let selectedField = val
        this.formInline.rows.forEach((val1, index1) => {
          let fieldList = val1.fieldList
          fieldList.forEach((val2, index2) => {
            // 释放下拉改变前选中的置灰项
            if (val2.enName === sessionStorage.getItem('changeBeforeField')) {
              val2.isDisabled = false
            }
            if (index !== index1 && val2.enName === selectedField) {
              val2.isDisabled = true
            }
          })
        })
      })
    },
    // 新构建展示下拉数据
    newBuildFieldList (index, fieldList) {
      let fieldMap = {}
      fieldMap.index = index
      fieldMap.enName = ''
      fieldMap.fieldValue = ''
      fieldMap.dataType = 1
      fieldMap.fieldList = fieldList
      this.formInline.rows.push(fieldMap)
      this.buildWholeDatas(index)
    },
    // 新建一行
    copyOneRow () {
      this.$refs['formInline'].validate((valid) => {
        if (valid) {
          // 当index等于 返回字段数组长度时  不再添加一行
          if (this.index + 1 === this.selectList.length) {
            return false
          }
          this.index++
          this.newBuildFieldList(this.index, this.selectList)
        } else {
          return false
        }
      })
    },
    // 删除一行
    cancleOneRow (index) {
      // 获取删除行字段  释放其他置灰项
      sessionStorage.setItem('dyncShowField', this.selectedField[index])
      this.formInline.rows.splice(index, 1)
      this.selectedField.splice(index, 1)
      this.fieldList.splice(index, 1)
      this.formInline.rows.forEach((val1, index1) => {
        let fieldList = val1.fieldList
        fieldList.forEach((val2, index2) => {
          if (val2.enName === sessionStorage.getItem('dyncShowField')) {
            val2.isDisabled = false
          }
        })
      })
      console.log('cancle')
      console.log(this.selectedField)
      this.index--
    },
    // 获取下拉数据
    getGainList () {
      let data = {}
      data.catalogId = sessionStorage.getItem('sourceId')
      data.createdBy = this.$route.query.createdBy
      data.type = 2
      data.userId = storeUserId.state.userId
      queryFiledListHqOrBd(data).then(res => {
        if (res.code === 100) {
          res.data.forEach((val, index) => {
            val.isDisabled = false
          })
          this.selectList = res.data
          // 组装第一条数据
          this.newBuildFieldList(this.index, this.selectList)
        } else {
          this.$message.error(res.message)
        }
      }).catch(() => {
        this.$message.error('服务器出错!')
      })
    }
  },
  created () {
    this.formInline.resourceName = sessionStorage.getItem('resourceName')
    this.formInline.tableName = sessionStorage.getItem('tableName')
    this.formInline.typeInfo = sessionStorage.getItem('typeInfo')
    this.formInline.isDataCenter = sessionStorage.getItem('isDataCenter')
    this.formInline.resourceId = sessionStorage.getItem('sourceId')
    this.getGainList()
    sessionStorage.removeItem('dyncShowField')
    sessionStorage.removeItem('changeBeforeField')
  },
  mounted () {}
}
</script>
<style>
#gainDetails  div .el-input__inner{
  width: 200px !important;
}
#gainDetails .el-date-editor.el-input {
  width: 200px !important;
}
#gainDetails .el-pagination__sizes .el-input__inner{
  width: 100px !important;
}
#gainDetails .el-pagination__jump .el-input__inner{
  width: 46px !important;
}
#gainDetails .el-button {
  height: 32px;
  width: 72px;
  line-height: 32px;
  padding: 0;
  border: 1px solid #4382e6;
  color: #4382e6;
}
#gainDetails .el-button.reset{
  border: 1px solid #999999;
  color:#999999;
}
#gainDetails .table_title {
  background-color: #eeeeee;
  border-bottom: 1px solid #ddd;
  padding-right: 16px;
  padding-left: 16px;
  height: 56px;
}
#gainDetails .table_title > span {
  line-height: 56px;
  color: #333;
  font-size: 16px;
}
#gainDetails .search_btns{
  float:right;
  margin-right:10%;
}
</style>
