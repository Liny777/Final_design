/ *

 * 搜索查询功能 —— 第二页

* 查找想要寻找的实体数据

* @author Lin Youguang

* @version 2.1

* @deprecated

*/
<template>
  <div class="zidian">
    <!-- <el-scrollbar style="height:500px"> -->
    <ul v-for="(item,index) in newArr" :key="index" class="live">
      <div @click="toggleShow(index)">
        <h3 class="tit">{{ item.title }}</h3>
      </div>
      <div v-show="item.show">
        <li v-for="(user,index1) in item.items" :key="index1" class="div" @click="handleSelect(user)">
          {{ user.name }}
        </li>
      </div>
    </ul>
    <!-- </el-scrollbar> -->
  </div>
</template>
<script>
import axios from 'axios'
require('echarts/theme/macarons') // echarts theme
// import resize from '../mixins/resize'
import { mapGetters } from 'vuex'
import vPinyin from '././vue-py.js'
import { mapActions } from 'vuex'
export default {
  data() {
    return {
      newArr: [],
      example: [],
      graph_length: 0
    }
  },
  computed: {
    ...mapGetters([
      'search_node_category'
    ])
  },
  watch: {
    // search_node_category: function(val, oldVal) {
    // }
    // newArr: {
    //   immediate: false,
    //   deep: true,
    //   handler(val) {
    //     this.getGuideData()
    //   }
    // }
  },
  mounted() {
    this.$nextTick(() => {
      this.getGuideData()
    })
  },
  created() {
    this.$nextTick(() => {
      this.getGuideData()
    })
  },
  methods: {
    toggleShow: function(index) {
      const newItem = this.newArr[index]
      newItem.show = !this.newArr[index].show
      this.$set(this.newArr, index, newItem)
      // 一定要注意vue不能检测的几种数据变化情况包括数组和对象
    },
    ...mapActions(
      {
        setBigshow3: 'search/setBigshow3',
        set_Node_Id: 'search/setNodeId',
        set_Node_Label: 'search/setNodeLabel',
        set_Node_Name: 'search/setNodeName' }
    ),
    getGuideData() {
      console.log('getGuideData: ')
      const url = 'http://localhost:10088/LabeltoFindV'
      const category = this.search_node_category
      const data =
          {
            'category': category,
            'content': category
          }
      // if (category !== null && category !== '' && category !== undefined) {
      axios.post(url, data).then((response) => {
        this.newArr = []
        this.example = []
        this.graph_length = response.data.length
        console.log('response: ' + response.data[0]['propertie'].name)
        // 获取数据
        for (var i = 0; i < this.graph_length; i++) {
          this.example.push({ name: response.data[i]['propertie'].name, pinyin: null, index: null, id: response.data[i]['id'] })
        }
        // 转成拼音
        for (var j = 0; j < this.example.length; j++) {
          var receive = this.example[j].name
          if (receive === null || receive === undefined || receive === '' || receive.length === 0) {
            continue
          } else {
            this.example[j].pinyin = vPinyin.chineseToPinYin(this.example[j].name)
            // console.log(this.example[j].pinyin)
            this.example[j].index = this.example[j].pinyin.charAt(0)
          }
        }
        // 进行字典排序
        this.example.sort(function(a, b) {
          return (a.pinyin + '').localeCompare(b.pinyin + '')
        })
        // 分组
        const map = {}
        for (let i = 0; i < 26; i++) {
          const key = String.fromCharCode(65 + i) // A-Z赋给key当作键
          map[key] =
							{ title: key, items: [], show: true }
          this.example.map((v, k) => { // 遍历数组
            // eslint-disable-next-line no-empty
            if (v.pinyin === null || v.pinyin === undefined || v.pinyin === '' || v.pinyin === 0) {

            } else {
              const firstIndex = v.pinyin.charAt(0).toUpperCase()// 首字母
              if (firstIndex.toUpperCase() === String.fromCharCode(65 + i)) { // 统一转成大写进行逐个判断
                // console.log(v.id)
                map[key].items.push({ name: v.name, id: v.id })// push进相对应的数组里头
              }
            }
          })// //如果当前的数组里头为空，则跳过。
          if (map[key].items === undefined || map[key].items.length === 0) { continue } else { this.newArr.push(map[key]) }// 将分类好的每个对象 合并在一个数组里面}
        }
        // for (var x = 0; x < this.newArr.length; x++) {
        //   console.log(this.newArr[x])
        // }
      })
      // }
    },
    handleSelect(item) {
      this.setBigshow3(3)
      this.set_Node_Id(item.id)
      // console.log('param.data.label: ' + param.data.label)
      this.set_Node_Label(this.search_node_category)
      this.set_Node_Name(item.name)
      // var the_choose_node_category = {}
      // the_choose_node_category['name'] = item.name
      // var the_choose_node = {}
      // the_choose_node['name'] = item.name
      // the_choose_node['category'] = 0
      // the_choose_node['label'] = 0
      // the_choose_node['id'] = item.id
      // the_choose_node['symbolSize'] = 30
      // this.nodes = []
      // this.categories = []
      // this.links = []
      // this.nodes[0] = the_choose_node
      // this.categories[0] = the_choose_node_category
      // this.drawLine()
      // console.log('item: ' + item.name)
    }
  }
}
</script>

