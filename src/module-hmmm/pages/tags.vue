<template>
  <div class="container">
    <el-card class="box-card">
      <el-form label-width="80px" :inline="true" :model="page">
        <el-form-item label="标签名称">
          <el-input style="width: 240px" v-model="page.tagName"></el-input>
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
          @click="addTag"
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
        <el-table-column prop="subjectName" label="所属学科"> </el-table-column>
        <el-table-column prop="tagName" label="标签名称"> </el-table-column>
        <el-table-column prop="username" label="创建者"> </el-table-column>
        <el-table-column prop="addDate" label="创建日期">
          <template slot-scope="{ row }">
            {{ row.addDate | parseTimeByString }}
          </template>
        </el-table-column>

        <el-table-column prop="state" label="状态" :formatter="formatterFn">
        </el-table-column>

        <el-table-column prop="twoLevelDirectory" label="操作" width="250">
          <template slot-scope="{ row }">
            <el-button type="text" @click="start(row)">
              {{ row.state === 1 ? '禁用' : '启用' }}</el-button
            >
            <el-button
              type="text"
              @click="editTag(row)"
              :disabled="row.state == 1 ? false : true"
              >修改</el-button
            >
            <el-button
              type="text"
              @click="delTag(row)"
              :disabled="row.state == 1 ? false : true"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <tags-add ref="editTag" :dialogVisible.sync="dialogVisible" />
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
import { list, remove, changeState } from '@/api/hmmm/tags'
import baseAPI from '@/api/base/baseApi.js'
import tagsAdd from '../../module-hmmm/components/tags-add.vue'
export default {
  components: { tagsAdd },
  data () {
    return {
      status: baseAPI.status,
      tableData: [],
      dialogVisible: false,
      page: {
        state: undefined,
        // tagName: '',
        page: 1,
        pagesize: 10,
        subjectID: undefined
      },
      counts: 0,
      form: {
        tagName: '',
        state: '',
      }
    }
  },
  created () {
    if (this.$route.query.id) this.page.subjectID = this.$route.query.id
    this.handleCurrentChange()

  },
  methods: {
    formatterFn (row, colum, cellValue) {
      // console.log(this.status);
      return this.status.filter(ele => ele.id == cellValue)[0].value
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
    addTag () {
      this.dialogVisible = true
    },
    editTag (row) {
      this.$refs.editTag.ruleForm = { ...row }
      this.dialogVisible = true
    },
    // 删除
    async delTag (id) {
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
    async start (row) {
      row.state === 1 ? row.state = 0 : row.state = 1
      const { data } = await changeState(row)
      this.$message.success("操作成功")
      // console.log(data);
      // console.log(1112, row.id);
      // console.log(222, row);
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
