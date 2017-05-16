<template>
    <div class="ats-tree" v-clickoutside="handleCloseTree" ref="atsTree">
        <div class="ats-input">
            <i class="el-input__icon el-icon-caret-bottom"
                :class="{'is-reverse':treeVisible}"
                @click="handleCloseTree(!treeVisible,$event)"></i>
            <input type="text"
                class="el-input__inner"
                v-model="treeSelected"
                :placeholder="placeholder"
                @input="handleFilter"
                @focus="treeVisible=true"
                @blur="handleAutoComplete">
        </div>
        <el-scrollbar v-show="treeVisible" class="ats-tree-scrollbar">
            <div class="ats-tree-wrapper">
                <ul class="ats-tree-nodes">
                    <tree-node
                        v-for="child in treeNodes[treeProps.children]"
                        :node="child"
                        :key="child.id"
                        :currentNodeId="currentNodeId"
                        :treeProps="treeProps"
                        :eventHub="eventHub"
                        :query="query"
                        :isQuering="isQuering">
                    </tree-node>
                </ul>
            </div>
        </el-scrollbar>
    </div>
</template>

<script>
    import Vue from 'vue';
    import {Scrollbar} from 'element-ui';
    import treeNode from "./tree-node.vue";
    import Clickoutside from "../../utils/clickoutside"
    import debounce from "throttle-debounce/throttle"
    import {objArrDeepCopy} from "../../utils/tools"
    Vue.component(Scrollbar.name, Scrollbar);

    export default {
        name: 'tree',
        components:{
            treeNode
        },
        props: {
            treeData:{
                type:Array,
                default:[]
            },
            treeProps:{
                type:Object,
                default:{
                    label:'label',
                    children:'children'
                }
            },
            placeholder:{
                type:String,
                default:'请选择'
            },
            value:{

            }
        },
        created(){
            this.eventHub.$on('node-click',this.handleNodeClick);
        },
        watch:{
            value(val){
                this.setSelected(val);
            },
            treeData(val){
                if(val){
                    this.treeNodes = {
                        [this.treeProps.children]:objArrDeepCopy(val,{visible:true}),
                        visible:true
                    };
                    this.setSelected(this.value);
                }
            }
        },
        data() {
            return {
                treeNodes:{
                    [this.treeProps.children]:objArrDeepCopy(this.treeData,{visible:true}),
                    visible:true
                },
                currentNodeId:'',
                currentSelected:'',
                treeSelected:'',
                treeVisible:false,
                eventHub:new Vue(),
                isQuering:false,
                query:''
            }
        },
        methods: {
            handleCloseTree(val){
                if(val==undefined){
                    this.treeVisible = false;
                }else{
                    this.treeVisible = val;
                }
            },
            handleNodeClick(node){
                this.currentNodeId = node.id;
                this.treeSelected = node[this.treeProps.label];
                this.currentSelected = this.treeSelected;
                this.$emit('setSelectedId',node.id);
            },
            setSelected(val){
                var self = this;
                let param = this.treeProps.children;
                this.treeNodes[param].forEach(function (node) {
                    if(node.id == val){
                        self.handleNodeClick(node);
                    }
                })
            },
            handleFilter:debounce(1000,function () {
                this.isQuering = true;
                if(this.isQuering){
                    this.query = this.treeSelected;
                    this.treeFilterMethod(this.treeNodes);
                }
            }),
            treeFilterMethod(node){
                let self = this;
                let childNodes = node[self.treeProps.children];
                childNodes.forEach((child) =>{
                    child.visible = child[self.treeProps.label].toLowerCase().indexOf(self.query.toLowerCase())>-1;
                    self.treeFilterMethod(child);
                })
                if (!node.visible && childNodes.length) {
                    let allHidden = true;
                    childNodes.forEach((child) => {
                        if (child.visible) allHidden = false;
                    });
                    node.visible = allHidden === false;
                }
                if (node.visible){
                    this.$set(node,'expanded',true);
                    if(self.query===''){
                        node.expanded = false;
                    }
                }
            },
            handleAutoComplete(){
                setTimeout(function () {
                    this.query = '';
                    if(this.currentNodeId){
                        this.treeSelected = this.currentSelected;
                    }else{
                        this.treeSelected = '';
                    }
                    if(this.isQuery){
                        this.treeVisible = false;
                    }
                    this.isQuery = false;
                }.bind(this),250)
            }
        },
        directives: { Clickoutside },
    }
</script>

<style lang="scss">
    .ats-tree{
        display: inline-block;
        position:relative;
        .ats-input{
            position: relative;
            .el-icon-caret-bottom{
                cursor: pointer;
                &.is-reverse{
                    transform: rotateZ(180deg);
                }
            }
        }
        input.el-input__inner{
            width: 360px;
            -webkit-appearance: none;
            -moz-appearance: none;
            appearance: none;
            background-color: #fff;
            background-image: none;
            border-radius: 4px;
            border: 1px solid rgb(191, 204, 217);
            box-sizing: border-box;
            color: rgb(31, 46, 61);
            display: block;
            font-size: inherit;
            height: 36px;
            line-height: 1;
            outline: 0;
            padding: 3px 10px;
            transition: border-color .2s cubic-bezier(.645,.045,.355,1);
        }
        .ats-tree-scrollbar{
            top: 40px;
            min-width: 360px;
            min-height: 300px;
            position: absolute;
            z-index: 1001;
            background: #fff;
            border: 1px solid #d1dbe5;
            .el-scrollbar__view{
                height: 500px;
            }
        }
        .ats-tree-wrapper{
            background-color: #fff;
        }
    }
</style>
