/ *

 * 图解读 —— 关联分析功能的子组件

* 图解读是关联分析功能左侧三个功能的第三个功能，用于高亮路径，并且显示路径的详细信息。

* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <ul v-for="(item, index) in relation_graphnode" :key="index" class="live" @mouseover="getnode(item.id)" @mouseleave="resetnode()">
      <h4>{{ item.label }}</h4>
      <p>中心点: {{ item.sname }}</p>
      <li>{{ index }}-{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  data() {
    return {
      // currentRow: null
    }
  },
  computed: {
    ...mapGetters([
      'relation_graphnode'
    ])
  },
  watch: {
  },
  created() {
  },
  methods: {
    // recover() {
    //   this.$store.dispatch('relation/setChosenId', 0)
    // },
    getnode(val) {
      // console.log('item.id: ' + val)
      this.$store.dispatch('relation/setChosenId', val)
    },
    resetnode() {
      this.$store.dispatch('relation/setChosenId', 0)
    }
  }
}
</script>

<style scoped>
tr.current-row > td,
    .el-table__body tr:hover > td {
      background:#f19944;
    }
ul {
  width: 100%;
  display: inline-block;
  text-align: left;
  vertical-align: top;
  background: #6699CC;
  /* background: #336699;
  /* background: rgba(0, 0, 0, 0.2); */
  /* color: rgba(255, 255, 255, 0.5); */
  color: #FFFFFF;
  border-radius: 5px;
  padding: 0.5em;
  /* margin: 2%; */
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.2);
}
/* ul:hover{
  /* background: #009966; */
/* }  */
.live {
  overflow: hidden;
  /* list-style-type:none; */
}
.live > li {
  font-size: 15px;
  list-style: none;
  position: relative;
  padding: 0 0 0 2em;
  margin: 0 0 .5em 10px;
  -webkit-transition: .12s;
          transition: .12s;
}
/* .live > */
.live > li::before {
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
.live > li:hover {
  color: #CCFFFF;
}
.live > li:hover::before {
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
.live > li:hover::before {
  -webkit-transform: none;
      -ms-transform: none;
          transform: none;
  border-radius: 5px;
  width: 25px;
  left: -10px;
  background: #BA5353;
}
.title {
color: #FFCC33;
}
</style>
