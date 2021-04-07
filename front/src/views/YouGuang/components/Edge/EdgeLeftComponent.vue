/ *

 * 定向分析功能 —— 路径分析功能的子组件

* 定向分析是路径分析功能左侧三个功能的第一个功能，用于选择所要查询路径的起始节点以及终止节点。
* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <el-row class="item1-1">
      <el-col :span="4">关系</el-col>
      <!-- <el-divider /> -->
      <el-col :span="16" :offset="2">
        <el-select v-model="start_cate" clearable filterable placeholder="请选择" @change="select_start_cate">
          <el-option
            v-for="item in edgedata"
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
      <el-divider />
      <el-row>
        <el-col :span="8" :offset="16">
          <el-button type="primary" @click="TransferBack">重置</el-button>
        </el-col>
      </el-row>
    </el-footer>
  </div>
</template>

<script>
// import axios from 'axios'
export default {
  props: {
    edgedata: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      start_cate: '',
      choose_cate: ''
    }
  },
  created() {
  },
  methods: {
    select_start_cate(value) {
      console.log('edgelabel: ' + value)
      this.choose_cate = value
    },
    TransferId() {
      console.log('edgelabel: ' + this.choose_cate)
      this.$store.dispatch('edge/setEdgeLabel', this.choose_cate)
    },
    TransferBack() {
      this.$store.dispatch('edge/setEdgeLabel', '')
    }
  }
}
</script>

<style scoped>
</style>
