<template>
  <el-container>
    <el-header class="homeheader">
      <div class="title">云办公</div>
      <el-dropdown class="userInfo" @command="commandHandler" >
  <span class="el-dropdown-link">
    {{ user.name }}<i><img :src="user.userFace"></i>
  </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="userinfo">个人中心</el-dropdown-item>
          <el-dropdown-item command="setting">设置</el-dropdown-item>
          <el-dropdown-item command="logout">注销登录</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu router unique-opened>
          <el-submenu :index="index+''" v-for="(item,index) in routes"
          :key="index" v-if="!item.hidden">
            <template slot="title">
              <i :class="item.iconCls" style="color: #62e6ea"></i>
              <span>{{item.name}}</span>
            </template>
              <el-menu-item
                  :index="children.path"
                  v-for="(children,indexj) in item.children"
                  :key="indexj">
                {{ children.name }}</el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <el-breadcrumb v-if="this.$router.currentRoute.path!='/home'"  separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>{{this.$router.currentRoute.name}}</el-breadcrumb-item>
        </el-breadcrumb>
        <div class="homeWelcome" v-if="this.$router.currentRoute.path=='/home'">
          欢迎来到云办公系统
        </div>
        <router-view class="homerRouterView" />
      </el-main>
    </el-container>
  </el-container>

</template>

<script>
export default {
  name: "Home",
  data(){
    return {
      user:JSON.parse(window.sessionStorage.getItem('user'))
    }
  },
  computed:{
    routes(){
      return this.$store.state.routes
    }
  },
  methods:{
  commandHandler(command){
    if(command=='logout'){
      this.$confirm('此操作将注销登录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
         //注销登录
        this.postRequest('/logout')
        //清空用户信息
        window.sessionStorage.removeItem('tokenStr');
        window.sessionStorage.removeItem('user');
        this.$store.commit('initRoutes',[])
        //跳转到登录
        this.$router.replace('/')
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消操作'
        });
      });

    }
   }
  }
}
</script>

<style scoped>
.homeheader{
  background: cadetblue;
  display: flex;
  align-items:center ;
  justify-content: space-between;
  box-sizing: border-box;
  padding: 0 15px;
}
 .homeheader .title{
  font-size: 30px;
   font-family: 楷体;
   color: white;
}
 .homeheader .userInfo{
   cursor: pointer;
 }
 .el-dropdown-link img{
   width: 48px;
   height: 48px;
   border-radius: 24px;
   margin-left: 8px;
 }
 .homeWelcome{
   text-align: center;
   font-size: 30px;
   font-family: 华文楷体;
   color: #2de3bf;
   padding-top: 50px;
 }
.homerRouterView{
  margin-top: 12px;
}
</style>
