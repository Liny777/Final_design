/ *

 * 力导图 —— 路径分析功能的子组件

* 路径分析页面中间的力导图

* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <div id="mychart" :class="className" :style="{height:height,width:width}" />
  </div>
</template>
<script>
import echarts from 'echarts'
import axios from 'axios'
require('echarts/theme/macarons') // echarts theme
import resize from '../mixins/resize'
import { mapGetters } from 'vuex'
import { mapActions } from 'vuex'
import { Loading } from 'element-ui'
// import func from 'vue-editor-bridge'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '500px'
    },
    autoResize: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      graph_categories: [{
        'name': 'start'
      }, {
        'name': 'end'
      }, {
        'name': 'other'
      }],
      graph_nodes: [],
      graph_links: [],
      links: [],
      nodes: [],
      graph_length: 0,
      graph: {},
      categories: [],
      chart: null,
      EntityOption: [],
      RelationOption: [],
      state2: '',
      timeout: null
    }
  },
  computed: {
    ...mapGetters([
      'edge_label'
    ])
  },
  watch: {
    edge_label: function(val, oldVal) {
      console.log('我来了！1')
      // if (val !== '') {
      this.loadData()
      // }
    }
  },
  created() {
  },
  mounted() {
    // this.$nextTick(() => {
    //   console.log('我来了！2')
    //   this.loadData()
    // })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    ...mapActions(
      {
        // setNodeId: 'edge/setNodeId',
        // setNodeLabel: 'edge/setNodeLabel'
        setBigshow3: 'search/setBigshow3',
        set_Node_Id: 'search/setNodeId',
        set_Node_Label: 'search/setNodeLabel',
        set_Node_Name: 'search/setNodeName'
      }
    ),
    startLoading() {
    // Loading.service(options); options 参数为 Loading 的配置项
    // 使用loading变量来接收Loading.service返回的实例
      this.loading = Loading.service({
        lock: true, // 是否锁定
        text: '拼命加载中...', // 显示在加载图标下方的加载文案
        background: 'rgba(0,0,0,.7)' // 遮罩背景色
      })
    },
    endLoading() {
      this.loading.close()
    },
    loadData() {
      console.log('我来了！3')
      const url = 'http://localhost:10088/getEdgeVertical'
      const data = {
        'category': '',
        'content': ''
      }
      data['category'] = this.edge_label
      this.startLoading()
      axios.post(url, data)
        .then((response) => {
          this.endLoading()
          // this.graphdata = response.data
          this.graph_nodes = []
          this.graph_length = response.data.length
          var count_number = 0
          var repeatid = ''
          // 存储节点，起始边一起，然后先第一步去重
          for (var i = 0; i < this.graph_length; i++) {
            if (response.data[i]['inV'] !== repeatid) {
              repeatid = response.data[i]['inV']
              var node = {}
              node['id'] = response.data[i]['inV']
              node['label'] = response.data[i]['inVLabel']
              node['type'] = 'node'
              this.graph_nodes[count_number] = node
              count_number = count_number + 1
            }
            //
            var node1 = {}
            node1['id'] = response.data[i]['outV']
            node1['label'] = response.data[i]['outVLabel']
            node1['type'] = 'node'
            this.graph_nodes[count_number] = node1
            count_number = count_number + 1
          }
          var len = count_number
          var count = 0
          this.nodes = []
          this.categories = []
          // 第二步去重
          for (var m = 0; m < len; m++) {
            for (var n = m + 1; n < len; n++) {
              if (this.graph_nodes[m]['id'] === this.graph_nodes[n]['id']) {
                n = ++m
              }
            }
            // 存储类别，用节点id做类别的名字，用count指定节点的类别
            var cate = {}
            cate['name'] = this.graph_nodes[m]['id']
            this.graph_nodes[m]['category'] = count
            count++
            this.categories.push(cate)
            this.nodes.push(this.graph_nodes[m])
          }
          // 存储边
          for (var j = 0; j < this.graph_length; j++) {
            var link = {}
            link['sourceLabel'] = response.data[j]['inVLabel']
            link['targetLabel'] = response.data[j]['outVLabel']
            link['source'] = response.data[j]['inV']
            link['target'] = response.data[j]['outV']
            link['label'] = response.data[j]['label']
            link['des'] = response.data[j]['description']
            link['type'] = 'link'
            this.links[j] = link
          }

          this.initChart()
        })
    },
    initChart() {
      // 1、基于准备好的dom，初始化echarts实例
      // const myChart = echarts.init(document.getElementById('myChart1'))
      this.chart = echarts.init(document.getElementById('mychart'), 'macarons')
      // 2、构造图表数据
      var options = {
        title: {
          // text: 'Les Miserables',
          // subtext: 'Default layout',
          top: 'bottom',
          left: 'right'
        },
        tooltip: {
          trigger: 'item',
          formatter: function(x) {
            // console.log("x",x)
            return x.data.des
          }
        },
        legend: [{
          show: false
          // selectedMode: 'single',
          // data: categories.map(function(a) {
          //   return a.name
          // })
        }],
        animation: false,
        series: [
          {
            name: 'Les Miserables',
            type: 'graph',
            layout: 'force',
            // silent: false, // xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
            data: this.nodes.map(function(node) {
              return {
                id: node.id,
                label: node.label,
                category: node.category,
                des: node.label,
                type: node.type
              }
            }),
            links: this.links.map(function(edge) {
              return {
                source: edge.source,
                label: edge.label,
                target: edge.target,
                des: edge.des,
                type: edge.type
              }
            }),
            categories: this.categories,
            nodeScaleRatio: 0.6,
            roam: true,
            label: {
              position: 'bottom',
              formatter: '{b}',
              show: true
            },
            draggable: true,
            focusNodeAdjacency: true, // -----
            force: {
              repulsion: 300, // 节点之间的斥力因子。支持数组表达斥力范围，值越大斥力越大。
              gravity: 0.01, // 节点受到的向中心的引力因子。该值越大节点越往中心点靠拢。
              edgeLength: 350, // 边的两个节点之间的距离，这个距离也会受 repulsion。[10, 50] 。值越小则长度越长
              layoutAnimation: true
            },
            legendHoverLink: true, // 是否启用图例 hover(悬停) 时的联动高亮。
            hoverAnimation: true, // 是否开启鼠标悬停节点的显示动画
            edgeSymbol: ['circle', 'arrow']
          }
        ] }
      this.chart.on(
        'click',
        (param) => {
          // this.setBigshow3(0)
          // this.set_Node_Id(param.data.id)
          // this.set_Node_Label(param.data.label)
          var url1 = 'http://localhost:10088/IdfindName'
          var data1 = {
            'category': '',
            'content': ''
          }
          data1['content'] = param.data.id
          console.log(param.data.id)
          if (param.data.type === 'node') {
            axios.post(url1, data1)
              .then((response) => {
                // console.log('response.data[0]: ' + response.data[0])
                console.log('response.data[0]: ' + response.data)
                this.setBigshow3(7)
                this.set_Node_Id(param.data.id)
                this.set_Node_Label(param.data.label)
                this.set_Node_Name(response.data)
              })
          }
        }
      )
      // 3、绘制图表
      this.chart.showLoading({
        text: 'loading',
        color: '#c23531',
        textColor: '#000',
        maskColor: 'rgba(255, 255, 255, 0.2)',
        zlevel: 0
      })
      setTimeout(() => {
        // setOption前隐藏loading事件
        this.chart.hideLoading()
        this.chart.setOption(options)
      }, 1000)
    }
  }
}
</script>
