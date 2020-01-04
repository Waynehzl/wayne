<template>
  <div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">删除</el-button>
    <el-table :data="address">
      <el-table-column prop="province" label="省份"></el-table-column>
      <el-table-column prop="city" label="城市"></el-table-column>
      <el-table-column prop="area" label="地区"></el-table-column>
      <el-table-column prop="address" label="地址"></el-table-column>
       <el-table-column prop="telephone" label="联系方式"></el-table-column>
        <el-table-column prop="customerId" label="顾客编号"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>
          <a href @click.prevent="toUpdateHandler(slot.row)"><i class="el-icon-edit-outline"></i></a>
        </template>
      </el-table-column>
    </el-table>
    <!-- <el-pagination
           layout="prev, pager, next"
           :total="50">
    </el-pagination>-->
    <el-dialog :title="title" :visible.sync="visible" width="60%">
      {{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="省份">
          <el-input v-model="form.province"></el-input>
        </el-form-item>
        <el-form-item label="城市">
          <el-input v-model="form.city"></el-input>
        </el-form-item>
        <el-form-item label="地区">
          <el-input v-model="form.area"></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="form.address"></el-input>
        </el-form-item>
        <el-form-item label="联系方式">
          <el-input v-model="form.telephone"></el-input>
        </el-form-item>
        <el-form-item label="顾客编号">
          <el-input v-model="form.customerId"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeModalHandler">取 消</el-button>
        <el-button type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import request from "@/utils/request";
import querystring from "querystring";
export default {
  methods: {
    loadData() {
      let url = "http://localhost:6677/address/findAll";
      request.get(url).then(response => {
        this.address = response.data;
      });
    },
    submitHandler() {
      let url = "http://localhost:6677/address/saveOrUpdate";
      request({
        url,
        method: "POST",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: querystring.stringify(this.form)
      }).then(response => {
        //关闭模态框
        this.closeModalHandler();
        //提示消息
        this.loadData();
        this.$message({
          type: "success",
          message: response.message
        });
      });
    },
    toAddHandler() {
      this.visible = true;
      this.title = "添加地址信息";
      this.form = {
        type: "address"
      };
    },
    closeModalHandler() {
      this.visible = false;
    },
    toDeleteHandler(id) {
      //确认
      this.$confirm("此操作将永久删除该记录, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let url = "http://localhost:6677/address/deleteById";
        request.get(url + "?id=" + id).then(response => {
          this.loadData();
          this.$message({
            type: "success",
            message: response.message
          });
        });
      });
    },
    toUpdateHandler(row) {
    this.form = row;
    this.visible = true;
    this.title = "修改地址信息";
  },
  },
  data() {
    return {
      title: "添加地址信息",
      visible: false,
      address: [],
      form: {
        type: "address"
      }
    };
  },
  created() {
    this.loadData();
  }
};
</script>

<style scoped>
</style>