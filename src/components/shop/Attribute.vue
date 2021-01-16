<template>
  <div>
    <h3>属性信息</h3>

    <el-table
      :data="tableData"
      style="width: 100%"
      @row-click="getDetails">

      <el-table-column
        prop="id"
        label="序号">
      </el-table-column>

      <el-table-column
        prop="name"
        label="属性名称">
      </el-table-column>

      <el-table-column
        prop="nameCH"
        label="中文名称">
      </el-table-column>

      <el-table-column
        prop="typeId"
        label="分类">
      </el-table-column>

      <el-table-column
        prop="type"
        label="属性类型"
        :formatter="typeShow">
      </el-table-column>

      <el-table-column
        prop="isSKU"
        label="是否为SKU"
        :formatter="isSKUShow">
      </el-table-column>

      <el-table-column
        prop="createDate"
        label="创建时间">
      </el-table-column>

      <el-table-column
        prop="updateDate"
        label="修改时间">
      </el-table-column>

      <el-table-column
        prop="isDel"
        label="是否删除"
        :formatter="isDelShow">
      </el-table-column>


      <el-table-column
        prop="author"
        label="操作人">
      </el-table-column>


      <el-table-column
        fixed="right"
        label="操作"
        width="100">
      </el-table-column>

    </el-table>

    <div align="center">
      <el-pagination
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        :page-sizes="sizes"
        :page-size="size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="count">
      </el-pagination>
    </div>
  </div>
</template>

<script>
  import  ajax from 'axios'

  export default {
    name: "Brand",
    data(){
      return{
        tableData: [],
        count:0,
        sizes:[2,3,5,10],
        size:2,
        start:1,
      }
    },methods:{
      queryData:function (page) {
        var athis=this;
        ajax.get("http://192.168.1.237:8080/api/attribute/list?currPage="+page+"&size="+athis.size).then(function (res) {
          athis.tableData=res.data.data.list;
          athis.count=res.data.data.count;
          console.log(res);

        }).catch(function () {
        })
      },typeShow:function (row,col,cell,index) {
        // 属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框
        if(cell==0){
          return "下拉框";
        }
        if(cell==1){
          return "单选框";
        }
        if(cell==2){
          return "复选框";
        }
        if(cell==3){
          return "输入框";
        }
        return "";
      },
      isSKUShow:function (row,col,cell,index) {
        // 是否为SKU属性   0否  1是
        if(cell==0){
          return "否";
        }
        if(cell==1){
          return "是";
        }
        return "";
      },
      isDelShow:function (row,col,cell,index) {
        // 是否删除    0否  1删除
        if(cell==0){
          return "否";
        }
        if(cell==1){
          return "是";
        }
        return "";
      },


      handleCurrentChange:function(start){ //跳转页面
        console.log(start);
        this.start=start;
        this.queryData(start);
      },handleSizeChange:function(size){ //跳转页面
        this.size=size;
        this.queryData(1);
      },

    },created:function () {
      //请求数据
      this.queryData(1,8);
      //查询品牌数据
    }
  }
</script>

<style scoped>

</style>
