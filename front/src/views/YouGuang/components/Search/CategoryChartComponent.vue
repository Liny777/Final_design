/ *

 * 搜索查询功能 —— 第一页

* 查找的医学数据类别

* @author Lin Youguang

* @version 2.1

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
// import search from '@/store/modules/search'
// import { Loading } from 'element-ui'
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
      graph_categories: [],
      graph_nodes: [],
      graph_links: [],
      graph_length: 0,
      graph: {},
      chart: null
    }
  },
  computed: {
    ...mapGetters([
    ])
  },
  watch: {
    search_bigshow5: function(val, oldVal) {
      if (val === 1) {
        this.loadData()
      }
    }
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
  created() {
  },
  methods: {
    ...mapActions(
      {
        // search
        search_setBigShow5: 'search/setBigShow5',
        search_setNodeCategory: 'search/setNodeCategory'
      }
    ),
    loadData() {
      axios.get('http://localhost:10088/MongoDB/getEntityType').then((response) => {
      // console.log(response.data)
        this.graph_length = 0
        this.graph_length = response.data.length
        var num = 0

        for (var i = 0; i < this.graph_length; i++) {
        // 类别legend
          var cate = {}
          cate['name'] = response.data[i].name
          // 节点
          var node = {}
          node['name'] = response.data[i].name
          node['category'] = i
          node['symbolSize'] = 50
          this.graph_categories[i] = cate
          this.graph_nodes[i] = node
          // console.log(response.data[0])
          for (var j = 0; j < response.data[i].link.length; j++) {
            var link = {}
            link['source'] = response.data[i].name
            link['target'] = response.data[i].link[j][0]
            this.graph_links[num] = link
            num++
          }
        }
        this.initChart()
      })
    },
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.chart.setOption({
        backgroundColor: '#ffffff',
        animationDurationUpdate: 1500,
        animationEasingUpdate: 'quinticInOut',
        legend: [{ show: true }],
        series: [
          {
            name: 'Les Miserables',
            type: 'graph',
            layout: 'circular',
            circular: {
              rotateLabel: true
            },
            data: this.graph_nodes,
            links: this.graph_links,
            categories: this.graph_categories,
            roam: true,
            label: {
              position: 'right',
              formatter: '{b}',
              show: true,
              textStyle: { fontSize: 16 }
            },
            lineStyle: {
              color: 'source',
              curveness: 0.6,
              width: 3
            }

          }
        ]
      })
      this.chart.on(
        'click',
        (param) => {
          this.search_setBigShow5(0)
          this.search_setNodeCategory(param.name)
          // 可以使用下面的方式进行路由的切换
          // alert(param.name)
          // this.$router.push({
          //   path: 'ForceChart',
          //   query: { category: param.name }
          // })
        }
      )
    }
  }
}
</script>

<style scoped>
</style>
