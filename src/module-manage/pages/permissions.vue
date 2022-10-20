<template>
  <div class="dashboard-container">
    <el-card style="width: 100%">
      <!-- 表单区域 -->
      <el-form>
        <el-row>
          <el-col class="col-left" :span="6">
            <el-form-item>
              <el-input
                v-model.trim="formData.title"
                placeholder="根据用户名搜索"
              ></el-input>
            </el-form-item>
            <el-form-item style="margin: 0 10px 0 15px">
              <el-button @click="formData.title = ''">清空</el-button>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="getPermissionsList"
                >搜索</el-button
              >
            </el-form-item>
          </el-col>
          <el-col :push="16" :span="6">
            <el-form-item>
              <el-button
                type="success"
                icon="el-icon-edit"
                @click="addPermissions"
                >新增权限组</el-button
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
      <el-table
        tooltip-effect="dark"
        style="margin-top: 20px; width: 100%"
        :data="permissionsList"
      >
        <el-table-column type="selection" width="55"> </el-table-column>
        <el-table-column
          label="用户名"
          width="655"
          prop="title"
          align="center"
        ></el-table-column>
        <el-table-column sortable label="日期" width="650" prop="create_date">
          <template slot-scope="{ row }">
            {{ row.create_date | formatDate }}
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="row">
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              @click="addPermissions(row)"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              circle
              @click="removePermissions(row)"
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
          @current-change="getPermissionsList"
          @size-change="getPermissionsList"
        >
        </el-pagination>
      </el-row>
    </el-card>
    <PermissionsAdd
      ref="addPermissionsForm"
      :permissionsList="permissionsList"
      :dialogFormVisible.sync="dialogFormVisible"
      :formBase="formBase"
      :objeditId="userId"
    />
  </div>
</template>

<script>
import { list, remove } from '@/api/base/permissions.js'
import PermissionsAdd from '../components/permissions-add.vue'
export default {
  components: { PermissionsAdd },
  data () {
    return {
      formData: {
        page: 1,
        pagesize: 10,
        username: '',
        counts: 0
      },
      userId: '',
      permissionsList: [],
      formBase: {},
      dialogFormVisible: false
    }
  },

  created () {
    this.getPermissionsList()
  },

  methods: {
    addPermissions (row) {
      this.dialogFormVisible = true
      if (row.row?.id) {
        this.$nextTick(() => {
          this.$children[1].hanldeEditForm()
        })
        this.userId = row.row?.id
      }
      // if (row.row) this.$refs.addPermissionsForm.formBase = row.row
    },
    async getPermissionsList () {
      const { data } = await list(this.formData)
      console.log('zhang', data)
      this.permissionsList = data.list
      this.formData.counts = data.counts
    },
    async removePermissions (row) {
      // console.log(666, row)
      await this.$confirm('是否删除该数据', '提示', {
        type: 'warning'
      })
      await remove(row.row)
      this.getPermissionsList()
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
