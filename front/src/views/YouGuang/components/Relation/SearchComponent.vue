/ *

 * 定向分析功能 —— 关联分析功能的子组件

* 定向分析是关联分析功能左侧三个功能的第一个功能，用于选择所要查询路径的起始节点以及终止节点。
* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <el-row class="item1">添加对象</el-row>
    <el-row class="item1-1">
      <el-col :span="4">类别</el-col>
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
    <el-row class="item1-1">
      <el-col :span="4">内容</el-col>
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
    <el-divider />
    <el-row>
      <el-col :span="8" :offset="16">
        <el-button type="primary" @click="TransferId">添加</el-button>
      </el-col>
    </el-row>
    <el-divider />
    <!-- <el-row class="item1">删除对象</el-row>
    <el-row class="item1-2">
      <el-col :span="4">选择删除节点名称</el-col>
      <el-col :span="16" :offset="2">
        <el-select v-model="end_cate" clearable filterable placeholder="请选择" @change="select_end_cate">
          <el-option
            v-for="item in endc"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="8" :offset="16">
        <el-button type="primary" @click="DeleteId">删除</el-button>
      </el-col>
    </el-row> -->
    <el-row>
      <el-col :span="8" :offset="16">
        <el-button type="primary" @click="Resetobject">重置</el-button>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'
import { mapGetters } from 'vuex'
export default {
  props: {
    catedata: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      endc: [
        {
          'label': '',
          'value': ''
        }
      ],
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
      deleteId: -1,
      deletename: ''

    }
  },
  computed: {
    ...mapGetters([
      'relation_searchid',
      'relation_searchname'
      // 'relation_deletename',
      // 'relation_deleteindex'
    ])
  },
  watch: {
    relation_searchid: function(val, oldVal) {
      var c = []
      for (var j = 0; j < val.length; j++) {
        var cate = {}
        cate['label'] = val[j].name
        cate['value'] = this.relation_searchname[j].name
        c.push[cate]
      }
      this.endc = []
      this.ednc = c
    }
  },
  created() {
  },
  methods: {
    select_start_cate(value) {
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
    select_start_content(e) {
      this.startId = e
      this.startContent.map((item) => {
        //  this.unitList 是你 select option遍历的集合 e 是选中的id
        if (item.value === e) {
          this.startName = item.label
        }
      })
    },
    // select_end_cate(val) {
    //   for (var i = 0; i < this.endc.length; i++) {
    //     if (this.endc[i].value === val) {
    //       this.deleteId = i
    //       this.deletename = this.endc[i].label
    //     }
    //   }
    // },
    // DeleteId() {
    //   if (this.deleteId !== -1) {
    //     this.$store.dispatch('relation/setDeleteIndex', this.deleteId)
    //     this.$store.dispatch('relation/setDeleteName', this.deletename)
    //     this.$store.dispatch('relation/deleteData', this.relation_deleteindex)
    //   }
    // },
    TransferId() {
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
      record.time = currentDate
      // 防止搜索相同的元素
      if (this.relation_searchid.length === 0) {
        this.$store.dispatch('relation/addHistoryRecord', record)
        this.$store.dispatch('relation/addSearchId', this.startId)
        this.$store.dispatch('relation/addSearchName', this.startName)
      } else {
        var ifsame = 0
        if (this.startId !== '') {
          for (var i = 0; i < this.relation_searchid.length; i++) {
            if (this.startId === this.relation_searchid[i]) {
              ifsame = 1
            }
          }
        } else {
          ifsame = 1
        }
        if (ifsame === 0) {
          this.$store.dispatch('relation/addHistoryRecord', record)
          this.$store.dispatch('relation/addSearchId', this.startId)
          this.$store.dispatch('relation/addSearchName', this.startName)
        }
      }
    },
    Resetobject() {
      this.$store.dispatch('relation/clearSearchId')
      this.$store.dispatch('relation/clearSearchName')
      this.$store.dispatch('relation/clearGraphData')
      this.$store.dispatch('relation/clearNodeData')
      this.$store.dispatch('relation/clearEdgeData')
      this.$store.dispatch('relation/clearCategory')
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
