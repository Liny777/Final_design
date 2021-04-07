/ *

 * 力导图 —— 关联分析功能的子组件

* 关联分析页面中间的力导图

* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div id="mychart" :class="className" :style="{height:height,width:width}" />
</template>
<script>
import echarts from 'echarts'
import axios from 'axios'
require('echarts/theme/macarons') // echarts theme
import resize from '../mixins/resize'
import { mapGetters } from 'vuex'
import { mapActions } from 'vuex'
import { Loading } from 'element-ui'
// import { Handler } from 'mockjs'
// import yaopin from '@/assets/ForceChart_images/yaopin.png'
// import yaowu from '@/assets/ForceChart_images/yaowu.png'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '800px'
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
      graph_categories: [],
      graph_nodes: [],
      graph_links: [],
      final_links_second: [],
      final_links_first: [],
      final_nodes: [],
      graph_length: 0,
      graph: {},
      chart: null,
      EntityOption: [],
      RelationOption: [],
      state2: '',
      timeout: null,
      loading: null
    }
  },
  computed: {
    ...mapGetters([
      'relation_chosennodelabel',
      'relation_chosenedgelabel',
      'relation_chosennode',
      'relation_chosenedge',
      'relation_searchid',
      'relation_graphdata',
      'relation_searchname',
      'relation_category'
      // 'relation_deleteindex',
      // 'relation_deletename'
      // 'relation_graphnode',
      // 'relation_graphedge'
    ])
  },
  watch: {
    relation_searchid: function(val, oldVal) {
      if (val.length === 0) {
        this.final_links_second = []
        this.final_nodes = []
        this.initChart()
      } else {
        this.loadData()
      }
      console.log('searchid: ' + val)
    },
    // relation_graphedge: {
    //   immediate: false,
    //   deep: true,
    //   handler(val) {
    //     console.log('我来过watchedge')
    //     this.initChart()
    //   }

    // },
    relation_chosennode: function(val, oldVal) {
      if (val === 0) {
        this.initChart()
      } else {
        if (this.chart !== null) {
          this.handleNode()
        }
      }
    },
    relation_chosenedge: function(val, oldVal) {
      if (val === 0) {
        this.initChart()
      } else {
        if (this.chart !== null) {
          this.handleEdge()
        }
      }
    },
    relation_chosennodelabel: {
      immediate: false,
      deep: true,
      handler(val) {
        this.clearData()
      }
    },
    relation_chosenedgelabel: {
      immediate: false,
      deep: true,
      handler(val) {
        this.clearData()
      }
    }
  },
  created() {
    this.loadData_entitytype()
    this.loadData_edgelabel()
  },
  mounted() {
    this.$nextTick(() => {
      this.loadData()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    // this.chart = null
  },
  methods: {
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
    ...mapActions(
      { setnodelabel: 'relation/setNodeLabel',
        setedgelabel: 'relation/setEdgeLabel',
        setChosenNodeLabel: 'relation/setChosenNodeLabel',
        setChosenEdgeLabel: 'relation/setChosenEdgeLabel',
        setBigshow3: 'search/setBigshow3',
        set_Node_Id: 'search/setNodeId',
        set_Node_Label: 'search/setNodeLabel',
        set_Node_Name: 'search/setNodeName',
        setCategory: 'relation/setCategory',
        addCategory: 'relation/addCategory' }
    ),
    loadData_entitytype() {
      axios.get('http://localhost:10088/MongoDB/getEntityType').then((response) => {
        for (var i = 0; i < response.data.length; i++) {
          this.EntityOption[i] = response.data[i].name
        }
        this.setnodelabel(this.EntityOption)
        this.setChosenNodeLabel(this.EntityOption)
      })
    },
    loadData_edgelabel() {
      axios.get('http://localhost:10088/MongoDB/getEdgeLabel').then((response) => {
        for (var i = 0; i < response.data.length; i++) {
          this.RelationOption[i] = response.data[i].name
        }
        this.setedgelabel(this.RelationOption)
        this.setChosenEdgeLabel(this.RelationOption)
      })
    },
    loadData() {
      // 保证每次申请都是最新的那个id
      if (this.relation_searchid.length >= 1) {
        var num = 0
        num = this.relation_searchid.length - 1
        console.log('我想要的id: ' + this.relation_searchid[num])
        const request = {
          'source': this.relation_searchid[num],
          'max_depth': 1
        }
        const url = 'http://localhost:10088//transver/neighbour'
        console.log('source: ' + request.source)
        console.log('this.relation_graphdata.length - 1 ' + this.relation_graphdata.length)
        // 这里是为了防止多次请求，保证一个id请求一次
        // this.startLoading()
        if (request.source !== null && request.source !== '' && request.source !== undefined && this.relation_searchid.length === (this.relation_graphdata.length + 1)) {
          this.startLoading()
          axios.post(url, request)
            .then((response) => {
              this.endLoading()
              this.$store.dispatch('relation/addGraphData', response.data)
              var ca = {}
              ca['name'] = this.relation_searchname[num]
              ca['index'] = num
              // this.graph_categories.push(ca)
              this.addCategory(ca)
              console.log('1111')
              console.log(response.data)
              this.clearData()
            })
        }
      }
    },
    clearData() {
      this.graph_nodes = []
      for (var i = 0; i < this.relation_graphdata.length; i++) {
        for (var j = 0; j < this.relation_graphdata[i]['node'].length; j++) {
          var node = {}
          node['sname'] = this.relation_searchname[i]
          node['name'] = this.relation_graphdata[i]['node'][j]['name']
          node['category'] = i
          node['label'] = this.relation_graphdata[i]['node'][j]['label']
          node['id'] = this.relation_graphdata[i]['node'][j]['id']
          node['value'] = this.relation_graphdata[i]['node'][j]['name']
          this.graph_nodes.push(node)
        }
      }
      var hash = []
      for (var p = 0; p < this.graph_nodes.length; p++) {
        for (var q = p + 1; q < this.graph_nodes.length; q++) {
          if (this.graph_nodes[p]['id'] === this.graph_nodes[q]['id']) {
            ++p
          }
        }
        hash.push(this.graph_nodes[p])
      }
      // this.$store.dispatch('relation/concatNodeData', this.unique4(this.graph_nodes))
      // console.log('this.graphnode.len: ' + this.relation_graphnode.length)
      this.graph_links = []
      for (var m = 0; m < this.relation_graphdata.length; m++) {
      // var m = this.relation_graphdata.length - 1
        for (var n = 0; n < this.relation_graphdata[m]['path'].length; n++) {
          var link = {}
          link['cate'] = m
          link['sourceLabel'] = this.relation_graphdata[m]['path'][n]['headLabel']
          link['targetLabel'] = this.relation_graphdata[m]['path'][n]['tailLabel']
          link['label'] = this.relation_graphdata[m]['path'][n]['relation']
          link['source'] = this.relation_graphdata[m]['path'][n]['headId']
          link['target'] = this.relation_graphdata[m]['path'][n]['tailId']
          link['des'] = this.relation_graphdata[m]['path'][n]['description']
          link['headName'] = this.relation_graphdata[m]['path'][n]['headName']
          link['tailName'] = this.relation_graphdata[m]['path'][n]['tailName']
          // link['description'] = this.relation_graphdata[m]['path'][n]['description']
          this.graph_links.push(link)
        }
      }

      // 根据实体标签筛选边
      this.final_links_first = []
      // final_links = this.graph_links
      for (var y = 0; y < this.graph_links.length; y++) {
        for (var g = 0; g < this.relation_chosennodelabel.length; g++) {
          if (this.graph_links[y]['sourceLabel'] === this.relation_chosennodelabel[g] || this.graph_links[y]['targetLabel'] === this.relation_chosennodelabel[g]) {
            this.final_links_first.push(this.graph_links[y])
          }
        }
      }
      // 根据边标签筛选边
      this.final_links_second = []
      for (var r = 0; r < this.final_links_first.length; r++) {
        for (var t = 0; t < this.relation_chosenedgelabel.length; t++) {
          if (this.final_links_first[r]['label'] === this.relation_chosenedgelabel[t]) {
            this.final_links_second.push(this.final_links_first[r])
          }
        }
      }
      // 根据实体标签筛选节点
      this.final_nodes = []
      for (var x = 0; x < hash.length; x++) {
        for (var h = 0; h < this.relation_chosennodelabel.length; h++) {
          if (hash[x]['label'] === this.relation_chosennodelabel[h]) {
            this.final_nodes.push(hash[x])
          }
        }
      }
      this.$store.dispatch('relation/setNodeData', hash)
      this.$store.dispatch('relation/setEdgeData', this.graph_links)
      this.initChart()
    },
    initChart() {
      this.chart = echarts.init(document.getElementById('mychart'), 'macarons')
      var option = {
        title: {
          top: 'bottom',
          left: 'right'
        },
        tooltip: {
          trigger: 'item',
          enterable: true,
          formatter: function(x) {
            var keydes = '<div style="height:300px;width:300px;overflow-y:auto;overflow-x:auto;"">'
            keydes = keydes + x.data.des + '</div>'
            return keydes
          }
        },
        legend: [{
          show: true
        }],
        animation: false,
        series: [
          {
            name: 'Les Miserables',
            type: 'graph',
            layout: 'force',
            // data: this.relation_graphnode,
            data: this.final_nodes.map(function(node) {
              // console.log('node' + node.name)
              return {
                id: node.id,
                name: node.name,
                label: node.label,
                category: node.category,
                // symbol: `image://${yaopin}`,
                itemStyle: { // ===============图形样式，有 normal 和 emphasis 两个状态。normal 是图形在默认状态下的样式；emphasis 是图形在高亮状态下的样式，比如在鼠标悬浮或者图例联动高亮时。
                  normal: { // 默认样式
                    label: {
                      show: true
                    },
                    borderType: 'solid', // 图形描边类型，默认为实线，支持 'solid'（实线）, 'dashed'(虚线), 'dotted'（点线）。
                    borderColor: 'rgba(255,215,0,0.4)', // 设置图形边框为淡金色,透明度为0.4
                    borderWidth: 0, // 图形的描边线宽。为 0 时无描边。
                    opacity: 1
                  },
                  emphasis: { // 高亮状态
                    label: {
                      show: true
                    },
                    opacity: 1
                  }
                }
              }
            }),
            links: this.final_links_second.map(function(edge) {
              return {
                source: edge.source,
                label: edge.label,
                target: edge.target,
                lineStyle: {
                  normal: {
                    color: 'rgba(30,144,255,0.4)',
                    width: '3',
                    type: 'dotted', // 线的类型 'solid'（实线）'dashed'（虚线）'dotted'（点线）
                    // curveness: 0.3, // 线条的曲线程度，从0到1
                    opacity: 1
                    // 图形透明度。支持从 0 到 1 的数字，为 0 时不绘制该图形。默认0.5
                  },
                  emphasis: { // 高亮状态
                    color: '#FFCC33',
                    opacity: 1
                  }
                }
              }
            }),
            // links: this.relation_graphedge,
            // categories: this.graph_categories,
            categories: this.relation_category,
            roam: true, // 是否开启鼠标缩放和平移漫游。默认不开启。如果只想要开启缩放或者平移，可以设置成 'scale' 或者 'move'。设置成 true 为都开启
            nodeScaleRatio: 0.6, // 鼠标漫游缩放时节点的相应缩放比例，当设为0时节点不随着鼠标的缩放而缩放
            label: { // =============图形上的文本标签
              normal: {
                show: true, // 是否显示标签。
                position: 'bottom', // 标签的位置。['50%', '50%'] [x,y]
                textStyle: { // 标签的字体样式
                  color: '#000000', // 字体颜色
                  fontStyle: 'normal', // 文字字体的风格 'normal'标准 'italic'斜体 'oblique' 倾斜
                  // fontWeight: 'bolder', // 'normal'标准'bold'粗的'bolder'更粗的'lighter'更细的或100 | 200 | 300 | 400...
                  fontFamily: 'sans-serif', // 文字的字体系列
                  fontSize: 15 // 字体大小
                }
              },
              emphasis: {// 高亮状态

              }
            },
            draggable: true,
            symbolSize: 20,
            focusNodeAdjacency: true, // -----
            force: {
              repulsion: 300, // 节点之间的斥力因子。支持数组表达斥力范围，值越大斥力越大。
              gravity: 0.01, // 节点受到的向中心的引力因子。该值越大节点越往中心点靠拢。
              edgeLength: 350, // 边的两个节点之间的距离，这个距离也会受 repulsion。[10, 50] 。值越小则长度越长
              layoutAnimation: true
            },
            legendHoverLink: true, // 是否启用图例 hover(悬停) 时的联动高亮。
            hoverAnimation: true, // 是否开启鼠标悬停节点的显示动画
            edgeSymbol: ['circle', 'arrow'], // -----
            edgeSymbolSize: 10, // -----
            edgeLabel: {
              normal: {
                show: true,
                formatter: function(x) {
                  return x.data.label
                }
              }

            }
          }
        ]
      }
      this.chart.on(
        'click',
        (param) => {
          // 可以使用下面的方式进行路由的切换
          // alert(param.data.id)
          this.setBigshow3(5)
          this.set_Node_Id(param.data.id)
          this.set_Node_Label(param.data.label)
          this.set_Node_Name(param.data.name)
        }
      )
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
        this.chart.setOption(option)
      }, 1000)
      // this.chart.setOption(option)
    },
    handleNode() {
      const options = this.chart.getOption()
      const nodesOption = options.series[0].data
      for (const m in nodesOption) {
        nodesOption[m].itemStyle.opacity = 0.3
        if (nodesOption[m].id === this.relation_chosennode) { // 先比较节点是否在
          nodesOption[m].itemStyle.opacity = 1
          nodesOption[m].itemStyle.color = '#FFCC00'
        }
      }
      this.chart.setOption(options)
    },
    handleEdge() {
      const options = this.chart.getOption()
      const nodesOption = options.series[0].data
      const linksOption = options.series[0].links
      for (const l in linksOption) {
        linksOption[l].lineStyle.opacity = 0.1
      }
      // for (const n in this.relation_chosenedge) {
      for (const p in linksOption) {
        if (linksOption[p].source === this.relation_chosenedge['source'] && linksOption[p].target === this.relation_chosenedge['target']) {
          linksOption[p].lineStyle.opacity = 1
          linksOption[p].lineStyle.color = '#FFCC00'
        }
      }
      // }
      // //////////////////////////////////
      for (const s in nodesOption) {
        nodesOption[s].itemStyle.opacity = 0.1
      }
      // for (const t in this.relation_chosenedge) {
      for (const w in nodesOption) {
        if (nodesOption[w].id === this.relation_chosenedge['source'] || nodesOption[w].id === this.relation_chosenedge['target']) {
          nodesOption[w].itemStyle.opacity = 1
          nodesOption[w].itemStyle.color = '#FFCC00'
        }
      }
      // }
      this.chart.setOption(options)
    }
    // handleradio() {
    //   const options = this.chart.getOption()
    //   const nodesOption = options.series[0].data

    //   for (const k in nodesOption) {
    //     console.log('k: ' + k)
    //     if (k === '0' || k === '1') {
    //       nodesOption[k].symbol = `image://${yaopin}`
    //       nodesOption[k].symbolSize = 70
    //     } else {
    //       nodesOption[k].symbol = `image://${yaowu}`
    //       nodesOption[k].symbolSize = 30
    //     }
    //   }
    //   this.chart.setOption(options)
    // }
  }
}
</script>
