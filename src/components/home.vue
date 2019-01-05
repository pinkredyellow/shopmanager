<template>
  <el-container class="container">
    <el-header class="header">
      <el-row>
        <el-col :span="3">
          <div class="grid-content bg-purple">
              <img src="@/assets/logo.png" alt="图片无法显示">
          </div>
        </el-col>
        <el-col :span="19" class="middle">
           <h2>电商后台管理系统</h2>
        </el-col>
        <el-col :span="1" class="loginout">
           <a href="#" @click.prevent ="handleLoginout()">退出</a>
        </el-col>
      </el-row>
    </el-header>
    <el-container>
    <el-aside width="200px" class="asdie"> 
        <!-- 1 -->
        <el-menu router  unique-opened default-active="2">
            <el-submenu index="1">
                 <template slot="title">
                   <i class="el-icon-location"></i>
                   <span>用户管理</span>
                </template>
                 <el-menu-item index="user">
                    <i class="el-icon-menu"></i> 
                     用户列表
                     </el-menu-item>
           </el-submenu>
           <!-- 2c -->
            <el-submenu index="2">
                 <template slot="title">
                   <i class="el-icon-location"></i>
                   <span>权限管理</span>
                </template>
                 <el-menu-item index="1-4">
                    <i class="el-icon-menu"></i> 
                     角色列表
                     </el-menu-item>
                      <el-menu-item index="1-5">
                    <i class="el-icon-menu"></i> 
                     权限列表
                     </el-menu-item>
           </el-submenu>
           <!-- 3c -->
            <el-submenu index="3">
                 <template slot="title">
                   <i class="el-icon-location"></i>
                   <span>商品管理</span>
                </template>
                 <el-menu-item index="1-6">
                    <i class="el-icon-menu"></i> 
                     商品列表
                </el-menu-item>
                 <el-menu-item index="1-7">
                    <i class="el-icon-menu"></i> 
                       分类参数
                </el-menu-item>
                 <el-menu-item index="1-8">
                    <i class="el-icon-menu"></i> 
                     商品分类
                </el-menu-item>
           </el-submenu>
           <!-- 4c -->
            <el-submenu index="4">
                 <template slot="title">
                   <i class="el-icon-location"></i>
                   <span>订单管理</span>
                </template>
                 <el-menu-item index="1-9">
                    <i class="el-icon-menu"></i> 
                    订单列表
                     </el-menu-item>
           </el-submenu>
           <!-- 5c -->
            <el-submenu index="5">
                 <template slot="title">
                   <i class="el-icon-location"></i>
                   <span>数据统计</span>
                </template>
                 <el-menu-item index="1-10">
                    <i class="el-icon-menu"></i> 
                     数据报表
                     </el-menu-item>
           </el-submenu>
       </el-menu>
       
        
  
    </el-aside>
    <el-main class="main">
     <router-view></router-view>
    </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {


//进入页面先登录
beforeCreate() {
   if(!localStorage.getItem('token')){
     this.$message.warning("请先登录");
     this.$router.push({
        name:"login"
     });
   }
},

methods: {
    //退出
    handleLoginout(){
      //清除token
      localStorage.clear(),
      //回到登录页面
      this.$router.push({
        name:"login"
      })
      //提示
     this.$message.success("退出成功");
    }
  },
};
</script>

<style>
.container {
  /* background-color: blue; */
  height: 100%;
}
.container .header {
    background-color: #B3C0D1; 
}
.container .asdie {
  /* background-color: goldenrod; */
}
.container .main {
  /* background-color: aqua; */
}
.middle{
    line-height: 60px; 
    text-align: center; 
    color: white;
}
.loginout a{
     line-height: 60px;
    text-decoration: none;
}
</style>
