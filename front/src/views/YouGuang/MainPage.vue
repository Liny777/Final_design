/ *

 * 路径分析与关联分析的主界面

* 父组件

* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <el-container class="body">
    <el-header>
      <el-row>
        <el-col :span="4" :offset="1">
          <div>
            <h1 class="text"><span>功能选择</span></h1>
          </div>
        </el-col>
        <el-col :span="8">
          <el-select v-model="value1" filterable placeholder="请选择" style="width:350px;" @change="changefunction">
            <el-option
              v-for="item in options1"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-col>
      </el-row>
    </el-header>
    <el-container>
      <el-aside width="320px">
        <div class="headerup">
          <div v-if="big_show1">
            <el-menu
              :default-active="activeIndex"
              class="el-menu-demo"
              mode="horizontal"
              background-color="#D3DCE6"
              text-color="#333"
              active-text-color="#009966"
              @select="handleSelect1"
            >
              <el-menu-item class="elmenu1" index="1">定向分析</el-menu-item>
              <el-menu-item class="elmenu1" index="2">邻居</el-menu-item>
              <el-menu-item class="elmenu1" index="3">关系</el-menu-item>
            </el-menu>
          </div>
          <div v-if="big_show2">
            <el-menu
              :default-active="activeIndex"
              class="el-menu-demo"
              mode="horizontal"
              background-color="#D3DCE6"
              text-color="#333"
              active-text-color="#009966"
              @select="handleSelect2"
            >
              <el-menu-item class="elmenu1" index="1">定向分析</el-menu-item>
              <el-menu-item class="elmenu1" index="2">邻居</el-menu-item>
              <el-menu-item class="elmenu1" index="3">关系</el-menu-item>
            </el-menu>
          </div>
        </div>
        <div class="footerdown">
          <div v-if="big_show1">
            <div v-show="small_show11" class="item1">
              <path-search-component :catedata="category" />
            </div>
            <div v-show="small_show12" class="item2">
              <path-image-read />
            </div>
            <div v-show="small_show13" class="item3">
              <path-image-analyze />
            </div>
          </div>
          <div v-if="big_show2">
            <div v-show="small_show21" class="item1">
              <relation-search-component :catedata="category" />
            </div>
            <div v-show="small_show22" class="item2">
              <relation-image-read />
            </div>
            <div v-show="small_show23" class="item3">
              <relation-image-analyze />
            </div>
          </div>
          <div v-if="big_show3">
            <div>
              <search-search-component />
            </div>
          </div>
          <div v-if="big_show4">
            <category-left-component />
          </div>
          <div v-if="big_show5">
            <entity-left-component />
          </div>
          <div v-if="big_show6">
            <edge-left-component :edgedata="edge" />
          </div>
        </div>
      </el-aside>
      <el-main>
        <div v-if="big_show1">
          <el-row>
            <el-col :span="21">
              <path-force-chart />
            </el-col>
            <el-col :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="drawer11 = true"> 筛选</i>
                <el-drawer
                  title="我是标题"
                  :visible.sync="drawer11"
                  :with-header="false"
                  :modal-append-to-body="false"
                  :modal="false"
                  size="20%"
                >
                  <path-filter-component />
                </el-drawer>
              </el-row>
              <el-divider />
              <el-row>
                <el-badge is-dot class="item" :hidden="redpoint1">
                  <i class="el-icon-pie-chart" @click="drawer12 = true"> 历史</i>
                  <el-drawer
                    title="我是标题"
                    :visible.sync="drawer12"
                    :with-header="false"
                    :modal-append-to-body="false"
                    :modal="false"
                    size="30%"
                  >
                    <path-history-component />
                  </el-drawer>
                </el-badge>
              </el-row>
              <el-divider />
              <el-row>
                <i class="el-icon-refresh" @click="recover"> 恢复</i>
              </el-row>
              <el-divider />
              <el-row>
                <el-switch
                  v-model="value3"
                  active-text="图标"
                  inactive-text="圆形"
                  @change="change_radio"
                />
              </el-row>
            </el-col>
          </el-row>
        </div>
        <div v-if="big_show2">
          <el-row>
            <el-col :span="21">
              <relation-force-chart />
            </el-col>
            <el-col :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="drawer21 = true"> 筛选</i>
                <el-drawer
                  title="我是标题"
                  :visible.sync="drawer21"
                  :with-header="false"
                  :modal-append-to-body="false"
                  :modal="false"
                  size="20%"
                >
                  <relation-filter-component />
                </el-drawer>
              </el-row>
              <el-divider />
              <el-row>
                <el-badge is-dot class="item" :hidden="redpoint2">
                  <i class="el-icon-pie-chart" @click="drawer22 = true"> 历史</i>
                  <el-drawer
                    title="我是标题"
                    :visible.sync="drawer22"
                    :with-header="false"
                    :modal-append-to-body="false"
                    :modal="false"
                    size="30%"
                  >
                    <relation-history-component />
                  </el-drawer>
                </el-badge>
              </el-row>
              <el-divider />
              <el-row>
                <i class="el-icon-refresh" @click="recover"> 恢复</i>
              </el-row>
              <el-divider />
            </el-col>
          </el-row>
        </div>
        <div v-if="big_show3">
          <el-row>
            <el-col :span="21">
              <search-chart-Component />
            </el-col>
            <el-col v-if="small_show31" :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="Back1"> 返回</i>
              </el-row>
              <el-divider />
            </el-col>
            <el-col v-if="small_show32" :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="Back2"> 返回</i>
              </el-row>
              <el-divider />
            </el-col>
            <el-col v-if="small_show33" :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="Back3"> 返回</i>
              </el-row>
              <el-divider />
            </el-col>
            <el-col v-if="small_show34" :span="3">
              <el-row>
                <i class="el-icon-s-tools" @click="Back4"> 返回</i>
              </el-row>
              <el-divider />
            </el-col>
          </el-row>
        </div>
        <div v-if="big_show4">
          <category-chart-component />
        </div>
        <div v-if="big_show5">
          <entity-right-component />
        </div>
        <div v-if="big_show6">
          <edge-right-chart />
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import axios from 'axios'
// path
import PathForceChart from './components/Path/ForceChartComponent'
import PathImageAnalyze from './components/Path/ImageAnalyze'
import PathImageRead from './components/Path/ImageRead'
import PathHistoryComponent from './components/Path/HistoryComponent'
import PathFilterComponent from './components/Path/FilterComponent'
import PathSearchComponent from './components/Path/SearchComponent'
// relation
import RelationForceChart from './components/Relation/ForceChartComponent'
import RelationImageAnalyze from './components/Relation/ImageAnalyze'
import RelationImageRead from './components/Relation/ImageRead'
import RelationHistoryComponent from './components/Relation/HistoryComponent'
import RelationFilterComponent from './components/Relation/FilterComponent'
import RelationSearchComponent from './components/Relation/SearchComponent'
// search
import SearchSearchComponent from './components/Search/SearchComponent'
import CategoryChartComponent from './components/Search/CategoryChartComponent'
import SearchChartComponent from './components/Search/ForceChartComponent'
import CategoryLeftComponent from './components/Search/CategoryLeftComponent'
import EntityLeftComponent from './components/Search/EntityLeftComponent'
import EntityRightComponent from './components/Search/EntityRightComponent'
// edge
import EdgeLeftComponent from './components/Edge/EdgeLeftComponent'
import EdgeRightChart from './components/Edge/EdgeRightChart'

