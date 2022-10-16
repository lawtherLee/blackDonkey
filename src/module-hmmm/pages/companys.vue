<template>
  <div class="companysBox">
    <el-card class="box-card">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-row type="flex" justify="space-around">
          <el-col :span="6">
            <el-form-item label="标签名称" label-width="80px">
              <el-input v-model="pages.tags" placeholder="请输入"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="城市" prop="province">
              <el-select
                class="filter-item"
                v-model="pages.province"
                @keyup.enter="handleFilter"
                @change="handleProvince"
                filterable
              >
                <el-option
                  v-for="item in citySelect.cityList"
                  :key="item"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="地区" prop="city">
              <el-select
                class="filter-item"
                v-model="pages.city"
                @keyup.enter="handleFilter"
                filterable
              >
                <el-option
                  v-for="item in citySelect.districtList"
                  :key="item"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="企业简称" label-width="80px">
              <el-input
                v-model="pages.shortName"
                placeholder="请输入"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="6">
            <el-form-item label="状态" label-width="80px">
              <el-select v-model="pages.state" placeholder="请输入">
                <el-option label="启用" :value="1"></el-option>
                <el-option label="禁用" :value="0"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="16">
            <el-form-item>
              <el-button @click="emptyInput">清除</el-button>
              <el-button type="primary" @click="companyList">查询</el-button>
            </el-form-item>
          </el-col>
          <el-col :span="2">
            <el-form-item style="padding-left: 30px">
              <el-button type="success" @click="addCompanys"
                >新增用户</el-button
              >
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <!-- 记录显示 -->
      <div class="record">
        <i class="el-icon-info"></i>共<span>{{ counts }}</span
        >条数据
      </div>

      <!-- 表格 -->
      <el-table
        v-loading="loading"
        :data="tableData"
        style="width: 100%; padding-left: 40px"
      >
        <el-table-column
          prop="index"
          label="序号"
          width="80"
          :formatter="formatter"
        >
        </el-table-column>
        <el-table-column prop="number" label="企业编号" width="180">
        </el-table-column>
        <el-table-column prop="shortName" label="企业简称"> </el-table-column>
        <el-table-column prop="tags" label="标签"> </el-table-column>
        <el-table-column prop="creatorID" label="创建者"> </el-table-column>
        <el-table-column prop="addDate" label="创建日期"> </el-table-column>
        <el-table-column prop="remarks" label="备注"> </el-table-column>
        <el-table-column prop="state" label="状态" :formatter="formatterStatus">
        </el-table-column>
        <el-table-column prop="address" width="180" label="操作">
          <template slot-scope="{ row }">
            <el-button
              @click="editcompany(row)"
              type="primary"
              circle
              plain
              icon="el-icon-edit"
            ></el-button>
            <el-button
              @click="changeState(row)"
              type="warning"
              :icon="row.state === 1 ? 'el-icon-check' : 'el-icon-close'"
              circle
              plain
            ></el-button>
            <el-button
              @click="delcompany(row)"
              type="danger"
              icon="el-icon-delete"
              circle
              plain
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-row type="flex" justify="space-between">
        <div></div>
        <el-pagination
          @size-change="companyList"
          @current-change="companyList"
          :current-page.sync="pages.page"
          :page-sizes="[2, 5, 10, 15]"
          :page-size.sync="pages.pagesize"
          layout="sizes, prev, pager, next,total,jumper"
          :total="counts"
        >
        </el-pagination>
      </el-row>
    </el-card>
    <companysAdd :titleInfo="titleInfo" ref="companysAdd"></companysAdd>
  </div>
</template>

<script>
import { list, disabled, remove } from '@/api/hmmm/companys'
import { citys } from '@/api/hmmm/citys.js'
import companysAdd from '../components/companys-add.vue'
import { status } from '@/api/hmmm/constants'
export default {
  components: { companysAdd },
  data () {
    return {
      formInline: {
        user: '',
        region: ''
      },
      loading: false,
      pages: {
        page: 1,
        pagesize: 10,
        tags: undefined,
        province: undefined,
        city: undefined,
        shortName: undefined,
        state: undefined
      },
      tableData: [],
      // 地区列表2
      citySelect: {

        // 市级列表
        cityList: [],
        // 区级列表
        districtList: []
      },

      // 列表总数
      counts: 0,
      status: status,
      titleInfo: {
        text: '创建',
        pageTitle: '用户'

      }

    }
  },
  created () {
    this.companyList()
  },
  methods: {
    // 状态转化
    formatterStatus (row, colum, cellValue, index) {
      return this.status.filter(item => {
        return item.value === cellValue
      })[0].label
    },
    // 索引修饰
    formatter (row, colum, cellValue, index) {
      // console.log(this.counts - index);
      return this.counts - ((this.pages.page - 1) * this.pages.pagesize + index)
    },
    onSubmit () {
      console.log('submit!')
    },
    // 企业列表
    async companyList () {
      try {
        this.loading = true
        const { data } = await list(this.pages)
        this.tableData = data.items
        this.counts = data.counts
      } finally {
        this.loading = false
      }
    },
    // 选省获取到市
    handleProvince: function (e) {
      this.citySelect.districtList = citys(e)
      this.pages.city = this.citySelect.districtList[0]
    },
    // 新增企业数据
    addCompanys () {
      this.titleInfo.text = '新增'
      this.$refs.companysAdd.dialogFormV()
    },
    // 初始化数据
    clearpages (page) {
      this.pages = {
        page: page || 1,
        pagesize: 10,
        tags: undefined,
        province: undefined,
        city: undefined,
        shortName: undefined,
        state: undefined
      }
    },
    // 清空
    emptyInput () {
      const page = this.pages.page
      this.clearpages(page)
      this.companyList()
    },
    // 编辑·企业信息

    editcompany (row) {
      row.isFamous === 1 ? row.isFamous = true : this.isFamous = false
      this.titleInfo.text = '编辑'

      this.$refs.companysAdd.formBase = row

      console.log(this.$refs.companysAdd.formBase)
      this.$refs.companysAdd.dialogFormV()
    },
    // 改便状态
    async changeState (row) {
      console.log(89, row)
      try {
        await this.$confirm(`是否改变${row.state === 1 ? '禁止' : '启用'}状态`, '提示',
          {
            type: 'warning'
          })
        row.state === 1 ? row.state = 0 : row.state = 1
        await disabled(row)
        await this.companyList()
        this.$message.success('改便状态成功')
      } catch (error) {
        this.$message.error('改便状态失败')
      }
    },
    // 删除企业
    async delcompany (row) {
      await this.$confirm('是否永久删除用户', '请继续', {
        type: 'warning'
      })
      await remove(row)
      this.$message.success('删除成功')
      this.companyList()
    }
  }
}
</script>
  <style scoped lang = 'less' >
.record {
  height: 15px;
  padding-left: 20px;
  font-size: 14px;
  color: #909398;
}
.companysBox {
  padding: 20px;
  .box-card {
    padding: 20px;
  }
}
/deep/ .el-form-item__content {
  width: 73%;
}
/deep/ .el-form-item {
  width: 100%;
}
/deep/ .el-select {
  width: 100%;
}
</style >
