<template>
  <div id="app">
      <tree-select
          ref="tree-select"
          :treeData="treeData"
          :treeProps="treeProps"
          v-model="treeSelected"
          placeholder="请选择部门"
          @setSelectedId="setSelectedId">
      </tree-select>
  </div>
</template>

<script>
import treeSelect from './components/tree-select/tree.vue'
import testData from './data.js'

export default {
  name: 'app',
  data () {
    return {
        treeSelected:'',
        treeData:window.testData,
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
  computed:{

  },
  methods:{
    setSelectedId(id){
        this.treeSelected = id;
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
    margin-left:300px;
  }
</style>
