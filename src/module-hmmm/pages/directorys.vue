<template>
  <div class="container">
    <el-card class="box-card">
      <el-form label-width="80px" :inline="true" :model="page">
        <el-form-item label="目录名称">
          <el-input
            style="width: 240px"
            v-model="page.directoryName"
          ></el-input>
        </el-form-item>
        <!-- 状态 -->
        <el-form-item label="状态">
          <el-select v-model="page.state" placeholder="请选择">
            <!-- <el-option label="启用" value="qiyong"></el-option>
            <el-option label="禁用" value="jinyong"></el-option> -->
          </el-select>
        </el-form-item>
        <el-button class="clear" @click="clear"> 清除</el-button>
        <el-button type="primary" @click="Search">搜索</el-button>
        <el-button type="success " icon="el-icon-edit" class="add"
          >新增学科</el-button
        >
      </el-form>
      <el-alert type="info" :closable="false" show-icon>
        <span>消息一共{{ counts }}条</span>
      </el-alert>
      <!-- 表 -->
      <el-table :data="tableData" style="width: 100%">
        <el-table-column type="index" label="序号" width="100">
        </el-table-column>
        <el-table-column prop="subjectName" label="所属学科" width="140">
        </el-table-column>
        <el-table-column prop="directoryName" label="目录名称" width="160">
        </el-table-column>
        <el-table-column prop="username" label="创建者" width="160">
        </el-table-column>
        <el-table-column prop="addDate" label="创建日期" width="160px">
          <template slot-scope="{ row }">
            {{ row.addDate | parseTimeByString }}
          </template>
        </el-table-column>
        <el-table-column prop="totals" label="面试题数量" width="160">
        </el-table-column>
        <el-table-column
          prop="state"
          label="状态"
          width="160"
          :formatter="formatterFn"
        >
        </el-table-column>

        <el-table-column prop="twoLevelDirectory" label="操作" width="250">
          <template>
            <el-button type="text">启用</el-button>
            <el-button type="text">修改</el-button>
            <el-button type="text">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="end" align="middle" style="height: 60px">
        <el-pagination
          @size-change="handleCurrentChange"
          @current-change="handleCurrentChange"
          :current-page.sync="page.page"
          :page-sizes="[5, 10, 15, 20]"
          :page-size.sync="page.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="counts"
        >
        </el-pagination>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import { list, changeState } from '@/api/hmmm/directorys'
import baseAPI from '@/api/base/baseApi.js'

export default {
  data () {
    return {
      status: baseAPI.status,
      tableData: [],
      page: {
        directoryName: '',
        page: 1,
        pagesize: 10,
      },
      counts: 0,
      form: {
        directoryName: '',
        state: '',
      }
    }
  },
  created () {
    this.handleCurrentChange()

  },
  methods: {
    formatterFn (row, colum, cellValue) {
      const res = this.status.find(ele => ele.id === cellValue)
      return res && res.value || '禁用'
    },
    // 清除
    clear () {
      this.page = {}
    },
    async handleCurrentChange () {
      const { data } = await list(this.page)
      this.tableData = data.items
      this.counts = data.counts
    },
    Search () {
      this.handleCurrentChange(this.page)
    },

  }
}
</script>

<style scoped lang='less'>
.container {
  .clear {
    margin-left: 15px;
  }
  .add {
    float: right;
  }
}
</style>
