<template>
  <el-card class="card">
    <!-- 面包屑 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 搜索+添加按钮 -->
    <el-row>
      <el-col>
        <el-input
         @clear="getAllUser()" clearable placeholder="请输入用户名" v-model="query" class="input-with-select">
          <el-button @click="searchUser()"  slot="append" icon="el-icon-search"></el-button>
        </el-input>
        <el-button type="success" @click = "showDiaAdd()">添加用户</el-button>
      </el-col>
    </el-row>

    <!-- 表格 -->
 
    <el-table class="table" height="300px" :data="tableData" style="width: 100%">
      <el-table-column prop="id" label="#" width="80"></el-table-column>
      <el-table-column prop="username" label="姓名" width="100"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
      <el-table-column prop="mobile" label="电话 " width="120"></el-table-column>
       <!-- /时间格式处理 -->
      <el-table-column   label="创建时间" width="180">
        <!-- 目的：在里层使用外层tabledata-》组件传值
        slot-scope作用：承上启下、传递数据 
        tabledata会自动获取上层数据的值：data="tabledata"
        在里层通过tableData.row
        -->
         <template slot-scope="scope"> {{scope.row.created_time |fmtDate}}</template>
      </el-table-column>
      <el-table-column  label="用户状态" width="140">
           <template slot-scope="scope">
                <el-switch
                   @change ="changeState(scope.row)" 
                    v-model="scope.row.mg_state"
                    active-color="#13ce66"
                    inactive-color="#ff4949">
                </el-switch>
            </template>
      </el-table-column>
      <el-table-column   label="操作" width="180">
         <template slot-scope="scope">
            <el-button size="mini" plain  type="primary" icon="el-icon-edit" circle
            @click = "showDiaEdit(scope.row)"></el-button>
            <el-button size="mini" plain  type="danger" icon="el-icon-delete" circle
             @click = "showDeleConfig(scope.row)"></el-button>
             <el-button @click = "showRoleDia(scope.row)" size="mini" plain  type="success" icon="el-icon-check" circle ></el-button>
         </template>
      </el-table-column>
    </el-table>

    <!-- 分页 -->
         <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagenum"
      :page-sizes="[2, 4, 6,8]"
      :page-size="2"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>

    <!-- 对话框 -->
    <!-- 添加 -->
<el-dialog title="添加用户" :visible.sync="dialogFormVisibleAdd">
     <el-form :model="form">
    <el-form-item label="用户名" :label-width="formLabelWidth">
      <el-input v-model="form.username" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="密码" :label-width="formLabelWidth">
      <el-input v-model="form.password" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="邮箱" :label-width="formLabelWidth">
      <el-input v-model="form.email" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="电话" :label-width="formLabelWidth">
      <el-input v-model="form.mobile" autocomplete="off"></el-input>
    </el-form-item>
    
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
    <el-button type="primary" @click="addUser()">确 定</el-button>
  </div>
</el-dialog>


  <!-- 编辑 -->
<el-dialog title="编辑用户" :visible.sync="dialogFormVisibleEdit">
     <el-form :model="form">
    <el-form-item label="用户名" :label-width="formLabelWidth">
      <el-input disabled v-model="form.username" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="邮箱" :label-width="formLabelWidth">
      <el-input v-model="form.email" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="电话" :label-width="formLabelWidth">
      <el-input v-model="form.mobile" autocomplete="off"></el-input>
    </el-form-item>
    </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisibleEdit = false">取 消</el-button>
    <el-button type="primary" @click="editUser()">确 定</el-button>
  </div>
</el-dialog>
<!-- 分配角色 -->
<el-dialog title="分配角色" :visible.sync="dialogFormVisibleRole">
  <el-form>
    <el-form-item label="用户名" :label-width="formLabelWidth">
      {{currUsername}}
    </el-form-item>
    <el-form-item label="角色分配" :label-width="formLabelWidth">
      {{currRoleId}}
      <el-select v-model="currRoleId">
        <el-option disabled label="请选择" :value="-1"></el-option>
        
        <el-option 
         v-for="(v,i) in roles" :key="i"
        :label="v.roleName" :value="v.id"></el-option>
      </el-select>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisible = false">取 消</el-button>
    <el-button type="primary" @click="setRole()">确 定</el-button>
  </div>
</el-dialog>
  </el-card>
</template>

