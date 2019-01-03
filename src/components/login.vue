<template>
  <div class="warp">
    <el-form class="form" label-position="top" label-width="80px" :model="formdata">
      <h3>用户登录</h3>
      <el-form-item label="用户名">
        <el-input v-model="formdata.username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="formdata.password"></el-input>
      </el-form-item>
      <el-button @click="handleLogin()" type="primary" class="deng">登录</el-button>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formdata: {
        username: "",
        password: ""
      }
    };
  },
 methods: {
    //登录请求
    async handleLogin() {
         const res = await  this.$http.post("login", this.formdata) 
            console.log(res);
      const{
          data:{
              meta:{msg,status},
              data
            }
          }=res;
        if (status === 200) {
           // 保存token值
           localStorage.setItem("token",data.token)
          this.$router.push({
            name: "home"
          });
        }else {
          this.$message.warning(msg);
    }
    }
 
 }
};
</script>

<style>
.warp {
  height: 100%;
  background-color: #324152;
  display: flex;
  justify-content: center;
  align-items: center;
}
.warp .form {
  width: 400px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
}
.warp .deng {
  width: 100%;
}
</style>

 
 

 
 
 
 
