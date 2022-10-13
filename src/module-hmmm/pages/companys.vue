<template>
  <div class="companysBox">
    <el-card class="box-card">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-row type="flex" justify="space-around">
          <el-col :span="6">
            <el-form-item label="标签名称" label-width="80px">
              <el-input
                v-model="formInline.user"
                placeholder="请输入"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="城市">
              <el-select v-model="formInline.region" placeholder="请输入">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="地区">
              <el-select v-model="formInline.region" placeholder="请输入">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="企业简称" label-width="80px">
              <el-input
                v-model="formInline.user"
                placeholder="请输入"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="6">
            <el-form-item label="状态" label-width="80px">
              <el-select v-model="formInline.region" placeholder="请输入">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="16">
            <el-form-item>
              <el-button>清除</el-button>

              <el-button type="primary" @click="onSubmit">查询</el-button>
            </el-form-item>
          </el-col>
          <el-col :span="2">
            <el-form-item style="padding-left: 30px">
              <el-button type="success" @click="onSubmit">新增用户</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <!-- 记录显示 -->
      <div class="record">
        <i class="el-icon-info"></i>共<span>{{ counts }}</span
        >条数据
      </div>
    </el-card>
    <!-- 表格 -->
    <el-table :data="tableData" style="width: 100%; padding-left: 40px">
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
      <el-table-column prop="address" label="操作">
        <template>
          <el-button
            type="primary"
            circle
            plain
            icon="el-icon-edit"
          ></el-button>
          <el-button
            type="warning"
            icon="el-icon-star-off"
            circle
            plain
          ></el-button>
          <el-button
            type="danger"
            icon="el-icon-delete"
            circle
            plain
          ></el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { list } from '@/api/hmmm/companys'
import { status } from '@/api/hmmm/constants'
export default {
  data () {
    return {
      formInline: {
        user: '',
        region: ''
      },
      page: {
        page: 1,
        pagesize: 10
      },
      tableData: [],
      // 列表总数
      counts: 0,
      status: status
    }
  },
  created () {
    this.companyList()
  },
  methods: {
    // 状态转化
    formatterStatus (row, colum, cellValue, index) {

    },
    // 索引修饰
    formatter (row, colum, cellValue, index) {
      // console.log(this.counts - index);
      return this.counts - index
    },
    onSubmit () {
      console.log('submit!');
    },
    // 企业列表
    async companyList () {
      const { data } = await list(this.page)
      this.tableData = data.items
      this.counts = data.counts
      console.log(data);
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

