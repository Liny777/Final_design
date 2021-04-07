/ *

 * 过滤筛选功能 —— 路径分析功能的子组件

* 路径分析页面右侧右上角的过滤筛选功能，用于筛选最大的路径长度，实体类别以及关系类别。

* @author Lin Youguang

* @version 1.1

* @deprecated

*/
<template>
  <div>
    <el-row class="drawer1_title">设定分析路长</el-row>
    <el-row class="drawer1_content">
      <el-radio-group v-model="radio" @change="getMaxdegree">
        <el-radio :label="3">3</el-radio>
        <el-radio :label="4">4</el-radio>
        <el-radio :label="5">5</el-radio>
        <el-radio :label="6">6</el-radio>
      </el-radio-group>
    </el-row>
    <el-divider />
    <el-row class="drawer2_title">设定分析主体</el-row>
    <el-row class="drawer2_content">
      <el-checkbox-group v-model="checkedEntity" @change="handleCheckedEntityChange">
        <el-checkbox v-for="e in nodelabel" :key="e" class="checkbox" :label="e">{{ e }}</el-checkbox>
      </el-checkbox-group>
    </el-row>
    <el-divider />
    <el-row class="drawer3_title">设定分析关系</el-row>
    <el-row class="drawer3_content">
      <el-scrollbar style="height:100%">
        <el-checkbox-group v-model="checkedEdge" @change="handleCheckedRelationChange">
          <el-checkbox v-for="edge in edgelabel" :key="edge" class="checkbox" :label="edge">{{ edge }}</el-checkbox>
        </el-checkbox-group>
      </el-scrollbar>
    </el-row>
    <el-divider />
    <!-- <el-footer>
      <el-row>
        <el-col :span="8" :offset="16">
          <el-button type="primary" @click="sendmsg">预览</el-button>
        </el-col>
      </el-row>
    </el-footer> -->
  </div>
</template>

<script>
// import { validEmail } from '@/utils/validate'
// import axios from 'axios'
import { mapGetters, mapActions } from 'vuex'
export default {
  // props: {
  //   entity: {
  //     type: Array,
  //     required: true
  //     // default: '1'
  //   },
  //   edgelabel: {
  //     type: Array,
  //     required: true
  //   }
  // },
  data() {
    return {
      // maxdegree: 3,
      radio: '1',
      // EntityOption: [],
      // RelationOption: [],
      // isIndeterminate: true,
      checkedEntity: [],
      entities: [],
      relations: [],
      bechosenentity: [],
      bechosenedgelabel: this.chosenedgelabel,
      checkedEdge: [],
      filtercondition: {
        choseEntity: [],
        choseEdge: []
        // maxdegree: 3
      }
    //   finaldegree: 3
    }
  },
  computed: {
    ...mapGetters([
      'nodelabel',
      'edgelabel',
      'chosenedgelabel'
    ])
  },
  created() {
    this.checkedEntity = this.nodelabel
    this.checkedEdge = this.edgelabel
  },
  methods: {
    ...mapActions(
      { setChosenNodeLabel: 'path/setChosenNodeLabel',
        setChosenEdgeLabel: 'path/setChosenEdgeLabel',
        setMaxDegree: 'path/setMaxDegree' }),
    handleCheckedEntityChange(value) {
      this.setChosenNodeLabel(value)
      // console.log('选中的值:' + Object.prototype.toString.call(value))
      // console.log('选中的实体标签：' + value)
      // this.bechosenentity = value
    },
    handleCheckedRelationChange(value) {
      // console.log('this.bechosenedgelabel' + value)
      // this.bechosenedgelabel = value
      this.setChosenEdgeLabel(value)
    },
    getMaxdegree(value) {
      // this.maxdegree = value
      // console.log('maxdegree: ' + this.maxdegree)
      // console.log(value)
      this.setMaxDegree(value)
    },
    sendmsg() {
      // console.log('sendmsg' + this.bechosenedgelabel)
      // this.setChosenNodeLabel(this.bechosenentity)
      // this.setChosenEdgeLabel(this.bechosenedgelabel)
      // this.setMaxDegree(this.maxdegree)
    }
  }
}
</script>

<style scoped>
.drawer1_title{
    /* padding-left: -20px; */
    height: 20px;
    margin-top: 20px;
    margin-left: -100px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
  .drawer2_title{
 height: 20px;
 margin-top: 20px;
    margin-left: -100px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
  .drawer3_title{
 height: 20px;
 margin-top: 20px;
    margin-left: -100px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
  .drawer1_content{
    height: 30px;
    margin-top: 20px;
    margin-left: 10px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
   .drawer2_content{
     height: 120px;
margin-top: 20px;
    margin-left: 10px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
   .drawer3_content{
     height: 260px;
margin-top: 20px;
    margin-left: 10px;
    margin-right: 10px;
    margin-bottom: 20px;
  }
  .checkbox{
    float: left;
  }
</style>
