<template>
  <div class="add-form">
    <el-dialog
      :title="addFormData.id ? '编辑用户' : '创建用户'"
      :visible="dialogFormVisible"
      @close="handleClose"
    >
      <el-form
        ref="dataForm"
        label-position="left"
        label-width="120px"
        style="width: 400px; margin-left: 120px"
        :model="addFormData"
        :rules="rules"
      >
        <el-form-item :label="$t('table.username')">
          <el-input v-model="addFormData.username"></el-input>
        </el-form-item>
        <el-form-item :label="$t('table.email')" prop="email">
          <el-input v-model="addFormData.email"></el-input>
        </el-form-item>
        <el-form-item
          v-if="!addFormData.id || addFormData.id === 0"
          :label="$t('table.paddword')"
        >
          <el-input v-model="addFormData.password"></el-input>
        </el-form-item>
        <!-- 角色 -->
        <el-form-item :label="$t('table.role')">
          <el-input v-model="addFormData.role"></el-input>
        </el-form-item>
        <!-- 权限组 -->
        <el-form-item :label="$t('table.permissionUser')">
          <el-select
            v-model="addFormData.permission_group_id"
            ref="quanxian"
            placeholder="请选择"
            class="filter-item"
          >
            <el-option
              v-for="item in userList"
              :key="item.id"
              :label="item.permission_group_title"
              :value="item.permission_group_id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item :label="$t('table.phone')">
          <el-input v-model="addFormData.phone"></el-input>
        </el-form-item>
        <el-form-item :label="$t('table.introduction')">
          <el-input
            v-model="addFormData.introduction"
            type="textarea"
            :autosize="{ minRows: 2, maxRows: 4 }"
            placeholder="Please input"
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleClose">{{ $t('table.cancel') }}</el-button>
        <el-button type="primary" @click="createData">{{
          $t('table.confirm')
        }}</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { update, add } from '@/api/base/users'
export default {
  name: 'usersAdd',
  props: [
    'dialogFormVisible',
    'userList'
  ],
  data () {
    return {
      // importFileUrl: 'https://jsonplaceholder.typicode.com/posts/',
      addFormData: {
        username: '',
        password: '',
        role: '',
        phone: '',
        permission_group_id: '',
        avatar: '',
        email: '',
        introduction: ''
      },
      rules: {
        email: [
          { required: true, message: '必填', trigger: 'blur' },
          { pattern: /^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/, message: '邮箱格式不正确', trigger: 'blur' }
        ]
      }
    }
  },
  computed: {},
  methods: {
    // 退出
    handleClose () {
      this.$emit('update:dialogFormVisible', false)
      // console.log(222, this.userList)
      this.$refs.dataForm.resetFields()
      this.addFormData = {
        username: '',
        password: '',
        role: '',
        phone: '',
        permission_group_id: '',
        avatar: '',
        email: '',
        introduction: ''
      }
    },

    // 表单提交
    async createData () {
      // console.log(666, this.addFormData)
      if (this.addFormData.id) {
        this.addFormData.permission_group_title = undefined
        this.addFormData.is_deleted = undefined
        this.addFormData.create_time = undefined
        this.addFormData.last_update_time = undefined
      }
      this.addFormData.id ? await update(this.addFormData) : await add(this.addFormData)
      this.$parent.getUserList()
      this.handleClose()
    }
  },
  // 挂载结束

  mounted: function () { },
  // 创建完毕状态
  created () { },
  // 组件更新
  updated: function () { }
}
</script>
<style>
.el-form--label-left .el-form-item__label {
  text-align: right;
}
.el-form-item--medium {
  margin-bottom: 22px;
}
.el-dialog__footer {
  text-align: center;
}
</style>
