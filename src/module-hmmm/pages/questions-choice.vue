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
              <el-select v-model="form.subject" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="二级目录">
              <el-select v-model="form.catalog" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6"
            ><el-form-item label="标签">
              <el-select v-model="form.label" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
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
              <el-select v-model="form.type" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="难度">
              <el-select v-model="form.difficulty" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="方向">
              <el-select v-model="form.direction" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="录入人">
              <el-select v-model="form.people" placeholder="请选择活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
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
              >
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
              <el-select
                placeholder="请选择"
                v-model="form.cityTwo"
                style="width: 105px; margin-left: 10px"
              >
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>

          <el-form class="btn">
            <el-button class="clear">清除</el-button>
            <el-button type="primary">搜索</el-button></el-form
          >
        </el-form>
      </el-row>
      <el-alert type="info" :closable="false" show-icon>
        <span>消息一共{{ counts }}条</span>
      </el-alert>
      <!-- tab表格 -->
      <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
        <el-tab-pane label="全部" name="first">
          <el-table :data="tableData" style="width: 100%" height="250">
            <el-table-column prop="number" label="试题编号" width="150">
            </el-table-column>
            <el-table-column prop="subjectID" label="学科" width="120">
            </el-table-column>
            <el-table-column prop="catalogID" label="目录" width="120">
            </el-table-column>
            <el-table-column prop="questionType" label="题型" width="120">
            </el-table-column>
            <el-table-column prop="question" label="题干" width="250">
            </el-table-column>
            <el-table-column prop="addDate" label="录入时间" width="120">
              <template slot-scope="{ row }">
                {{ row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column prop="difficulty" label="难度" width="120">
            </el-table-column>
            <el-table-column prop="creator" label="录入人" width="120">
            </el-table-column>
            <el-table-column prop="chkState" label="审核状态" width="120">
            </el-table-column>
            <el-table-column prop="chkRemarks" label="审核意见" width="120">
            </el-table-column>
            <el-table-column prop="chkUser" label="审核人" width="120">
            </el-table-column>
            <el-table-column prop="chkState" label="发布状态" width="200">
            </el-table-column>
            <el-table-column fixed="right" label="操作" width="200">
              <template class="btns">
                <el-button type="text" class="btns">预览</el-button>
                <el-button type="text" class="btns">审核</el-button>
                <el-button type="text" class="btns">修改</el-button>
                <el-button type="text" class="btns">下架</el-button>
                <el-button type="text" class="btns">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="待审核" name="second">待审核</el-tab-pane>
        <el-tab-pane label="角色管理" name="third">角色管理</el-tab-pane>
        <el-tab-pane label="已拒绝" name="fourth">定时任务补偿</el-tab-pane>
      </el-tabs>
      <!-- 分页 -->
      <el-row type="flex" justify="end" align="middle" style="height: 60px">
        <el-pagination
          @size-change="choice"
          @current-change="choice"
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
import { choice } from '../../api/hmmm/questions'
export default {
  data () {
    return {
      activeName: 'first',
      form: {
        subject: '',
        catalog: '',
        label: '',
        keyword: '',
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
      page: {
        page: 1,
        pagesize: 10
      },
      counts: 0
    }

  },
  created () {
    this.choice()
  },
  methods: {
    handleClick (tab, event) {
      // console.log(tab, event);
    },
    async choice () {
      const { data } = await choice(this.page)
      console.log(data.items);
      this.tableData = data.items
      this.counts = data.counts

    },

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

