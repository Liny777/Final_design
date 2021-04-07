<template>
  <!-- <div class="left">
    <p class="source_header">Source Name</p>
    <p class="sourceName" v-text="search_nodename">SourceName</p>
    <p class="source_header">Source Label</p>
    <p class="sourceId" v-text="search_nodelabel">SourceLabel</p>
  </div> -->
  <div class="searchcomponent">
    <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark">类别: {{ search_nodelabel }}</div></el-col>
    </el-row>
    <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark">姓名: {{ search_nodename }}</div></el-col>
    </el-row>
    <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark"><el-row>
        <el-cascader
          ref="refSubCat"
          v-model="value"
          class="choose"
          :options="search_options"
          :props="{ expandTrigger: 'hover'}"
          @change="handleChange"
        />
      </el-row></div></el-col>
    </el-row>
    <el-row>
      <el-col :span="24"><div class="grid-content bg-purple-dark"><el-scrollbar style="height:280px;overflow-x: hidden;">
        <ul v-for="(item, index) in graphdes" :key="index" class="live">
          <li>{{ item }}</li>
          <!-- <h4>{{ item.label }}</h4> -->
          <!-- <li>{{ index }}-{{ item.name }}</li> -->
        </ul>
      </el-scrollbar></div></el-col>
    </el-row>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
export default {
  data() {
    return {
      value: [],
      graphdes: []
    }
  },
  computed: {
    ...mapGetters([
      'search_nodeid',
      'search_nodename',
      'search_nodelabel',
      'search_options'
    ])
  },
  watch: {
    // graphdes: {
    //   immediate: false,
    //   deep: true,
    //   handler(val) {
    //   }
    // }
  },
  created() {
  },
  methods: {
    handleChange(val) {
      var m = val.toString().split(',')
      var rela = m[0]
      var child = m[1]
      for (var n = 0; n < this.search_options.length; n++) {
        if (this.search_options[n].value === rela) {
          for (var l = 0; l < this.search_options[n].children.length; l++) {
            if (this.search_options[n]['children'][l].label === child) {
              console.log('success')
              var xx = this.search_options[n].children[l].description
              var ll = xx.split('<br/>')
              this.graphdes = []
              for (var j = 0; j < ll.length - 1; j++) {
                this.graphdes.push(ll[j])
              }
              // console.log('des: ' + xx)
            }
          }
        }
      }
      // if (val !== '[]') {
      //   var info = '<div style="height:300px;overflow-y:auto">'
      //   var i = 0
      //   while (i < val.length) {
      //     var p_key = val.indexOf('key=', i)
      //     var p_dou = val.indexOf(',', p_key)
      //     var keya = val.substring(p_key + 4, p_dou)
      //     var p_value = val.indexOf('value=', p_dou)
      //     var p_kuo = val.indexOf('}', p_value)
      //     var valuea = val.substring(p_value + 6, p_kuo)
      //     if (p_key === -1) {
      //       i = i + 1
      //     } else {
      //       i = p_kuo + 1
      //       info = info + '<strong>' + keya + ': ' + '</strong>' + '<p>' + valuea + '</p>' + '<hr />'
      //     }
      //   }
      //   info = info + '</div>'
      // }
    }
  }
}
</script>
<style scoped>
.el-row {
    margin-bottom: 20px;
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
  .choose {
    width: 100%;
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
<style>
.el-scrollbar__wrap
{
  overflow-x: hidden;
}
</style>
