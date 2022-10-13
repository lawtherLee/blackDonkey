<template>
  <div class="container">
    <el-card class="box-card">
      <el-form label-width="80px" :model="page">
        <el-form-item label="学科名称">
          <el-input style="width: 240px" v-model="page.subjectName"></el-input>
          <el-button class="clear" @click="clear">清除</el-button>
          <el-button type="primary" @click="Search">搜索</el-button>
          <el-button
            type="success "
            icon="el-icon-edit"
            class="add"
            @click="addSubjet"
            >新增学科</el-button
          >
        </el-form-item>
      </el-form>
      <el-alert type="info" :closable="false" show-icon>
        <span>消息一共{{ counts }}条</span>
      </el-alert>

      <!-- 表 -->
      <el-table :data="tableData" style="width: 100%">
        <el-table-column type="index" label="序号" width="100">
        </el-table-column>
        <el-table-column prop="subjectName" label="学科名称" width="100">
        </el-table-column>
        <el-table-column prop="username" label="创建者" width="120">
        </el-table-column>
        <el-table-column prop="addDate" label="创建日期">
          <template slot-scope="{ row }">
            {{ row.addDate | parseTimeByString }}
          </template>
        </el-table-column>
        <el-table-column
          prop="isFrontDisplay"
          label="前台是否显示"
          width="120px"
        >
          <template slot-scope="scope">
            {{ scope.row.isFrontDisplay === 0 ? '否' : '是' }}
          </template>
        </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="二级目录">
        </el-table-column>
        <el-table-column prop="tags" label="标签"> </el-table-column>
        <el-table-column prop="totals" label="题目数量"> </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="操作" width="250">
          <template slot-scope="{ row }">
            <el-button type="text">学科分类</el-button>
            <el-button type="text">学科标签</el-button>
            <el-button type="text" @click="editSubjet(row)">修改</el-button>
            <el-button type="text" @click="delSubject(row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <Subjectadd ref="editSub" :dialogVisible.sync="dialogVisible" />
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
import { list, remove } from '@/api/hmmm/subjects'
import Subjectadd from '../../module-hmmm/components/subjects-add.vue'
export default {
  components: { Subjectadd },
  data () {
    return {
      tableData: [],
      page: {
        subjectName: '',
        page: 1,
        pagesize: 10,

      },
      dialogVisible: false,
      counts: 0
    }
  },
  created () {
    this.handleCurrentChange()

  },
  methods: {
    async handleCurrentChange () {
      const { data } = await list(this.page)
      this.tableData = data.items
      this.counts = data.counts
    },
    // 搜索
    Search () {

      this.handleCurrentChange(this.page)
    },
    // 清除
    clear () {
      this.page = {}
    },
    // 删除
    async delSubject (id) {
      try {
        await this.$confirm('确定删除该学科', '提示', {
          cancelButtonText: '取消',
          confirmButtonText: '确定',
          type: 'warning'
        })
        await remove(id)
        console.log(id);
        this.handleCurrentChange()
        this.$message.success('删除成功')
      } catch (error) {
        this.$message.error('删除失败')

      }
    },
    // 修改
    editSubjet (row) {
      this.$refs.editSub.ruleForm = { ...row }
      // console.log(this.$refs.editSub.ruleForm);
      this.dialogVisible = true
    },
    // 新增
    async addSubjet () {
      this.dialogVisible = true
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
