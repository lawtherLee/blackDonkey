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
        <el-form-item label="所属学科" style="width: 120%">
          <el-select v-model="ruleForm.subjectID" placeholder="请选择">
            <el-option
              v-for="(item, index) in simples"
              :key="index"
              :label="item.label"
              :value="item.value"
              >{{ item.label }}</el-option
            >
          </el-select>
        </el-form-item>
        <el-form-item label="目录名称" prop="tagName">
          <el-input
            v-model="ruleForm.tagName"
            placeholder="请输入目录名称"
          ></el-input>
        </el-form-item>
      </el-form>
      <!-- 是否显示 -->

      <span slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" @click="confirm">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { add, update } from '@/api/hmmm/tags'
import { simple as simpe1 } from '@/api/hmmm/subjects.js'
export default {
  name: "Tag",
  props: {
    dialogVisible: {
      type: Boolean,
      default: false,
    },
  },
  data () {
    return {
      simples: [],
      ruleForm: {
        tagName: '',
        subjectID: ""
      },
      rules: {
        tagName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },

        ],
      }

    };
  },
  created () {
    this.simple()
  },
  computed: {
    title () {
      return this.ruleForm.id ? '修改目录' : '新增目录'
    }
  },
  methods: {
    handleClose () {
      this.$emit('update:dialogVisible', false)
      this.$refs.ruleForm.resetFields()
      this.ruleForm = {

        subjectID: '',
        tagName: ''
      }
    },

    cancel () {
      this.handleClose()
    },
    async simple () {
      // const data = await simple()
      const { data } = await simpe1()
      // console.log(111, data);
      this.simples = data
    },
    // 添加
    async confirm () {
      // 通过校验再请求接口
      this.$refs.ruleForm.validate()
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
