<template>
  <div>
    
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- 表格 -->
    <el-table :data="employees">
      <el-table-column fixed="left" label="编号" prop="id"></el-table-column>
      <el-table-column fixed="left" label="用户名" prop="username"></el-table-column>
      <el-table-column label="姓名" prop="realname"></el-table-column>
      <el-table-column label="性别" prop="gender"></el-table-column>
      <el-table-column label="联系方式" prop="telephone"></el-table-column>
      <el-table-column label="身份证号码" prop="idCard"></el-table-column>
      <el-table-column label="银行卡号" prop="bankCard"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <template v-slot= "slot">
          <a href @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>

    <!-- 模态框 -->
    <el-dialog :title="title" :visible.sync="visible" width="60%">
      <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="性别">
          <el-radio-group v-model="form.gender">
            <el-radio label="男">男</el-radio>
            <el-radio label="女">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input v-model="form.idCard"></el-input>
        </el-form-item>
        <el-form-item label="银行卡号">
          <el-input v-model="form.bankCard"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="colseModul" size="small">取 消</el-button>
        <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
//import { request } from 'http'
import request from "@/utils/request";
import querystring from "querystring";
//暴漏接口
export default {
  data() {
    return {
      title: "录入员工信息",
      visible: false,
      employees: [],
      form: {
        type: "employee"
      }
    };
  },
  //页面加载时刷新数据
  created() {
    this.loadData();
  },
  methods: {
    //确定提交
    submitHandler() {
      let url = "http://localhost:6677/waiter/saveOrUpdate";
      //前端向后台发送请求，完成数据的保存操作
      request({
        url,
        method: "POST",
        //告知后台即将查询字符串,请求体
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        //请求体中的数据，将this.form转换为查询字符串放送给后台
        data: querystring.stringify(this.form)
      }).then(response => {
        this.colseModul();
        this.loadData();
        this.$message({
          type: "success",
          message: response.message
        });
      });
    },

    //重载数据
    loadData() {
      let url = "http://localhost:6677/waiter/findAll";
      request.get(url).then(response => {
        //箭头函数中this 指向外部函数中的this
        this.employees = response.data;
      });
    },
    toAddHandler() {
        this.form = {
            type:"employee"
        }
      this.title = "录入员工信息";
      this.visible = true;
    },

    
    toDeleteHandler(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let url = "http://localhost:6677/waiter/deleteById?id=" + id;
        request.get(url).then(response => {
          //刷新模态框
          this.loadData();
          //提示结果
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        });
      });
    },
    toUpdateHandler(row) {
        this.form = row;
      this.title = "修改员工信息",
       this.visible = true;
    },
    colseModul() {
      this.visible = false;
    }
  }
};
</script>



<style scoped>
</style>
