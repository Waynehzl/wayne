<template>
  <!-- 顾客管理 -->
  <div>
    <!--按钮 -->
    <el-button type="success" size="small" @click="toAddHandle">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!--按钮 -->
    <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="所有订单" name="first">所有订单</el-tab-pane>
        <el-tab-pane label="待支付" name="second">待支付</el-tab-pane>
        <el-tab-pane label="待派单" name="third">待派单</el-tab-pane>
        <el-tab-pane label="待接单" name="fourth">待接单</el-tab-pane>
        <el-tab-pane label="待服务" name="fifth">待服务</el-tab-pane>
        <el-tab-pane label="待确认" name="sixth">待确认</el-tab-pane>
        <el-tab-pane label="待完成" name="seventh">待完成</el-tab-pane>

    </el-tabs>
    <!--表格-->
    <el-table :data="order.list">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="orderTime" label="下单时间"></el-table-column>
      <el-table-column prop="total" label="总价"></el-table-column>
      <el-table-column prop="status" label="状态"></el-table-column>
      <el-table-column prop="customerId" label="顾客Id"></el-table-column>
      <el-table-column prop="waiterId" label="员工Id"></el-table-column>
      <el-table-column prop="addressId" label="地址Id"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href @click.prevent="toDeleteHandler(slot.row.id)">
            <i class="el-icon-delete"></i>
          </a>
          <a href @click.prevent="toUpdataHandler(slot.row)">
            <i class="el-icon-edit-outline"></i>
          </a>
        </template>
      </el-table-column>
    </el-table>

    <!--表格 -->
    <!--分页 -->
    <el-pagination     layout="prev, pager, next"     :total="order.total" @current-change="pageChangeHandler">   

    </el-pagination>
    <!--分页结束 -->
    <!--模态框 -->

    <el-dialog :title="title" :visible.sync="visiable" width="60%">
      
      <el-form :model="form" label-width="80px">
        <el-form-item label="姓名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
      </el-form>
      <span></span>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!--/模态框 -->
  </div>
</template>



<script>
import request from "@/utils/request";
import querystring from "querystring";
export default {
  //用于存放要向网页中现实的数据
  data() {
    return {
      visiable: false,
      order: {},
      form: {},
      params:{
          page:0,
          pageSize:10,
          customerId:26
      },
      title:"",
      activeName: "first",
    
      activeName: "second",
    
      activeName: "third",
    
      activeName: "fourth",
    
      activeName: "fifth",
    
      activeName: "sixth",
    
      activeName: "sevevth",
    
      activeName: "eigth",
    }
  },
  created() {
    //vue实例创建完毕
    this.loadData();
  },
  //用于存放网页中需要调用的方法
  methods: {
      //当分页中当前页改变的时候执行
      pageChangeHandler(page){
          //将paramas中当前页改变为插件中的当前页
          this.params.page=page-1;
          this.loadData();
      },
         handleClick(tab, event) {
        console.log(tab, event);
      },
    loadData() {
      let url = "http://localhost:6677/order/queryPage";
      request({
          url,
          method: "POST",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: querystring.stringify(this.params)
      }).then((response)=>{
          this.order=response.data;
      })
    },

    submitHandler() {
      //通过request与后台进行交互
      let url = "http://localhost:6677/order/saveOrUpdate";
      request({
        url,
        method: "POST",

        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: querystring.stringify(this.form)
      }).then((response) => {
        //模态框关闭
        this.closeModalHandler();
        //提示消息
        this.loadData();
        this.$message({
          type: "success",
          message: response.message
        });
      });
    },
    toDeleteHandler(id) {
      //确认
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        //调用后台接口完成删除操作
        let url = "http://localhost:6677/order/deleteById?id=" + id;
        request.get(url).then(response => {
          this.loadData();
          //刷新数据
          //提示结果
          this.$message({
            type: "success",
            message: response.message
          });
        });
      });
    },
    toUpdataHandler(row) {
      this.form = row;
      this.title = "修改顾客信息", 
      this.visiable = true;
    },
    closeModalHandler() {
      this.visiable = false;
    },

    toAddHandle() {
      //将form变为初始值
      this.form = {
        type: "order"
      };
      this.title = "录入顾客信息", 
      this.visiable = true;
    }
  }
};
</script>



<style scoped>
</style>