<template>
<div>
  <div>
    <el-input size="small" class="addPosInput"
        placeholder="添加职位"
        suffix-icon="el-icon-plus"
        @keydown.enter.native="addPosition"
        v-model="pos.name">
    </el-input>
    <el-button type="primary" icon="el-icon-plus" size="small"
               @click="addPosition">添加</el-button>
  </div>

  <div class="posMana">
    <el-table
        border
        stripe
        size="small"
        :data="positions"
        @selection-change="handleSelectionChange"
        style="width: 70%">
      <el-table-column
          type="selection"
          width="55">
      </el-table-column>
      <el-table-column
          prop="id"
          label="编号"
          width="55">
      </el-table-column>
      <el-table-column
          prop="name"
          label="职位"
          width="120">
      </el-table-column>
      <el-table-column
          prop="createDate"
          label="创建时间"
          width="200">
      </el-table-column>
      <el-table-column
          label="操作">
        <template slot-scope="scope">
          <el-button
              size="mini"
              @click="showEditView(scope.$index, scope.row)">编辑</el-button>
          <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
  <el-button size="small" style="margin-top: 8px" type="danger" :disabled="this.multipleSelection.length==0"
  @click="deleteMany">批量删除</el-button>
  <el-dialog
      title="编辑职位"
      :visible.sync="dialogVisible"
      width="30%">
    <div>
      <el-tag>职位名称</el-tag>
      <el-input size="small" v-model="updatePos.name" class="updatePosInput"></el-input>
    </div>
    <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="dialogVisible = false">取 消</el-button>
    <el-button size="small" type="primary" @click="daUpdate">确 定</el-button>
  </span>
  </el-dialog>
</div>
</template>

<script>
export default {
  name: "PosMana",
  data(){
    return {
      pos:{
        name:''
      },
      positions: [],
      dialogVisible:false,
      updatePos:{
        name:''
      },
      multipleSelection: []
    }
  },
  mounted(){
    this.initPosition()
  },
  methods:{
    deleteMany(){
      this.$confirm('此操作将永久删除['+this.multipleSelection.length+']条职位, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let ids='?';
        this.multipleSelection.forEach(item=>{
          ids+='ids='+item.id+'&';
        })
        this.deleteRequest('/system/basic/pos/'+ids).then(resp=>{
          if(resp){
            this.initPosition();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    daUpdate(){
      this.putRequest('/system/basic/pos/',this.updatePos).then(resp=>{
        if(resp){
          this.initPosition();
          this.dialogVisible=false;
        }
      })
    },
    showEditView(index,data){
        Object.assign(this.updatePos,data)
        this.dialogVisible=true;
        this.updatePos.createDate=''
    },
    handleDelete(index,data){
      this.$confirm('此操作将永久删除['+data.name+']职位, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/pos/'+data.id).then(resp=>{
          if(resp){
            this.initPosition();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addPosition(){
      if(this.pos.name){
        this.postRequest('/system/basic/pos/',this.pos).then(resp=>{
          if(resp){
            this.initPosition();
            this.pos.name='';
          }
        })
      }else {
        this.$message.error('职业名称不能为空')
      }
    },
    initPosition(){
      this.getRequest('/system/basic/pos/').then(res=>{
        if(res){
          this.positions=res
        }
      })
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    }
  }
}
</script>

<style scoped>
.addPosInput{
  width: 300px;
  margin-right: 8px;
}
.posMana{
  margin-top: 10px;
}
.updatePosInput{
  width: 200px;
  margin-left: 8px;
}
</style>
