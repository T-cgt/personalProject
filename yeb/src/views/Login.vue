<template>
  <div>
    <el-form :rules="rules"
             ref="loginForm"
             :model="loginForm"
             v-loading="loading"
             element-loading-text="正在登陆..."
             element-loading-spinner="el-icon-loading"
             element-loading-background="rgba(0, 0, 0, 0.8)"
             class="loginContainer">
      <h3 class="logintitle">系统登录</h3>
      <el-form-item prop="username">
        <el-input type="text" placeholder="请输入用户名" auto-complete="false" v-model="loginForm.username" >1</el-input>
      </el-form-item>
    <el-form-item prop="password">
      <el-input type="password" placeholder="请输入密码" auto-complete="false" v-model="loginForm.password" ></el-input>
    </el-form-item>
    <el-form-item prop="code">
      <el-input v-model="loginForm.code" auto-complete="false"  placeholder="点击图片更换验证码" style="width: 220px;margin-right: 15px "></el-input>
      <img :src="captchaUrl" @click="updateCaptcha">
    </el-form-item>
    <el-checkbox v-model="checked" style="margin-bottom: 15px">记住我</el-checkbox>
    <el-button type="primary" style="width: 100%" @click="submitForm">登录</el-button>
    </el-form>
  </div>
</template>

<script>


export default {
name: "Login",
  data(){
  return{
    captchaUrl:'/captcha?time='+new Date(),
    loginForm:{
      username:'admin',
      password:'123',
      code:''
    },
    checked:true,
    loading:false,
    rules:{
      username:[{required:true,message:'请输入用户名',trigger:'blur'}],
      password:[{required:true,message:'请输入密码',trigger:'blur'}],
      code:[{required:true,message:'请输入验证码',trigger:'blur'}]
    }
  }
  },
  methods:{
  updateCaptcha(){
    this.captchaUrl='/captcha?time='+new Date();
  },
    submitForm() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.loading=true
          this.postRequest('/login',this.loginForm).then(res=>{
            if(res){
              //储存用户token
              this.loading=false
              const tokenStr=res.obj.tokenHead+res.obj.token;
              window.sessionStorage.setItem('tokenStr',tokenStr)
              //跳转首页
              let path=this.$route.query.redirect
              this.$router.replace((path =='/' || path == undefined) ? '/home': path)
            }
          })
        } else {
          this.$message.error('请输入所有字段');
          return false;
        }
      });
    }
  }
}
</script>

<style >
.loginContainer{
  border-radius: 15px;
  background-clip: padding-box;
  margin:180px auto;
  width: 350px;
  padding: 15px 35px;
  background: #fff;
  border: 1px solid #eaeaea;
  box-shadow: 0 0 25px #cac6c6;
}
.el-form-item__content{
  display: flex;
  align-items: center;
}
.logintitle{
  margin: 0px auto 40px ;
  text-align: center;
}

</style>
