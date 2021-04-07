/ *

 * 定向分析功能 —— 路径分析功能的子组件

* 定向分析是路径分析功能左侧三个功能的第一个功能，用于选择所要查询路径的起始节点以及终止节点。
* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <el-row class="item1">起点</el-row>
    <!-- <el-divider /> -->
    <el-row class="item1-1">
      <el-col :span="4">类别</el-col>
      <!-- <el-divider /> -->
      <el-col :span="16" :offset="2">
        <el-select v-model="start_cate" clearable filterable placeholder="请选择" @change="select_start_cate">
          <el-option
            v-for="item in catedata"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <!-- <el-divider /> -->
    <el-row class="item1-1">
      <el-col :span="4">内容</el-col>
      <!-- <el-divider /> -->
      <el-col :span="16" :offset="2">
        <el-select v-model="start" clearable filterable placeholder="请选择" @change="select_start_content">
          <el-option
            v-for="item in startContent"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <el-row class="item1">终点</el-row>
    <!-- <el-divider /> -->
    <el-row class="item1-2">
      <el-col :span="4">类别</el-col>
      <!-- <el-divider /> -->
      <el-col :span="16" :offset="2">
        <el-select v-model="end_cate" clearable filterable placeholder="请选择" @change="select_end_cate">
          <el-option
            v-for="item in catedata"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <!-- <el-divider /> -->
    <el-row class="item1-2">
      <el-col :span="4">内容</el-col>
      <!-- <el-divider /> -->
      <el-col :span="16" :offset="2">
        <el-select v-model="end" clearable filterable placeholder="请选择" @change="select_end_content">
          <el-option
            v-for="item in endContent"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <el-divider />
    <el-footer>
      <el-row>
        <el-col :span="8" :offset="16">
          <el-button type="primary" @click="TransferId">预览</el-button>
        </el-col>
      </el-row>
    </el-footer>
  </div>
</template>

<script>
import axios from 'axios'
// import url(https://fonts.googleapis.com/css?family=Open+Sans:400,700,600,300,800);
export default {
  props: {
    catedata: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      start_cate: '',
      end_cate: '',
      category_start: [],
      categories_start: [],
      category_end: [],
      categories_end: [],
      start: '',
      end: '',
      startContent: [],
      endContent: [],
      startId: '',
      endId: ''

    }
  },
  created() {
  },
  methods: {
    select_start_cate(value) {
      // console.log('start_cate: ' + value)
      if (value !== '') {
        const url = 'http://localhost:10088/LabeltoFindV'
        const category1 = value
        const data =
          {
            'category': category1,
            'content': category1
          }
        this.graph_length_start_content = 0
        axios.post(url, data).then((response) => {
          this.example = []
          // 获取数据
          // console.log('111')
          this.graph_length_start_content = response.data.length
          for (var i = 0; i < this.graph_length_start_content; i++) {
            var name = ''
            if (response.data[i]['propertie'].name.length >= 35) {
              name = response.data[i]['propertie'].name.substr(0, 35)
            } else {
              name = response.data[i]['propertie'].name
            }
            this.example.push({ label: name, value: response.data[i]['id'] })
          }
          this.startContent = []
          this.startContent = this.example
        })
      }
    },
    select_end_cate(value) {
      if (value !== '') {
        const url = 'http://localhost:10088/LabeltoFindV'
        const category2 = value
        const data =
          {
            'category': category2,
            'content': category2
          }
        this.graph_length_end_content = 0
        axios.post(url, data).then((response) => {
          this.example1 = []
          // 获取数据
          // console.log('222')
          this.graph_length_end_content = response.data.length
          for (var i = 0; i < this.graph_length_end_content; i++) {
            var name1 = ''
            if (response.data[i]['propertie'].name.length >= 35) {
              name1 = response.data[i]['propertie'].name.substr(0, 35)
            } else {
              name1 = response.data[i]['propertie'].name
            }
            this.example1.push({ label: name1, value: response.data[i]['id'] })
          }
          this.endContent = []
          this.endContent = this.example1
        })
      }
    },
    select_start_content(e) {
      this.startId = e
      this.startContent.map((item) => {
        //  this.unitList 是你 select option遍历的集合 e 是选中的id
        if (item.value === e) {
          this.startName = item.label
        }
      })
    },
    select_end_content(e) {
      this.endId = e
      this.endContent.map((item) => {
        //  this.unitList 是你 select option遍历的集合 e 是选中的id
        if (item.value === e) {
          this.endName = item.label
        }
      })
    },
    TransferId() {
      // this.redpoint = false
      // console.log('this.startId: ' + this.startId)
      // console.log('this.endId: ' + this.endId)
      var transData = {}
      transData.id1 = this.startId
      transData.id2 = this.endId
      // transData.id1 = this.startId
      // transData.id2 = this.endId
      this.ForceChartData = transData
      // console.log(555)
      var nowTime = new Date()
      var month = nowTime.getMonth() + 1// 一定要+1,表示月份的参数介于 0 到 11 之间。也就是说，如果希望把月设置为 8 月，则参数应该是 7。
      var date = nowTime.getDate()
      var seperator1 = '-'// 设置成自己想要的年月日格式：年-月-日
      var seperator2 = ':'// 设置成自己想要的时分秒格式：时:分:秒
      if (month >= 1 && month <= 9) {
        month = '0' + month
      }
      if (date <= 9) {
        date = '0' + date
      }
      var currentDate = nowTime.getFullYear() + seperator1 + month + seperator1 + date + ' ' +
          nowTime.getHours() + seperator2 + nowTime.getMinutes() + seperator2 + nowTime.getSeconds()
      var record = {}
      record.start = this.startName
      record.end = this.endName
      record.time = currentDate

      if (this.startId !== this.endId) {
        this.$store.dispatch('path/addHistoryRecord', record)
        this.$store.dispatch('path/setStartId', this.startId)
        this.$store.dispatch('path/setEndId', this.endId)
      }
    }
  }
}
</script>

<style scoped>
  .item1{
    margin-top: 20px;
    /* font-weight:bold */
  }
</style>
