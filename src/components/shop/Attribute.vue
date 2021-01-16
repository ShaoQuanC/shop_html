<template>
  <div>
    <h3>属性信息</h3>

    <el-button type="success" @click="addFormFlag=true">新增</el-button>

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
        <template slot-scope="scope">
          <el-button type="warning" size="small"
                     @click="() => updateFormFlag=true">编辑</el-button>
          <!--<el-button type="text" size="small" v-on:click="deleteCar(scope.row.cid)">删除</el-button>-->
        </template>
      </el-table-column>

    </el-table>

    <!--  新增的弹框   -->
    <el-dialog title="录入属性信息" :visible.sync="addFormFlag">

      <el-form :model="addAttributeForm"  label-width="80px">

        <el-form-item label="属性名称" prop="name">
          <el-input v-model="addAttributeForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="中文名称" prop="nameCH">
          <el-input v-model="addAttributeForm.nameCH"></el-input>
        </el-form-item>

        <el-form-item label="分类" prop="typeId">
          <el-input v-model="addAttributeForm.typeId"></el-input>
        </el-form-item>

        <el-form-item label="是否SKU" prop="isSKU">
          <el-radio v-model="addAttributeForm.isSKU" label="0">否</el-radio>
          <el-radio v-model="addAttributeForm.isSKU" label="1">是</el-radio>
        </el-form-item>

        <el-form-item label="是否删除" prop="isDel">
          <el-radio v-model="addAttributeForm.isDel" label="0">不删除</el-radio>
          <el-radio v-model="addAttributeForm.isDel" label="1">删除</el-radio>
        </el-form-item>

        <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
        <el-form-item label="属性的类型" prop="type">
          <el-radio v-model="addAttributeForm.type" label="0">下拉框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="1">单选框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="2">复选框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="3">输入框</el-radio>
        </el-form-item>

        <el-form-item label="操作人" prop="author">
          <el-input v-model="addAttributeForm.author"></el-input>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>


    <!--  修改的弹框   -->
    <el-dialog title="修改属性信息" :visible.sync="updateFormFlag">

      <el-form :model="updateAttributeForm"  label-width="100px">

        <el-form-item label="属性名称" prop="name">
          <el-input v-model="updateAttributeForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="中文名称" prop="nameCH">
          <el-input v-model="updateAttributeForm.nameCH"></el-input>
        </el-form-item>

        <el-form-item label="分类" prop="typeId">
          <el-input v-model="updateAttributeForm.typeId"></el-input>
        </el-form-item>

        <el-form-item label="是否SKU" prop="isSKU">
          <el-radio v-model="updateAttributeForm.isSKU" :label="0">否</el-radio>
          <el-radio v-model="updateAttributeForm.isSKU" :label="1">是</el-radio>
        </el-form-item>

        <el-form-item label="是否删除" prop="isDel">
          <el-radio v-model="updateAttributeForm.isDel" :label="0">不删除</el-radio>
          <el-radio v-model="updateAttributeForm.isDel" :label="1">删除</el-radio>
        </el-form-item>

        <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
        <el-form-item label="属性的类型" prop="type">
          <!--:label  表示获取选项的value-->
          <el-radio v-model="updateAttributeForm.type" :label="0">下拉框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="1">单选框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="2">复选框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="3">输入框</el-radio>
        </el-form-item>

        <el-form-item label="操作人" prop="author">
          <el-input v-model="updateAttributeForm.author"></el-input>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updateFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updateForm">确 定</el-button>
      </div>
    </el-dialog>

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

        /*新增*/
        addFormFlag:false,
        addAttributeForm:{
          name:"",
          nameCH:"",
          typeId:"",
          type:[],
          isSKU:"",
          isDel:"0",
          author:""
        },

        /*修改*/
        updateFormFlag:false,
        updateAttributeForm:{
          name:"",
          nameCH:"",
          typeId:"",
          type:[],
          isSKU:"",
          isDel:"",
          author:""
        },
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
      },
      typeShow:function (row,col,cell,index) {
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

      addForm:function () {
        var athis=this;
        ajax.post("http://192.168.1.237:8080/api/attribute/add",this.$qs.stringify(this.addAttributeForm)).then(function () {
          athis.addFormFlag = false;
          athis.queryData(1);
        }).catch(function () {

        })
      },

      /*回显信息*/
      getDetails(row){
        var athis = this;
        athis.updateAttributeForm.id=row.id;
        athis.updateAttributeForm.name=row.name;
        athis.updateAttributeForm.nameCH=row.nameCH;
        athis.updateAttributeForm.typeId=row.typeId;
        athis.updateAttributeForm.type=row.type;
        athis.updateAttributeForm.isSKU=row.isSKU;
        athis.updateAttributeForm.isDel=row.isDel;
        athis.updateAttributeForm.author=row.author;
        console.log("属性修改"+JSON.stringify(row))//此时就能拿到整行的信息
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
