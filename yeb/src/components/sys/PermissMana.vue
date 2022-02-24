<template>
<div>
  <div class="permissManaTool">
    <el-input size="small" placeholder="请输入角色英文名" v-model="role.name">
      <template slot="prepend">ROLE_</template>
    </el-input>
    <el-input size="small" placeholder="请输入角色中文名" v-model="role.nameZh" @keydown.enter.native="doAddRole"></el-input>
    <el-button size="small" icon="el-icon-plus" type="primary" @click="doAddRole">添加角色</el-button>
  </div>
  <div class="permissManaMain">
    <el-collapse  accordion @change="change" v-model="activeNames">
      <el-collapse-item :title=r.nameZh :name=r.id v-for="(r,index) in roles" :key="index">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>可访问资源</span>
            <el-button style="float: right; padding: 3px 0;color: #ff0000" type="text" icon="el-icon-delete" @click="doDeleteRole(r)" ></el-button>
          </div>
          <div>
            <el-tree
                show-checkbox
                :data="allMenus"
                :props="defaultProps"
                :default-checked-keys="selectedMenus"
                node-key="id"
                ref="tree"
                :key="index"
                >
            </el-tree>
            <div style="display:flex; justify-content: flex-end" >
              <el-button size="mini" @click="canceUpdate">取消修改</el-button>
              <el-button size="mini" type="primary" @click="doupdate(r.id,index)">确认修改</el-button>
            </div>
          </div>
        </el-card>
      </el-collapse-item>

    </el-collapse>
  </div>
</div>
</template>

<script>
export default {
  name: "PermissMana",
  data(){
    return{
      role:{
        name:'',
        nameZh:''
      },
      roles:[],
      allMenus:[],
      defaultProps: {
        children: 'children',
        label: 'name'
      },
      selectedMenus:[],
      activeNames:-1
    }
  },
  mounted() {
    this.initRoles()
  },
  methods:{
    doDeleteRole(role){
      this.$confirm('此操作将永久删除['+role.nameZh+']职位, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/permission/role/'+role.id).then(resp=>{
          if(resp){
            this.initRoles();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    doAddRole(){
      if(this.role.name && this.role.nameZh){
        this.postRequest('/system/basic/permission/',this.role).then(resp=>{
          if(resp){
            this.initRoles();
            this.role.name='';
            this.role.nameZh='';

          }
        })
      }else {
        this.$message.error('所有字段不能为空')
      }
    },
    canceUpdate(){
      this.activeNames=-1;
    },
    doupdate(rid,index){
      let tree=this.$refs.tree[index];
      let selectedKey=tree.getCheckedKeys(true);
      let url=' /system/basic/permission/?rid='+rid;
      selectedKey.forEach(key=>{
        url+='&mids='+key;
      });
      this.putRequest(url).then(resp=>{
        if(resp){
          this.activeNames=-1
        }
      })
    },
    change(rid){
        if(rid){
          this.initMenus()
          this.initSelectMenus(rid);
        }
    },
    initSelectMenus(rid){
      this.getRequest('/system/basic/permission/mid/'+rid).then(resp=>{
        if(resp){
          this.selectedMenus=resp;
        }
      })
    },
    initMenus(){
      this.getRequest('/system/basic/permission/menus').then(resp=>{
        this.allMenus=resp;
      })
    },
    initRoles(){
      this.getRequest('/system/basic/permission/').then(resp=>{
        if(resp){
          this.roles=resp;
        }
      })
}
  }
}
</script>

<style scoped>
.permissManaTool{
  display: flex;
  justify-content: flex-start;
}
.permissManaTool .el-input{
  width: 300px;
  margin-right: 6px;
}
.permissManaMain{
  margin-top: 10px;
  width: 700px;
}
</style>