import { mapActions, mapGetters } from 'vuex'
// 非甾体类抗炎药
export default {
  components: {
    // path
    PathForceChart,
    PathImageAnalyze,
    PathImageRead,
    PathHistoryComponent,
    PathFilterComponent,
    PathSearchComponent,
    // relation
    RelationForceChart,
    RelationImageAnalyze,
    RelationImageRead,
    RelationHistoryComponent,
    RelationFilterComponent,
    RelationSearchComponent,
    // search
    SearchSearchComponent,
    SearchChartComponent,
    CategoryChartComponent,
    CategoryLeftComponent,
    EntityLeftComponent,
    EntityRightComponent,
    // edge
    EdgeLeftComponent,
    EdgeRightChart
  },
  data() {
    return {
      value3: '',
      ForceChartData: {
        id1: '',
        id2: ''
      },
      options1: [{
        value: '1',
        label: '路径分析功能 Path analysis function'
      }, {
        value: '2',
        label: '关联分析功能 Association analysis function'
      },
      {
        value: '3',
        label: '实体查询功能 Entity query function'
      },
      {
        value: '4',
        label: '关系查询功能 Relational query function'
      }],
      value1: '',
      startpoints: [],
      endpoints: [],
      state1: '',
      state2: '',
      size1: 0,
      drawer11: false,
      drawer12: false,
      drawer21: false,
      drawer22: false,
      isshow1: true,
      category: [],
      categories: [],
      graph_length: 0,
      graph_length_start_content: 0,
      graph_length_end_content: 0,
      activeIndex: '1',
      options: [],
      big_show1: false,
      big_show2: false,
      big_show3: false,
      big_show4: false,
      big_show5: false,
      big_show6: false,
      // big_show3path: false,
      // big_show3relation: false,
      small_show11: true,
      small_show12: false,
      small_show13: false,
      small_show21: true,
      small_show22: false,
      small_show23: false,
      small_show31: false,
      small_show32: false,
      small_show33: false,
      small_show34: false,
      NodeData: [{
        id: '',
        name: ''
      }],
      // LinkData: [{
      //   path: ''
      // }],
      startName: '',
      endName: '',
      countnumber: 0,
      EntityOption: [],
      RelationOption: [],
      redpoint1: true,
      redpoint2: true,
      // filterinformation: {}
      EdgeOption: [],
      edge: [],
      edges: []
    }
  },
  computed: {
    ...mapGetters([
      'radio',
      'histories',
      'relation_histories',
      'search_bigshow3',
      'search_bigshow5'
    ])
  },
  watch: {
    search_bigshow5: function(val, oldVal) {
      console.log('search_bigshow5: ' + val)
      if (val === 0) {
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = false
        this.big_show4 = false
        this.big_show5 = true
        this.big_show6 = false
      } else {
        this.big_show5 = false
      }
    },
    search_bigshow3: function(val, oldVal) {
      if (val === 0) {
        // path -> final
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = true //
        this.small_show31 = true //
        this.small_show33 = false
        this.small_show32 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 1) {
        // final -> path
        this.big_show1 = true //
        this.big_show2 = false
        this.big_show3 = false
        this.small_show31 = false
        this.small_show33 = false
        this.small_show32 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 2) {
        // final -> relation
        this.big_show1 = false
        this.big_show2 = true //
        this.big_show3 = false
        this.small_show31 = false
        this.small_show33 = false
        this.small_show32 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 3) {
        // search2 -> search3
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = true
        this.small_show31 = false
        this.small_show32 = true
        this.small_show33 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 4) {
        // search3 -> search1
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = false
        this.small_show31 = false
        this.small_show32 = false
        this.small_show33 = false
        this.big_show4 = true
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 5) {
        // relation -> final
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = true //
        this.small_show31 = false
        this.small_show32 = false
        this.small_show33 = true //
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
      if (val === 6) {
        // final -> edge
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = false
        this.small_show31 = false
        this.small_show32 = false
        this.small_show33 = false
        this.small_show34 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = true
      }
      if (val === 7) {
        // edge -> final
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = true
        this.small_show31 = false
        this.small_show32 = false
        this.small_show33 = false
        this.small_show34 = true
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      }
    },
    histories: function(val, oldVal) {
      console.log('his: ' + val.length)
      if (val.length === 0) {
        this.redpoint1 = true
      } else {
        this.redpoint1 = false
      }
    },
    relation_histories: function(val, oldVal) {
      if (val.length === 0) {
        this.redpoint2 = true
      } else {
        this.redpoint2 = false
      }
    }
  },
  mounted() {
    // if (this.$route.params.userId === 1) {
    //   this.big_show1 = true
    //   this.big_show2 = false
    // }
    // console.log('this.$route.query.useId: ' + this.$route.params.userId)
    this.$nextTick(() => {
      this.loadData_entitytype()
      this.load_edgelabel()
    })
  },
  created() {
    console.log('this.$route.query.useId: ' + this.$route.params.userId)
    if (this.$route.params.userId === 1) {
      this.big_show1 = true
      this.big_show2 = false
      this.big_show3 = false
      this.big_show4 = false
      this.big_show5 = false
      this.big_show6 = false
    }
    if (this.$route.params.userId === 2) {
      this.big_show1 = false
      this.big_show2 = true
      this.big_show3 = false
      this.big_show4 = false
      this.big_show5 = false
      this.big_show6 = false
    }
    if (this.$route.params.userId === 3) {
      this.big_show1 = false
      this.big_show2 = false
      this.big_show3 = false
      this.big_show4 = true
      this.big_show5 = false
      this.big_show6 = false
    }
    if (this.$route.params.userId === 4) {
      this.big_show1 = false
      this.big_show2 = false
      this.big_show3 = false
      this.big_show4 = false
      this.big_show5 = false
      this.big_show6 = true
    }
    // console.log()
    this.$nextTick(() => {
      this.loadData_entitytype()
      this.load_edgelabel()
    })
  },
  // watch: {
  //   this.$route.query.useId:
  // },
  methods: {
    change_radio(data) {
      if (data === true) {
        this.$store.dispatch('path/setRadio', true)
      } else {
        this.$store.dispatch('path/setRadio', false)
      }
    },
    recover() {
      this.$store.dispatch('path/setChosenId', 0)
      this.$store.dispatch('path/setChosenEdge', 0)
      this.$store.dispatch('relation/setChosenId', 0)
      this.$store.dispatch('relation/setChosenEdge', 0)
    },
    ...mapActions(
      {
        // search
        setBigshow3: 'search/setBigshow3',
        search_setBigShow5: 'search/setBigShow5'
      }
    ),
    load_edgelabel() {
      axios.get('http://localhost:10088/MongoDB/getAllEdgeLabel').then((response) => {
        for (var i = 0; i < response.data.length; i++) {
          this.EdgeOption[i] = response.data[i].label
          var edge1 = {}
          edge1['label'] = response.data[i].label
          edge1['value'] = response.data[i].label
          this.edges[i] = edge1
        }
        this.edge = []
        this.edge = this.edges
      })
    },
    loadData_entitytype() {
      axios.get('http://localhost:10088/MongoDB/getEntityType').then((response) => {
        for (var i = 0; i < response.data.length; i++) {
          this.EntityOption[i] = response.data[i].name
          var cate = {}
          cate['label'] = response.data[i].name
          cate['value'] = response.data[i].name
          this.categories[i] = cate
        }
        this.category = []
        this.category = this.categories
      })
    },
    // getfilterinfo(data) {
    //   this.filterinformation = data
    //   console.log('finaldegree: ' + this.filterinformation['maxdegree'])
    // },
    changefunction(value) {
      if (value === '1') {
        this.big_show1 = true
        this.big_show2 = false
        this.big_show3 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      } else if (value === '2') {
        this.big_show1 = false
        this.big_show2 = true
        this.big_show3 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = false
      } else if (value === '3') {
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = false
        this.big_show4 = true
        this.big_show5 = false
        this.big_show6 = false
      } else if (value === '4') {
        this.big_show1 = false
        this.big_show2 = false
        this.big_show3 = false
        this.big_show4 = false
        this.big_show5 = false
        this.big_show6 = true
      }
    },
    handleSelect1(key, keyPath) {
      if (key === '3') {
        this.small_show13 = true
        this.small_show12 = false
        this.small_show11 = false
      }
      if (key === '2') {
        this.small_show13 = false
        this.small_show12 = true
        this.small_show11 = false
      }
      if (key === '1') {
        this.small_show13 = false
        this.small_show12 = false
        this.small_show11 = true
      }
    },
    handleSelect2(key, keyPath) {
      if (key === '3') {
        this.small_show23 = true
        this.small_show22 = false
        this.small_show21 = false
      }
      if (key === '2') {
        this.small_show23 = false
        this.small_show22 = true
        this.small_show21 = false
      }
      if (key === '1') {
        this.small_show23 = false
        this.small_show22 = false
        this.small_show21 = true
      }
    },
    Back1() {
      this.setBigshow3(1)
    },
    Back2() {
      this.setBigshow3(4)
      this.search_setBigShow5(1)
    },
    Back3() {
      this.setBigshow3(2)
    },
    Back4() {
      this.setBigshow3(6)
    }
  }

}
</script>

<style scoped>
 .el-header {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    margin-bottom: 0px;
    height: 575px;
  }

  .el-main {
    background-color:#fff;
     /* #E9EEF3; */
    color: #333;
    text-align: center;
  }
  .el-menu-demo{
      margin-top: 20px;
  }
  .elmenu1{
    width: 80px;
  }
  .headerup{
      height: 50px;
  }
  .footerdown{
       height: 450px;
        text-align: center;
  }
  .block{
    bottom: 0px;
    left: 0px;
  }
  .drawer{
    background-color: #B3C0D1;
  }
  /* .item1{
    margin-top: 20px;
  } */
h1 {
        font-family: "Arial", sans-serif;
        font-size: 30px;
        text-align: center;
        font-weight: bold;
    color: #99CCCC;
    /* margin-top: 0px; */
    }
    .text {
        position:relative;
         margin-top: 0px;
    }
    span {
    text-shadow:
    0 -1px 0 #858585,
    0 1px 10px rgba(0,0,0,.6),
    0 6px 1px rgba(0,0,0,.1),
    0 0 5px rgba(0,0,0,.2),
    0 1px 3px rgba(0,0,0,.3),
    0 3px 5px rgba(0,0,0,.2),
    0 7px 10px rgba(0,0,0,.25),
    0 15px 10px rgba(0,0,0,.2),
    0 25px 15px rgba(0,0,0,.15);
    }
</style>