<style scoped>
/* .zidian {
  background-color: aliceblue;
} */
.tit {
  text-align: center;
  /* margin:0 auto; */
}
ul {
  width: 100%;
  display: inline-block;
  text-align: left;
  vertical-align: top;
  background: #336699;
  /* background: rgba(0, 0, 0, 0.2); */
  /* color: rgba(255, 255, 255, 0.5); */
  color: #FFFFFF;
  border-radius: 5px;
  padding: 0.5em;
  /* margin: 2%; */
  /* box-shadow: 0 0 8px rgba(0, 0, 0, 0.2); */
  border: 0 solid;
  box-shadow: inset 0 0 20px rgba(255, 255, 255, 0);
  outline: 1px solid;
  outline-color: rgba(255, 255, 255, 0.5);
  outline-offset: 0px;
  text-shadow: none;
  transition: all 1250ms cubic-bezier(0.19, 1, 0.22, 1);
}
ul:hover{
  background: #009966;
  border: 1px solid;
  box-shadow: inset 0 0 20px rgba(255, 255, 255, 0.5), 0 0 20px rgba(255, 255, 255, 0.2);
  outline-color: rgba(255, 255, 255, 0);
  outline-offset: 15px;
  text-shadow: 1px 1px 2px #427388;
}
.live {
  overflow: hidden;
  /* list-style-type:none; */
}
li {
  font-size: 15px;
  list-style: none;
  position: relative;
  padding: 0 0 0 2em;
  margin: 0 0 .5em 10px;
  -webkit-transition: .12s;
          transition: .12s;
}
/* .live > */
li::before {
  position: absolute;
  content: '\2022';
  font-family: Arial;
  color: #CCFFFF;
  top: 0;
  left: 0;
  text-align: center;
  font-size: 2em;
  opacity: .5;
  line-height: .75;
  -webkit-transition: .5s;
          transition: .5s;
}
li:hover {
  color: #CCFFFF;
}
li:hover::before {
  -webkit-transform: scale(2);
      -ms-transform: scale(2);
          transform: scale(2);
  opacity: 1;
  text-shadow: 0 0 4px;
  -webkit-transition: .1s;
          transition: .1s;
}
.live > li::before {
  content: '';
  width: 10px;
  height: 10px;
  background: #FFF;
  border-radius: 3px;
  line-height: 0;
  top: .27em;
  left: 5px;
}
li:hover::before {
  -webkit-transform: none;
      -ms-transform: none;
          transform: none;
  border-radius: 5px;
  width: 25px;
  left: -10px;
  background: #BA5353;
}
</style>
