<template>
  <div id="app">
      <tree-select
          :treeData="treeData"
          :treeProps="treeProps"
          v-model="treeSelected"
          :multiple="false"
          placeholder="请选择部门"
          @errorCallback="showTreeError"
          @setSelectedId="setSelectedId">
      </tree-select>
      <tree-select
          :treeData="treeData"
          :treeProps="treeProps"
          v-model="treeSelectArr"
          :multiple="true"
          placeholder="请选择部门"
          @errorCallback="showTreeError"
          @setSelectedId="setSelectedId">
      </tree-select>
  </div>
</template>

<script>
import treeSelect from './components/tree-select/tree.vue'
import department from './department.js'
import {Message} from 'element-ui';

export default {
  name: 'app',
  data () {
    return {
        treeSelected:'',
        treeSelectArr:[],
        treeData:window.department,
        treeProps:{
            label: "name",
            children: "childDepts",
            level:"deptLevel"
        },
    }
  },
  components:{
    treeSelect
  },
  mounted(){
      let i = 75170;
      setInterval(function () {
          i+=1;
          //this.treeSelectArr = [i+1, i+2, i+3];
          //this.treeSelected = i;
      }.bind(this),2000)
  },
  methods:{
    setSelectedId(val){
      if(val instanceof Array){
        this.treeSelectArr = val;
      }else{
        this.treeSelected = val;
      }
    },
    showTreeError(error){
      Message.info(error.message);
    }
  }
}
</script>

<style lang="scss">
  *{
      padding:0;
      margin:0;
  }
  li{
      list-style:none;
  }
  #app{
    margin-top:100px;
  }
  .ats-tree{
    vertical-align: top;
    margin-left:300px;
  }
</style>
