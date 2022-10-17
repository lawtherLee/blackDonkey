<template>
  <div class="dashboard-container">
    <el-card style="width: 100%">
      <!-- 表单区域 -->
      <el-form>
        <el-row>
          <el-col class="col-left" :span="6">
            <el-form-item>
              <el-input
                v-model.trim="formData.username"
                placeholder="根据用户名搜索"
              ></el-input>
            </el-form-item>
            <el-form-item style="margin: 0 10px 0 15px">
              <el-button @click="formData.username = ''">清空</el-button>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="getUserList">搜索</el-button>
            </el-form-item>
          </el-col>
          <el-col :push="16" :span="6">
            <el-form-item>
              <el-button type="success" icon="el-icon-edit" @click="addUser"
                >新增用户</el-button
              >
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <!-- 提示信息 -->
      <el-alert :closable="false" type="info" show-icon>
        <template #title> 共{{ formData.counts }}条记录 </template>
      </el-alert>
      <!-- table表格 -->
      <el-table style="margin-top: 20px; width: 100%" :data="userList">
        <el-table-column
          label="序号"
          width="240"
          align="center"
          :formatter="formatterFn"
        >
        </el-table-column>
        <el-table-column label="邮箱" width="220" align="center" prop="email">
        </el-table-column>
        <el-table-column
          label="联系电话"
          width="315"
          prop="phone"
        ></el-table-column>
        <el-table-column
          label="用户名"
          width="215"
          prop="username"
        ></el-table-column>
        <el-table-column
          label="权限组名称"
          width="245"
          prop="permission_group_title"
        ></el-table-column>
        <el-table-column label="角色" width="120" prop="role"></el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="row">
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              @click="addUser(row)"
            ></el-button>
            <el-button
              v-if="row.$index !== 0"
              type="danger"
              icon="el-icon-delete"
              circle
              @click="removeUser(row)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-row type="flex" justify="end" align="middle" style="height: 60px">
        <el-pagination
          layout="prev, pager, next, sizes, jumper"
          :current-page.sync="formData.page"
          :page-sizes="[2, 5, 10]"
          :page-size.sync="formData.pagesize"
          background
          :total="formData.counts"
          @current-change="getUserList"
          @size-change="getUserList"
        >
        </el-pagination>
      </el-row>
    </el-card>
    <UserAdd
      ref="addUserForm"
      :userList="userList"
      :dialogFormVisible.sync="dialogFormVisible"
      :formBase="formBase"
    />
  </div>
</template>

<script>
import { list, remove } from '@/api/base/users.js'
import UserAdd from '../components/user-add.vue'
export default {
  components: { UserAdd },
  data () {
    return {
      formData: {
        page: 1,
        pagesize: 10,
        username: '',
        counts: 0
      },
      userList: [],
      formBase: {},
      dialogFormVisible: false
    }
  },

  created () {
    this.getUserList()
  },

  methods: {
    formatterFn (row, column, cellValue, index) {
      return index + (this.formData.page - 1) * 10 + 1
    },
    addUser (row) {
      this.dialogFormVisible = true
      if (row.row) this.$refs.addUserForm.addFormData = { ...row.row }
    },
    async getUserList () {
      const { data } = await list(this.formData)
      console.log('zhang', data)
      this.userList = data.list
      this.formData.counts = data.counts
    },
    async removeUser (row) {
      console.log(row.row)
      await this.$confirm('是否删除该数据', '提示', {
        type: 'warning'
      })
      await remove(row.row)
      this.getUserList()
    }
  }
}
</script>

<style scoped lang='less'>
.dashboard-container {
  margin: 10px;
  .col-left {
    display: flex;
  }
}
</style>