<script>
export default {
  data() {
    return {
      query: "",

      //表格数据源
      tableData: [],
      query:'',
      pagenum:1,
      pagesize:2,
      total:-1,
      //对话框
      dialogFormVisibleAdd:false,
      dialogFormVisibleEdit:false,
      dialogFormVisibleRole:false,
      form:{
          username:'',
          password:'',
          email:'',
          mobile:'',
      },
      formLabelWidth:"100px",
      //select所绑定的数据
      currRoleId:-1,
      //接收数据
      roles:[],
      currUsername:"",
      currUserId:-1
      

       

    };
  },
  created() {
      this.getTableData();
  },
  methods: {
    //分配用户角色
    async setRole(user){
    const res = await this.$http.put(`users/${this.currUserId}/role`,{
      rid:this.currRoleId
    });
    //console.log(res);
    this.dialogFormVisibleRole = false;
    },
    //分配角色
    async showRoleDia(user){
      this.currUsername=user.username;
      this.currUserId = user.id;
      this.dialogFormVisibleRole = true;
      //获取所有角色的名字
      const res = await this.$http.get(`roles`);
      //console.log(res);
      const {meta:{msg,status},data}=res.data;
      if(status===200){
        //获取data
        this.roles=data;
      }
      //根据id查询名字
      const res2 = await this.$http.get(`users/${user.id}`)
      //console.log(res2);
      this.currRoleId=res2.data.data.rid;
      
    },
    //改变用户状态
    async changeState(user){
      // console.log(user);
      const res = await this.$http.put(`users/${user.id}/state/${user.mg_state}`);
      //console.log(res);
     
    },
      //编辑--发送请求

      async editUser(){
         const res = await this.$http.put(`users/${this.form.id}`,this.form);
         //console.log(res);
         const {meta:{msg,status}}=res.data;
         if(status===200){
             //关闭对话框
             this.dialogFormVisibleEdit = false;
             //刷新表格
             this.getTableData();
         }else{
             this.$message.warning(msg);
         }

      
       },
      //编辑--打开对话框
   async showDiaEdit(user){
       this.dialogFormVisibleEdit =true;
       const res = await this.$http.get(`users/${user.id}`,this.form);
       //console.log(res);
       const {meta:{msg,status},data}= res.data;
       if(status===200){
         this.form = data;
         //console.log(this.form);
       } 
     

    },
    //   删除
    showDeleConfig(user){
        // console.log(test);
         this.$confirm("是否删除?","提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
        .then(async() => {
          //发送请求
        const res =await  this.$http.delete(`users/${user.id}`);
        //console.log(res);
        const {meta:{msg,status}}=res.data;
        if(status===200){
            this.$message.success("删除成功!"); 
            this.pagenum=1;
            this.getTableData();
        }else{
            this.$message.warning(msg); 
           
        }
         })    
       .catch(() => {
               this.$message.info("已取消删除!");      
        });
    },
      //添加用户--发送请求
     async addUser(){
         const res = await this.$http.post(`users`,this.form)
         //console.log(res);
         const {meta:{msg,status}}=res.data;
           if(status===201){
           //关闭对话框
           this.dialogFormVisibleAdd = false;
             //刷新表格
             this.getTableData();
             
         }else{
             this.$message.warning(msg);
         }
      },
      //添加用户--对话框
     
      showDiaAdd(){
          //清空
         this.form={};
        this.dialogFormVisibleAdd = true;
      },
       // 点击X 获取所有用户
    getAllUser(){
        this.getTableData();
    },
      //搜索
     
      searchUser(){
         this.pagenum =1;
           this.getTableData();
      },
      //分页    
      handleSizeChange(val) {
          this.pagenum =1;
          this.pagesize = val;
          this.getTableData();
       // console.log(`每页 ${val} 条`);
      },
      handleCurrentChange(val) {
          //val是当前页码
          this.pagenum =val;
        this.getTableData();
       // console.log(`当前页: ${val}`);
      },
      //获取用户列表数据
     async getTableData(){
         //请求头
         const AUTH_TOKEN = localStorage.getItem("token")
         this.$http.defaults.headers.common['Authorization'] = AUTH_TOKEN;

        //  setHeader("Context/type")
          const res = await this.$http.get(
              `users?query=${this.query}&pagenum=${this.pagenum}&pagesize=${this.pagesize}`)
           //console.log(res);
          const {meta:{msg,status},data:{total,users}}=res.data;
          if(status===200){
              //获取成功
              this.tableData=users;
              //console.log(this.tableData);
              this.total = total;
              this.$message.success(msg);
          }else{
              this.$message.warning(msg);
          }
      },
  },

};
</script>

<style>
.card {
  height: 100%;
}
.input-with-select {
  width: 360px;
  margin-top: 20px;
}
</style>
