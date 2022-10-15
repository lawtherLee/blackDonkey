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
            <el-option label="启用" value="1"></el-option>
            <el-option label="禁用" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-button class="clear" @click="clear"> 清除</el-button>
        <el-button type="primary" @click="Search">搜索</el-button>
        <el-button
          type="success "
          icon="el-icon-edit"
          class="add"
          @click="addDirectorys"
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
          <template slot-scope="{ row }">
            <el-button type="text" @click="start(row)">启用</el-button>
            <el-button type="text" @click="editDire(row)">修改</el-button>
            <el-button type="text" @click="delDire(row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <directorysadd ref="editDire" :dialogVisible.sync="dialogVisible" />
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
import { list, remove, changeState } from '@/api/hmmm/directorys'
import baseAPI from '@/api/base/baseApi.js'
import directorysadd from '../../module-hmmm/components/directorys-add.vue'
export default {
  components: {
    directorysadd
  },
  data () {
    return {
      status: baseAPI.status,
      tableData: [],
      // subjectID: this.tableData,

      rowss: '',
      page: {
        directoryName: '',
        page: 1,
        pagesize: 10,
      },

      dialogVisible: false,
      counts: 0,
      form: {
        state: '',
        id: ''
      },
    }
  },
  created () {
    this.handleCurrentChange()


  },
  methods: {
    formatterFn (row, colum, cellValue) {
      return this.status.filter(ele => ele.id == cellValue)[0].value
    },
    // 清除
    clear () {
      this.page = {}
    },
    async handleCurrentChange () {
      const { data } = await list(this.page)
      // console.log(data.items)
      this.tableData = data.items
      this.counts = data.counts
      console.log(111, this.tableData);


    },
    Search () {
      this.handleCurrentChange(this.page)
    },
    // 新增
    addDirectorys () {
      this.dialogVisible = true
    },
    // 修改 
    editDire (row) {

      this.$refs.editDire.ruleForm = { ...row }
      this.dialogVisible = true
    },
    // 删除 
    async delDire (id) {
      try {
        await this.$confirm('确定删除该学科', '提示', {
          cancelButtonText: '取消',
          confirmButtonText: '确定',
          type: 'warning'
        })
        await remove(id)
        // console.log(id);
        this.handleCurrentChange()
        this.$message.success('删除成功')
      } catch (error) {
        this.$message.error('删除失败')

      }
    },
    // 
    async start (row) {
      
      const data = await changeState(row, this.form)
      console.log(111, row.id);
      console.log(222, data);
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
