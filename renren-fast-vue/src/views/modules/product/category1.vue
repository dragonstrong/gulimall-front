<template>
    <el-tree :data="menus" :props="defaultProps" :expand-on-click-node="false" 
    show-checkbox node-key="catId" :default-expanded-keys="expandedKey">
        <span class="custom-tree-node" slot-scope="{ node, data }">
            <span>{{ node.label }}</span>
            <span>
            <el-button v-if="node.level <=2" type="text" size="mini" @click="() => append(data)">Append</el-button>
            <el-button v-if="node.childNodes.length==0" type="text" size="mini" @click="() => remove(node, data)">Delete</el-button>
            </span>
        </span>
    </el-tree>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

export default {
//import引入的组件需要注入到对象中才能使用
components: {},
props: {},
data() {
    return {
    menus: [],
    expandedKey: [],
    data: [],
    defaultProps: {
        children: 'children',
        label: 'name' // 要显示的字段
    }
    };
},
methods: {
    getMenus(){ // 获取所有菜单（三级缓存）
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'), // 请求的后端接口uri
          method: 'get'
        }).then(({data}) => {
          console.log("成功获取到菜单数据...",data.categories) // 解构数据，只要categories
          this.menus=data.categories;
        })
    },

    append(data) {
        console.log("append",data);
      },

    remove(node, data) { // 删除菜单
    console.log("remove",node,data);
    var ids=[data.catId] // 获取要删除的id列表
    // 弹框提示是否确认删除
    this.$confirm(`是否删除当前菜单【${data.name}】?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            this.$http({
            url: this.$http.adornUrl('/product/category/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            this.$message({
            message: '菜单删除成功',
            type: 'success'
            });
            this.getMenus(); // 删除后重新获取菜单 刷新数据
            // 设置展开删除节点的父节点
            this.expandedKey=[node.parent.data.catId]
          })
        }).catch(()=>{
        });  
     
    }
},

//监听属性 类似于data概念
computed: {},
//监控data中的数据变化
watch: {},
//生命周期 - 创建完成（可以访问当前this实例）
created() {
    this.getMenus(); // 一创建组件就调用getMenus方法
},
//生命周期 - 挂载完成（可以访问DOM元素）
mounted() {

},
beforeCreate() {}, //生命周期 - 创建之前
beforeMount() {}, //生命周期 - 挂载之前
beforeUpdate() {}, //生命周期 - 更新之前
updated() {}, //生命周期 - 更新之后
beforeDestroy() {}, //生命周期 - 销毁之前
destroyed() {}, //生命周期 - 销毁完成
activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
<style scoped>
</style>