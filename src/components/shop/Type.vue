<template>
      <div>
        <h1>商品分类管理</h1>
        <el-button type="primary" @click="addFormFlag=true" >新增</el-button>
        <el-tree :data="data" :props="defaultProps" accordion
                 show-checkbox @node-click="handleBucketClick">
          <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            type="text"
            size="mini"
            @click="() => updateFormFlag=true">
            修改
          </el-button>
          <el-button
            type="text"
            size="mini"
            @click="() => deleteType(node, data)">
            删除
          </el-button>
        </span>
      </span>
        </el-tree>

        <!--<el-tree-->
          <!--:data="data"-->
          <!--ref="tree"-->
          <!--show-checkbox-->
          <!--accordion-->
          <!--node-key="id"-->
          <!--@node-click="clickNode"-->
          <!--:check-on-click-node="true"-->
          <!--:default-expand-all="true"-->
          <!--:props="defaultProps">-->
        <!--</el-tree>-->



<!--新增模板-->
        <el-dialog title="录入分类信息" :visible.sync="addFormFlag">

          <el-form :model="addTypeForm" ref="addTypeForm"  label-width="80px">



            <el-form-item label="pid" prop="pid">
              <el-input v-model="addTypeForm.pid" autocomplete="off" ></el-input>
            </el-form-item>
            <el-form-item label="上级目录" prop="sjname">
              <el-input v-model="addTypeForm.sjname" autocomplete="off" ></el-input>
            </el-form-item>

            <el-form-item label="名称" prop="name">
              <el-input v-model="addTypeForm.name" autocomplete="off" ></el-input>
            </el-form-item>

            <!--<el-form-item label="汽车类型" prop="carType">
              <el-radio-group v-model="addCarForm.carType">
                <el-radio v-for="type in types" :label="type.id" :key="type.id">{{type.name}}</el-radio>
              </el-radio-group>
            </el-form-item>

            <el-form-item label="图片">
              <el-upload
                class="upload-demo"
                action="http://192.168.1.43:8080/api/car/upload"
                :on-success="imgCallBack"
                name="file"
                list-type="picture">
                <el-button size="small" type="primary">点击上传</el-button>
                <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
              </el-upload>
            </el-form-item>

            <el-form-item label="价格" prop="price">
              <el-input v-model="addCarForm.price"></el-input>
            </el-form-item>

            <el-form-item label="品牌" prop="bid">
              <el-select v-model="addCarForm.bid" placeholder="请选择">
                <el-option
                  v-for="item in bandData"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id">
                </el-option>
              </el-select>

            </el-form-item>-->

            <!--<el-form-item label="时间" prop="createDate">
              <el-date-picker
                v-model="addTypeForm.createDate"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
            </el-form-item>-->

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="addFormFlag = false">取 消</el-button>
            <el-button type="primary" @click="addForm">确 定</el-button>
          </div>

        </el-dialog>


        <!--修改模板-->
        <el-dialog title="录入分类信息" :visible.sync="updateFormFlag">

          <el-form :model="updateTypeForm" ref="addTypeForm"  label-width="80px">
            <el-form-item label="pid" prop="pid">
              <el-input v-model="updateTypeForm.pid" autocomplete="off" ></el-input>
            </el-form-item>


            <el-form-item label="名称" prop="name">
              <el-input v-model="updateTypeForm.name" autocomplete="off" ></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="updateFormFlag = false">取 消</el-button>
            <el-button type="primary" @click="updateForm">确 定</el-button>
          </div>

        </el-dialog>




      </div>
</template>

<script>
  import axios from 'axios';
    export default {
        name: "Type",
      data() {
        return {
          data: [],//树的数据
          ajaxData:[],// 远程请求的数据
          jsonStr:"", //递归拼接处理
          defaultProps: {},

          /* 新增模块的数据  */
          addFormFlag:false,
          addTypeForm:{
            name:"",
            pid:"",
            sjname:""
          },
          /* 修改模块的数据  */
          updateFormFlag:false,
          updateTypeForm:{
            name:"",
            pid:"",
          }
        };
      },
      methods: {
        chandleData:function(){ //ajaxData  处理成 data   //转换数据
          debugger;
          //找到顶层节点的数据
          for (let i = 0; i <this.ajaxData.length ; i++) {
            if(this.ajaxData[i].pid==0){
              this.diguiNode(this.ajaxData[i]);
              break;
            }
          }
          console.log(this.jsonStr);
          //将字符串 转为json对象
          this.data.push(JSON.parse(this.jsonStr));

        },  //  id  name  pid        id  name children []
        diguiNode:function (node) {
          // 判断是否为父节点
          var bf=this.isParent(node);
          if(bf==true){
            //{"id":1,"label":"分类",}
            //{"id":1,"label":"分类","children":[]}
            this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'","children":[';
            //拼接子节点
            //查找此节点的子节点
            let count=0
            for (let i = 0; i <this.ajaxData.length ; i++) {
              //判断是否为当前节点的子节点
              if(node.id==this.ajaxData[i].pid){
                count++;
                this.diguiNode(this.ajaxData[i]);
                this.jsonStr+=",";
              }
            }
            //处理多余的,号
            if(count!=0){
              this.jsonStr=this.jsonStr.substr(0,this.jsonStr.length-1);
            }



            //拼完整
            this.jsonStr+=']}';
          }else{
            this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'"}';
          }

        }
        ,isParent:function(node){ // 判断是否为父节点  pid 有没有指向当前id

          for (let i = 0; i <this.ajaxData.length ; i++) {
            if(node.id==this.ajaxData[i].pid){
              return true;
            }
          }
          return false;
        },
        handleBucketClick(v){
          //alert(v.id)
          console.log(v.id);
          this.currentbucket=v.id;
          this.addTypeForm.pid=v.id;
          this.addTypeForm.sjname=v.label;

          this.updateTypeForm.id=v.id;
          this.updateTypeForm.name=v.label;
        },
        addForm:function () {
          this.$axios.post("http://localhost:8080/api/type/add",this.$qs.stringify(this.addTypeForm)).then(res=>{
            this.addFormFlag=false;

          }).catch(err=>console.log(err))
        },
        updateForm:function (rs) {
          console.log(rs)
          this.$axios.post("http://localhost:8080/api/type/update",this.$qs.stringify(this.updateTypeForm)).then(res=>{
            this.updateFormFlag=false;

          }).catch(err=>console.log(err))
        }
        

        
      },
      /*created:function () {
        //取远程数据
        this.$axios.get("http://localhost:8080/api/type/getData").then(res=>{
          this.ajaxData=res.data.data;//把拿到的数据赋给全局
          this.chandleData();
        }).catch(err=>console.log(err));
      }*/
      created:function () {
        var a =this;
        axios.get("http://localhost:8080/api/type/getData").then(function (res) {
          a.ajaxData=res.data.data;//把拿到的数据赋给全局
          a.chandleData();

        }).catch(function (err) {
          console.log(err)
        })
      }
    
    }
</script>

<style scoped>

</style>
