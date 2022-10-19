<template>
  <div class="container">
    <el-card class="box-card">
      <el-form label-width="80px">
        <el-form-item>
          <el-button type="success " icon="el-icon-edit" class="add"
            >新增学科</el-button
          >
        </el-form-item>
      </el-form>
      <el-row type="flex" class="row-bg">
        <el-form label-width="80px" inline class="from" :model="form">
          <el-col :span="6">
            <el-form-item label="学科">
              <el-select
                v-model="form.subjectID"
                placeholder="请选择活动区域"
                @change="OnchangeSub"
              >
                <el-option
                  v-for="item in simples"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="二级目录">
              <el-select v-model="form.catalog" placeholder="请选择">
                <el-option
                  v-for="item in direSimples"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6"
            ><el-form-item label="标签">
              <el-select v-model="form.node" placeholder="请选择">
                <el-option
                  v-for="item in tagSimples"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="关键字">
              <el-input
                placeholder="根据题干搜索"
                v-model="form.keyword"
              /> </el-form-item
          ></el-col>

          <!--  -->
          <el-col :span="6">
            <el-form-item label="试题类型">
              <el-select v-model="form.type" placeholder="请选择">
                <el-option label="单选" value="shanghai"></el-option>
                <el-option label="多选" value="beijing"></el-option>
                <el-option label="简答" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="难度">
              <el-select v-model="form.difficulty" placeholder="请选择活动区域">
                <el-option label="简单" value="shanghai"></el-option>
                <el-option label="一般" value="beijing"></el-option>
                <el-option label="困难" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="方向">
              <el-select v-model="form.direction" placeholder="请选择活动区域">
                <el-option
                  v-for="(item, index) in direction"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="录入人">
              <el-select v-model="form.people" placeholder="请选择活动区域">
                <el-option label="超级管理员" value="shanghai"></el-option>
                <el-option label="录入管理员" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="题目备注">
              <el-input v-model="form.title" /> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="企业简称">
              <el-input v-model="form.firm" /> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="城市">
              <el-select
                placeholder="请选择"
                style="width: 105px"
                v-model="form.city"
                @change="Onchange"
              >
                <el-option
                  v-for="(item, index) in provinces1"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
              <el-select
                placeholder="请选择"
                v-model="form.cityTwo"
                style="width: 105px; margin-left: 10px"
              >
                <el-option
                  v-for="(item, index) in city"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>

          <el-form class="btn">
            <el-button class="clear" @click="clear">清除</el-button>
            <el-button type="primary" @click="Search">搜索</el-button></el-form
          >
        </el-form>
      </el-row>
      <!-- <el-alert type="info" :closable="false" show-icon>
        <span>消息一共{{ counts }}条</span></el-alert
      > -->
      <!-- tab表格 -->
      <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
        <el-alert type="info" :closable="false" show-icon>
          <span>消息一共{{ counts }}条</span></el-alert
        >
        <el-tab-pane label="全部" name="first">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="number" label="试题编号" width="150">
            </el-table-column>
            <el-table-column prop="subject" label="学科" width="120">
            </el-table-column>
            <el-table-column prop="catalog" label="目录" width="120">
            </el-table-column>
            <el-table-column
              prop="questionType"
              label="题型"
              width="120"
              :formatter="formatterFn"
            >
            </el-table-column>
            <el-table-column prop="question" label="题干" width="250">
              <template slot-scope="scope">
                {{ delHtmlTag(scope.row.question) }}
              </template>
            </el-table-column>
            <el-table-column prop="addDate" label="录入时间" width="150">
              <template slot-scope="{ row }">
                {{ row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column
              prop="difficulty"
              label="难度"
              width="120"
              :formatter="formaterdifficulty"
            >
            </el-table-column>
            <el-table-column prop="creator" label="录入人" width="120">
            </el-table-column>
            <el-table-column
              prop="chkState"
              label="审核状态"
              width="120"
              :formatter="formatterchkType"
            >
            </el-table-column>
            <el-table-column prop="chkRemarks" label="审核意见" width="120">
            </el-table-column>
            <el-table-column prop="chkUser" label="审核人" width="100">
            </el-table-column>
            <el-table-column
              prop="publishState"
              label="发布状态"
              width="200"
              :formatter="formatterpublishType"
            >
            </el-table-column>

            <el-table-column fixed="right" label="操作" width="250">
              <template class="btns" slot-scope="{ row }">
                <el-button type="text" class="btns">预览</el-button>
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >审核</el-button
                >
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >修改</el-button
                >
                <el-button type="text" class="btns" @click="putaway(row)">{{
                  row.publishState === 1 ? '已上架' : '已下架'
                }}</el-button>
                <el-button
                  type="text"
                  class="btns"
                  @click="del(row)"
                  :disabled="row.publishState === 1 ? false : true"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="待审核" name="second">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="number" label="试题编号" width="150">
            </el-table-column>
            <el-table-column prop="subject" label="学科" width="120">
            </el-table-column>
            <el-table-column prop="catalog" label="目录" width="120">
            </el-table-column>
            <el-table-column
              prop="questionType"
              label="题型"
              width="120"
              :formatter="formatterFn"
            >
            </el-table-column>
            <el-table-column prop="question" label="题干" width="250">
              <template slot-scope="scope">
                {{ delHtmlTag(scope.row.question) }}
              </template>
            </el-table-column>
            <el-table-column prop="addDate" label="录入时间" width="150">
              <template slot-scope="{ row }">
                {{ row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column
              prop="difficulty"
              label="难度"
              width="120"
              :formatter="formaterdifficulty"
            >
            </el-table-column>
            <el-table-column prop="creator" label="录入人" width="120">
            </el-table-column>
            <el-table-column
              prop="chkState"
              label="审核状态"
              width="120"
              :formatter="formatterchkType"
            >
            </el-table-column>
            <el-table-column prop="chkRemarks" label="审核意见" width="120">
            </el-table-column>
            <el-table-column prop="chkUser" label="审核人" width="100">
            </el-table-column>
            <el-table-column
              prop="publishState"
              label="发布状态"
              width="200"
              :formatter="formatterpublishType"
            >
            </el-table-column>

            <el-table-column fixed="right" label="操作" width="250">
              <template class="btns" slot-scope="{ row }">
                <el-button type="text" class="btns">预览</el-button>
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >审核</el-button
                >
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >修改</el-button
                >
                <el-button type="text" class="btns" @click="putaway(row)">{{
                  row.publishState === 1 ? '已上架' : '已下架'
                }}</el-button>
                <el-button
                  type="text"
                  class="btns"
                  @click="del(row)"
                  :disabled="row.publishState === 1 ? false : true"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="已审核" name="third">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="number" label="试题编号" width="150">
            </el-table-column>
            <el-table-column prop="subject" label="学科" width="120">
            </el-table-column>
            <el-table-column prop="catalog" label="目录" width="120">
            </el-table-column>
            <el-table-column
              prop="questionType"
              label="题型"
              width="120"
              :formatter="formatterFn"
            >
            </el-table-column>
            <el-table-column prop="question" label="题干" width="250">
              <template slot-scope="scope">
                {{ delHtmlTag(scope.row.question) }}
              </template>
            </el-table-column>
            <el-table-column prop="addDate" label="录入时间" width="150">
              <template slot-scope="{ row }">
                {{ row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column
              prop="difficulty"
              label="难度"
              width="120"
              :formatter="formaterdifficulty"
            >
            </el-table-column>
            <el-table-column prop="creator" label="录入人" width="120">
            </el-table-column>
            <el-table-column
              prop="chkState"
              label="审核状态"
              width="120"
              :formatter="formatterchkType"
            >
            </el-table-column>
            <el-table-column prop="chkRemarks" label="审核意见" width="120">
            </el-table-column>
            <el-table-column prop="chkUser" label="审核人" width="100">
            </el-table-column>
            <el-table-column
              prop="publishState"
              label="发布状态"
              width="200"
              :formatter="formatterpublishType"
            >
            </el-table-column>

            <el-table-column fixed="right" label="操作" width="250">
              <template class="btns" slot-scope="{ row }">
                <el-button type="text" class="btns">预览</el-button>
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >审核</el-button
                >
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >修改</el-button
                >
                <el-button type="text" class="btns" @click="putaway(row)">{{
                  row.publishState === 1 ? '已上架' : '已下架'
                }}</el-button>
                <el-button
                  type="text"
                  class="btns"
                  @click="del(row)"
                  :disabled="row.publishState === 1 ? false : true"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table></el-tab-pane
        >
        <el-tab-pane label="已拒绝" name="fourth">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="number" label="试题编号" width="150">
            </el-table-column>
            <el-table-column prop="subject" label="学科" width="120">
            </el-table-column>
            <el-table-column prop="catalog" label="目录" width="120">
            </el-table-column>
            <el-table-column
              prop="questionType"
              label="题型"
              width="120"
              :formatter="formatterFn"
            >
            </el-table-column>
            <el-table-column prop="question" label="题干" width="250">
              <template slot-scope="scope">
                {{ delHtmlTag(scope.row.question) }}
              </template>
            </el-table-column>
            <el-table-column prop="addDate" label="录入时间" width="150">
              <template slot-scope="{ row }">
                {{ row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column
              prop="difficulty"
              label="难度"
              width="120"
              :formatter="formaterdifficulty"
            >
            </el-table-column>
            <el-table-column prop="creator" label="录入人" width="120">
            </el-table-column>
            <el-table-column
              prop="chkState"
              label="审核状态"
              width="120"
              :formatter="formatterchkType"
            >
            </el-table-column>
            <el-table-column prop="chkRemarks" label="审核意见" width="120">
            </el-table-column>
            <el-table-column prop="chkUser" label="审核人" width="100">
            </el-table-column>
            <el-table-column
              prop="publishState"
              label="发布状态"
              width="200"
              :formatter="formatterpublishType"
            >
            </el-table-column>

            <el-table-column fixed="right" label="操作" width="250">
              <template class="btns" slot-scope="{ row }">
                <el-button type="text" class="btns">预览</el-button>
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >审核</el-button
                >
                <el-button
                  type="text"
                  class="btns"
                  :disabled="row.publishState === 1 ? false : true"
                  >修改</el-button
                >
                <el-button type="text" class="btns" @click="putaway(row)">{{
                  row.publishState === 1 ? '已上架' : '已下架'
                }}</el-button>
                <el-button
                  type="text"
                  class="btns"
                  @click="del(row)"
                  :disabled="row.publishState === 1 ? false : true"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table></el-tab-pane
        >
      </el-tabs>

      <!-- 分页 -->
      <el-row type="flex" justify="end" align="middle" style="height: 60px">
        <el-pagination
          @size-change="handleCurrentChange"
          @current-change="handleCurrentChange"
          :current-page.sync="form.page"
          :page-sizes="[5, 10, 15, 20]"
          :page-size.sync="form.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="counts"
        >
        </el-pagination>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import { choice, remove, choicePublish } from '../../api/hmmm/questions'
import { simple } from '../../api/hmmm/subjects'
import { simple as tagSimple } from '../../api/hmmm/tags'
import { simple as direSimple } from '../../api/hmmm/directorys'
import { datas, provinces, citys } from '../../api/hmmm/citys'
import { questionType, difficulty, chkType, publishType, direction } from '../../api/hmmm/constants.js'

export default {
  name: 'question',
  data () {
    return {
      questionType: questionType,
      difficulty: difficulty,
      chkType: chkType,
      publishType: publishType,
      activeName: 'first',
      page: {
        page: 1,
        pagesize: 10,
        // chkState: '',
      },
      form: {
        chkState: undefined,
        subjectID: '',
        keyword: '',
        subject: '',
        catalog: '',
        node: '',
        type: '',
        difficulty: '',
        direction: '',
        people: '',
        title: '',
        firm: '',
        city: '',
        cityTwo: '',
      },
      tableData: [],

      provinces1: '',
      counts: 0,
      simples: [],
      city: [],
      direction: [],
      direSimples: [],
      tagSimples: []
    }

  },
  created () {
    this.handleCurrentChange()
    this.simple()

    // console.log(citys('北京市'))
    // console.log(direction);
    // const provinces1 = provinces(datas)
    // console.log(provinces1);
    // console.log(provinces(area));
  },
  methods: {

    formatterFn (row, colum, cellValue) {
      // console.log(111, cellValue);
      if (row.questionType == 1) {
        return '单选'
      } else if (row.questionType == 2) {
        return '多选'
      }
      return '简答'

    },
    formaterdifficulty (row, colum, cellValue) {
      if (row.difficulty == 1) {
        return '简单'
      } else if (row.difficulty == 2) {
        return '一般'
      }
      return '困难'
    },
    formatterchkType (row, colum, cellValue) {
      if (row.chkState == 0) {
        return '待审核'
      } else if (row.chkState == 1) {
        return '已审核'
      }
      return '已拒绝'
    },
    formatterpublishType (row, colum, cellValue) {
      if (row.publishState == 1) {
        return '待发布'
      } else if (row.publishState == 2) {
        return '已发布'
      }
      return '已下架'
    },
    async handleClick (tab) {
      // this.tableData.chkState
      if (tab.index === '0') {
        // this.page.chkState = 2
        this.page = {}
        console.log(this.page);
        const data = await choice(this.page)
        this.tableData = data.items
        this.handleCurrentChange()
      } else if (tab.index === '1') {
        this.page.chkState = 0
        const data = await choice(this.page)
        this.tableData = data.items
        this.handleCurrentChange()
        // console.log(data)
        // console.log('222');
      } else if (tab.index === '2') {
        // console.log(444)
        this.page.chkState = 1
        const data = await choice(this.page)
        this.tableData = data.items
        this.handleCurrentChange()
      } else {
        this.page.chkState = 2
        const data = await choice(this.page)
        this.tableData = data.items
        this.handleCurrentChange()
      }


    },
    async handleCurrentChange () {
      const { data } = await choice(this.page)
      // console.log(data);
      this.tableData = data.items
      this.provinces1 = provinces(datas)

      this.counts = data.counts
      this.direction = direction

    },
    async simple () {
      const { data } = await simple()
      this.simples = data
    },
    async Search () {
      const str = { ...this.page, ...this.form }
      // console.log(str)
      const { data } = await choice(str)
      this.tableData = data.items
      this.counts = data.counts



    },
    async del (id) {
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
    clear () {
      this.form = {}
      this.handleCurrentChange()
    },
    delHtmlTag (str) {
      return str.replace(/<[^>]+>/g, '').replace(/&nbsp;/ig, '')
    },
    async putaway (row) {
      // row.publishState === 1 ? row.publishState = 0 : row.publishState = 1
      if (row.publishState === 1) {
        await this.$confirm('您确认下架这道题目吗?', '提示', {
          cancelButtonText: '取消',
          confirmButtonText: '确定',
          type: 'warning'
        })
        row.publishState === 1 ? row.publishState = 0 : row.publishState = 1


        this.$message.success('下架成功')
      } else {
        await this.$confirm('您确认上架这道题目吗?', '提示', {
          cancelButtonText: '取消',
          confirmButtonText: '确定',
          type: 'warning'
        })
        row.publishState === 1 ? row.publishState = 0 : row.publishState = 1

        this.$message.success('上架成功')
      }

      await choicePublish(row)
      this.handleCurrentChange()
      // // console.log(id);



    },
    Onchange () {

      this.city = citys(this.form.city)
    },
    async OnchangeSub () {

      this.subjectID = this.form.subjectID


      const { data } = await direSimple({
        subjectID: this.subjectID
      })
      this.direSimples = data


      const res = await tagSimple({
        subjectID: this.subjectID
      })

      this.tagSimples = res.data
      this.form.catalog = ''
    }

  }

}

</script>

<style scoped lang='less'>
.container {
  .add,
  .btn {
    float: right;
  }

  .box-card {
    margin: 11px 15px;
  }
  .btns {
    font-size: 12px;
  }
}
</style>

