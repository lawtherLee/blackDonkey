<!-- eslint-disable eqeqeq -->

<template>
  <div class="container">
    <el-card class="box-card">
      <el-form>
        <el-row type="flex" justify="space-between">
          <el-form-item label="关键字" label-width="60px">
            <el-input
              v-model="page.keyword"
              placeholder="根据编号搜索"
            ></el-input>
          </el-form-item>
          <el-form-item>
            <el-button size="small" @click="clearInput">清除</el-button>
            <el-button
              type="primary"
              size="small"
              @click="getQusetionRandomList"
              >搜索</el-button
            >
          </el-form-item>
        </el-row>
      </el-form>
      <!-- 记录显示 -->
      <div class="record">
        <i class="el-icon-info"></i>共<span>{{ counts }}</span
        >条数据
      </div>
      <el-table v-loading="loading" :data="tableData" style="width: 100%">
        <el-table-column prop="id" label="编号" width="230"> </el-table-column>
        <el-table-column
          prop="questionType"
          label="题型"
          width="80"
          :formatter="randomsType"
        >
        </el-table-column>
        <el-table-column label="题目编号" width="230">
          >
          <template slot-scope="{ row }">
            <a
              @click="questionPropups(item)"
              href="#"
              style="display: block; color: blue"
              v-for="(item, index) in row.questionIDs"
              :key="index"
              >{{ item.number }}</a
            >
          </template>
        </el-table-column>
        <el-table-column prop="addTime" label="录入时间" width="230">
        </el-table-column>
        <el-table-column prop="totalSeconds" label="答题时间(s)" width="230">
        </el-table-column>
        <el-table-column prop="accuracyRate" label="正确率（%)">
        </el-table-column>
        <el-table-column prop="userName" label="录入人"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="{ row }">
            <el-button
              @click="delQuestionREandom(row)"
              type="danger"
              icon="el-icon-delete"
              circle
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="space-between">
        <div></div>
        <el-pagination
          @size-change="getQusetionRandomList"
          @current-change="getQusetionRandomList"
          :current-page.sync="page.page"
          :page-sizes="[5, 10, 15, 20]"
          :page-size.sync="page.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="counts"
        >
        </el-pagination>
      </el-row>
    </el-card>
    <el-dialog
      v-if="questionPropup"
      title="题目预览"
      :visible="questionPropup"
      width="50%"
      @close="handleClose"
    >
      <el-row>
        <span class="questionSpan"
          >【题型】 :
          {{ randomsType('a', 'a', questionObj.questionType) }}</span
        >
        <span class="questionSpan">【编号】 : {{ questionObj.number }}</span>
        <span class="questionSpan"
          >【难度】 : {{ questionDifficulty(questionObj.difficulty) }}</span
        >
        <span class="questionSpan">【标签】 : {{ questionObj.tags }}</span>
      </el-row>
      <el-row>
        <span class="questionSpan"
          >【学科】 : {{ questionObj.subjectName }}</span
        >
        <span class="questionSpan"
          >【目录】 : {{ questionObj.directoryName }}</span
        >
        <span class="questionSpan">【方向】 : {{ questionObj.direction }}</span>
      </el-row>
      <hr />
      <el-row>【题干】 :</el-row>
      <div style="color: blue" v-html="questionObj.question"></div>
      <el-row>
        {{ randomsType('a', 'a', questionObj.questionType) }}
        选项：（以下选中的选项为正确答案）
      </el-row>
      <el-form>
        <el-form-item prop="resource">
          <el-radio-group :value="radios">
            <el-radio
              v-for="(item, index) in questionObj.options"
              :key="index"
              :label="item.isRight"
              >{{ `${item.code} : ${item.title}` }}</el-radio
            >
          </el-radio-group>
        </el-form-item>
      </el-form>
      <hr />
      <el-row>
        【参考答案】
        <el-button @click="isShowVideo = true" size="small" type="danger"
          >点击查看答案</el-button
        ></el-row
      >
      <video
        v-if="isShowVideo"
        style="margin-top: 10px; width: 400px"
        controls="controls"
        src="https://v-cdn.zjol.com.cn/277004.mp4"
      ></video>
      <hr />
      【答案解析】<span v-html="questionObj.answer"></span>
      <hr />
      <el-row> 【答案备注】 <span v-html="questionObj.answer"></span></el-row>
      <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose">关 闭</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { questionType, difficulty } from '@/api/hmmm/constants'
import { randoms, removeRandoms, detail } from '@/api/hmmm/questions'
export default {
  name: 'questionsRandoms',
  data () {
    return {
      counts: 0,
      difficulty,
      page: {
        page: 1,
        pagesize: 10,
        keyword: ''
      },
      loading: false,
      tableData: [],
      questionType,
      questionPropup: false,
      questionObj: {},
      radios: 1,
      isShowVideo: false
    }
  },
  created () {
    this.getQusetionRandomList()
  },
  methods: {
    // 关闭弹窗
    handleClose () {
      this.questionPropup = false
      this.isShowVideo = false
    }, // 难度
    questionDifficulty (value) {
      console.log(70.0, value)
      // eslint-disable-next-line eqeqeq
      const res = this.difficulty.find(ele => ele.value == value)
      // console.log(res)
      return res?.label
    },
    // 试题详情
    async questionPropups (item) {
      this.questionPropup = true
      const { data } = await detail(item)
      this.questionObj = data
      // console.log(this.questionObj)
    },
    // 题目编号
    questionINsNumber (row, col, cellData) {
      let questionIDs = ''
      cellData.forEach(ele => {
        questionIDs += ele.number
      })
      return questionIDs
    },
    // 题目类型
    randomsType (row, col, cellData) {
      // console.log(cellData)
      return this.questionType.filter(item => {
        // eslint-disable-next-line eqeqeq
        return item.value == cellData
      })[0]?.label
    },
    // 题测列表
    async getQusetionRandomList () {
      try {
        this.loading = true
        const { data } = await randoms(this.page)
        this.tableData = data.items
        // console.log(this.tableData)
        this.counts = data.counts
      } finally {
        this.loading = false
      }
    },
    // 清除
    clearInput () {
      this.page.keyword = ''
    },
    // 删除组题列表
    async delQuestionREandom (row) {
      // console.log(row)
      try {
        await this.$confirm('是否永久删除', '提示', {
          type: 'warning'
        })
        await removeRandoms(row)
        this.getQusetionRandomList()
        this.$message.success('删除成功')
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style scoped lang='less'>
.container {
  padding: 18px;
}
.record {
  height: 15px;
  padding-left: 20px;
  margin: 10px 0 20px 0;
  font-size: 14px;
  color: #909398;
}

.questionSpan {
  display: inline-block;
  margin: 10px 50px 15px 0;
  // font-size: 16px;
}
.dialog-footer {
  padding: 0;
  margin: 0;
}
</style>
