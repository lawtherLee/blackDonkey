<template>
  <div class="container">
    <el-dialog
      :title="title"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <el-form
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="学科名称" prop="subjectName">
          <el-input
            v-model="ruleForm.subjectName"
            placeholder="请输入学科名称"
          ></el-input>
        </el-form-item>
        <!-- 是否显示 -->
        <el-form-item label="是否显示"
          ><el-switch
            v-model="ruleForm.isFrontDisplay"
            active-color="#13ce66"
            inactive-color="#ff4949"
          >
          </el-switch
        ></el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" @click="confirm">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { add, update } from '@/api/hmmm/subjects'
export default {
  props: {
    dialogVisible: {
      type: Boolean,
      default: false,
    },
  },
  data () {
    return {
      isFrontDisplay: true,
      ruleForm: {
        id: undefined,
        subjectName: '',
        isFrontDisplay: true
      },
      rules: {
        subjectName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },

        ],
      }
    };
  },

  computed: {
    title () {
      return this.ruleForm.id ? '修改学科' : '新增学科'

    }
  },

  methods: {
    handleClose () {
      this.$emit('update:dialogVisible', false)
      this.$refs.ruleForm.resetFields()
      this.ruleForm = {
        id: undefined,
        subjectName: '',
        isFrontDisplay: true
      }
    },
    cancel () {
      this.handleClose()
    },

    async confirm () {
      // 通过校验再请求接口
      await this.$refs.ruleForm.validate()
      // 清空表单
      this.ruleForm.id ? await update(this.ruleForm) : await add(this.ruleForm)
      this.$message.success(this.ruleForm.id ? '修改成功' : '新增成功')
      this.handleClose()
      //重新渲染
      this.$parent.$parent.handleCurrentChange()
    }
  }
}
</script>

<style scoped lang='less'></style>
