<template>
  <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- 表格 -->
    <el-table :data="comments">
      <el-table-column fixed="left" label="编号" prop="id"></el-table-column>
      <el-table-column fixed="left" label="内容" prop="content"></el-table-column>
      <el-table-column label="评论次数" prop="commentTime"></el-table-column>
      <el-table-column label="以前号码" prop="orderId"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href @click.prevent="toDeleteHandler(slot.row.id)">
            <i class="el-icon-delete"></i>
          </a>
          <a href @click.prevent="toUpdateHandler(slot.row)">
            <i class="el-icon-edit-outline"></i>
          </a>
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>

    <!-- 模态框 -->
    <el-dialog :title="title" :visible.sync="visible" width="60%">
      <el-form :model="form" label-width="80px">
        <el-form-item label="内容">
          <el-input v-model="form.content"></el-input>
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
      title: "录入评论信息",
      visible: false,
      comments: [],
      form: {
        type: "comment"
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
      let url = "http://localhost:6677/comment/saveOrUpdate";
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
      let url = "http://localhost:6677/comment/findAll";
      request.get(url).then(response => {
        //箭头函数中this 指向外部函数中的this
        this.comments = response.data;
      });
    },
    toAddHandler() {
      this.form = {
        type: "comment"
      };
      this.title = "录入评论信息";
      this.visible = true;
    },

    toDeleteHandler(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let url = "http://localhost:6677/comment/deleteById?id=" + id;
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
      this.title = "修改录入信息", 
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
