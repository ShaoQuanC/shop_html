<template>
  <div>
    <el-tree
      :data="data"
      ref="tree"
      show-checkbox
      node-key="id"
      @node-click="clickNode"
      :check-on-click-node="true"
      :default-expand-all="true"
      :props="defaultProps"
      accordion>
    </el-tree>


  </div>
</template>

<script>
  export default {
    name: 'chil',
    data() {
      return {
        chil:{
          id:'',
          name:'',
          pid:''
        },
        dialogFormVisible:false,
        formLabelWidth:'120px',
        checkedNdoe:{
          id:'',
          name:'',
          pid:''
        },
        isShow:false,
        data: [{

        }],
        defaultProps: {
          dialogFormVisible:true,
          children: 'children',
          label: 'name'
        }
      };
    },
    created:function() {
      this.query()
    },
    methods: {

      downFile(){
        location.href="/productApi/file/downExcel2.do";
      },
      clickNode(data,none){
        this.checkedNdoe=data;
        this.isShow=true
        var pid=data.pid;
        this.$refs.tree.setChecked(pid,false,false);
      },
      query(){
        var aa = this;
        this.$ajax.get("http://localhost:8080/api/type/getData").then(function (res) {
          console.log(res)
          aa.data=res.data.data.typeList;
        }).catch(function (res) {
          alert("处理异常")
        })
      },
      add(){

        var aa = this;
        this.axios.post("/productApi/tree/addOrUpdate.do",this.$qs.stringify(this.chil)).then(function (res) {
          if (res.data.code==200){
            aa.query();
          }

        })
        this.chil.name=null;
      },
      edif(fige){
        if (fige==1){
          this.dialogFormVisible=true;
          this.chil.pid=this.checkedNdoe.id;
        }else if(fige==2){
          this.dialogFormVisible=true;
          this.chil=this.checkedNdoe;
        }

      },
      del(){
        var checkedbox = this.$refs.tree.getCheckedNodes()
        var idList=[];
        if (checkedbox!=null&&checkedbox.length>0){
          for (let i = 0; i < checkedbox.length; i++) {
            idList.push(checkedbox[i].id)
          }
          var aa = this;
          this.axios.post("/productApi/tree/delete.do",this.$qs.stringify({"idList":idList},{indices:false})).then(function (res) {
            if (res.data.code==200){
              aa.query();
            }

          })
        }else{
          this.$message('请选择要删除的节点');
        }
      }

    }
  }
</script>

<style scoped>

</style>
