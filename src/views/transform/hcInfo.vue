<template>
  <div>
    <breadcrumb :list="breadcrumbData"></breadcrumb>
    <div class="search">
      <div>
        <span>院内耗材编码:</span>
        <el-input v-model="YNHCBS" size="mini"></el-input>
      </div>
      <div>
        <span>院内耗材名称:</span>
        <el-input v-model="MATERIALNAME" size="mini"></el-input>
      </div>
      <el-button type="success" plain size="mini" @click="search">搜索</el-button>
      <el-button type="primary" plain size="mini" @click="reset">重置</el-button>
      <el-button :disabled="!searched" type="warning" plain size="mini" @click="back">返回</el-button>
      <!--<el-button type="danger" plain size="mini" @click="download">下载</el-button>-->
    </div>
    <table class="table">
      <tr>
        <td>序号</td>
        <td>HCBS</td>
        <td>商品名</td>
        <td>企业名称</td>
        <td>注册证号/备案号/生产许可证号</td>
        <td>型号</td>
        <td>规格</td>
        <td>最小计量单位</td>
        <td>最小销售单位</td>
        <td>转换系数</td>
        <td>备注</td>
      </tr>
      <tr v-for="(item,index) in tableData" :key="index">
        <td>{{(index+1)+(currentPage - 1)*pageSize}}</td>
        <td>{{item.ynhcbs}}</td>
        <td>{{item.materialname}}</td>
        <td>{{item.factoryname}}</td>
        <td>{{item.regcardnm}}</td>
        <td>{{item.producttype}}</td>
        <td>{{item.productoutlookc}}</td>
        <td>{{item.productunit}}</td>
        <td>{{item.productminpunit}}</td>
        <td>{{item.productmactor}}</td>
        <td></td>
      </tr>
    </table>
    <el-pagination
      class="pagination"
      background
      @current-change="handleCurrentChange"
      :page-size="pageSize"
      layout="prev, pager, next, jumper"
      :current-page.sync="currentPage"
      :pager-count="7"
      :total="total">
    </el-pagination>
    <loading v-if="loading"></loading>
  </div>
</template>

<script>
  import $ from 'jquery'
  import breadcrumb from '../../components/breadcrumb'
  import loading from '../../components/loading'
  import api from '../../api'

  export default {
    components: {
      breadcrumb,
      loading
    },
    data() {
      return {
        breadcrumbData: ['转换数据', '高值医用耗材监测表', '完整信息表'],
        tableData: [],
        currentPage: 1,
        pageSize: 40,
        total: 0,
        loading: false,
        searched:false,
        YNHCBS:'',
        MATERIALNAME:''
      }
    },
    created() {
      this.getTableData(this.currentPage)
    },
    methods: {
      getTableData(page) {
        this.loading = true
        $.get(api.baseUrl + api.getstandhcdict + `${page}/${this.pageSize}`).then((res) => {
          console.log(res)
          this.loading = false
          this.tableData = res.data
          this.total = res.totalNum
        })
      },
      handleCurrentChange(page) {
        this.currentPage = page
        this.searched ? this.getSearchData(page) : this.getTableData(page)
        window.scrollTo(0, 0)
      },
      reset() {
        this.YNHCBS = ''
        this.MATERIALNAME = ''
      },
      search() {
        if (!this.YNHCBS.trim() || !this.MATERIALNAME.trim()) {
          this.$message({
            message: '请输入搜索内容',
            type: 'warning',
            center: true
          })
          return
        }
        this.loading = true
        this.searched = true
        this.currentPage = 1
        this.getSearchData(1)
      },
      download() {
        $.get(api.baseUrl + api.exportExcelHC).then((res) => {
          console.log(res)
        })
      },
      getSearchData(page) {
        $.get(api.baseUrl + api.getstandhcdict + `${page}/${this.pageSize}?YNHCBS=${this.YNHCBS}&MATERIALNAME=${this.MATERIALNAME}`).then((res) => {
          console.log(res)
          this.loading = false
          this.tableData = res.data
          this.total = res.totalNum
        })
      },
      back() {
        window.location.reload()
      }
    }
  }
</script>
