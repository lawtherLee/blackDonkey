<template>
  <div class="container">
    <el-card class="box-card">
      <el-form label-width="80px">
        <el-form-item label="学科名称">
          <el-input style="width: 240px"></el-input>
          <el-button class="clear">清除</el-button>
          <el-button type="primary">搜索</el-button>
          <el-button type="success " icon="el-icon-edit" class="add"
            >新增学科</el-button
          >
        </el-form-item>
      </el-form>
      <el-alert title="消息一共21条" type="info" :closable="false" show-icon>
      </el-alert>

      <!-- 表 -->
      <el-table :data="tableData" style="width: 100%">
        <el-table-column type="index" label="序号" width="100">
        </el-table-column>
        <el-table-column prop="subjectName" label="学科名称" width="100">
        </el-table-column>
        <el-table-column prop="creator" label="创建者" width="120">
        </el-table-column>
        <el-table-column prop="addDate" label="创建日期"> </el-table-column>
        <el-table-column prop="isFrontDisplay" label="前台是否显示">
        </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="二级目录">
        </el-table-column>
        <el-table-column prop="tags" label="标签"> </el-table-column>
        <el-table-column prop="totals" label="题目数量"> </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="操作">
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import { list } from '../../api/hmmm/subjects'
export default {
  data () {
    return {
      tableData: {},
      page: {
        subjectName: '',
        page: 1,
        pagesize: 10,
      }
    }
  },
  created () {
    this.list()
  },
  methods: {
    async list () {
      const items = list(this.page)
      console.log(items);
      // this.tableData= data
    }
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
