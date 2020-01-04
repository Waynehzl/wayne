<template>
<!-- 顾客管理 -->
    <div>   
        <!--按钮 -->                    
        <el-button type="success" size="small" @click="toAddHandle">添加</el-button>         
        <el-button type="danger" size="small">批量删除</el-button>      
        <!--按钮 -->   

        <!--表格-->                    
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>git
                <a href="" @click.prevent="toUpdataHandler(slot.row)"><i class="el-icon-edit-outline"></i></a>

            </template>
            </el-table-column>

        </el-table>   
        <!--表格 --> 
        <!--分页 --> 
<!-- <el-pagination     layout="prev, pager, next"     :total="50">   </el-pagination> -->
        <!--分页结束 --> 
        <!--模态框 --> 
                           
<el-dialog :title="title" :visible.sync="visiable" width="60%" >
       ---{{form}}
    <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
            <el-input v-model="form.username"></el-input>
            </el-form-item>

            <el-form-item label="密码">
            <el-input type="password" v-model="form.password"></el-input>
            </el-form-item>

             <el-form-item label="真实姓名">
            <el-input  v-model="form.realname"></el-input>
            </el-form-item>

             <el-form-item label="手机号">
            <el-input v-model="form.telephone"></el-input>
            </el-form-item>
    </el-form>  <span></span>
  <span slot="footer" class="dialog-footer">
    <el-button  size="small"  @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>               
        <!--/模态框 --> 

    </div>
</template>



<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
     //用于存放要向网页中现实的数据                   
data(){
    return{
        visiable:false,
        customers:[],
        form:{}

    }
 },
 created(){
     //vue实例创建完毕
    this.loadData();
 },
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
               let url = "http://localhost:6677/customer/findAll"
                request.get(url).then((response)=>{
        //将查询结果设置到customers中,this指向外部函数的this
        this.customers = response.data;
    })
        },
        submitHandler(){
            //通过request与后台进行交互
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
            
                headers:{
                   "Content-Type":"application/x-www-form-urlencoded"
                },
                data: querystring.stringify(this.form)

            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //提示消息
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
            },
                toDeleteHandler(id){
                //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //调用后台接口完成删除操作
                let url = "http://localhost:6677/customer/deleteById?id="+id
                request.get(url).then((response)=>{
                    this.loadData();
                    //刷新数据
                    //提示结果
                     this.$message({
                        type: 'success',
                        message: response.message
                    });
                })
               
            })
            },
            toUpdataHandler(row){
                this.form=row;
                this.title="修改顾客信息",
                this.visiable=true;
            },
            closeModalHandler(){
                this.visiable=false;
            },
        
        toAddHandle(){
            //将form变为初始值
            this.form={
                type:"customer"
            }
             this.title="录入顾客信息",
            this.visiable=true;
        },
        }
        
    
   
}
</script>



<style scoped>                  

</style>