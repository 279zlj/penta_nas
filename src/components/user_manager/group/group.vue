<template>
    <div class="content">
        <div>
            <div class='tip_bg'>
                <span class='tip'>{{$t('message.group')}}</span>
            </div>
            <el-row class='main_table'>
                <div style="width:96%;margin:0 auto">
                <el-row style='margin-bottom:.5em;float:right'>
                    <el-tooltip :content="$t('message.add')" placement="bottom"><el-button type='primary' icon="el-icon-circle-plus" size='small' @click='creategroup = true'></el-button></el-tooltip>
                </el-row>
                <el-table :data='groupdata.slice((currpage - 1) * pagesize, currpage * pagesize)' border :header-cell-style="getRowClass"  class="table_cell" style='width:100%;min-height:32rem'>
                    <el-table-column :label="$t('group.name')" prop='name'></el-table-column>
                    <el-table-column :label="$t('group.id')" prop='id'></el-table-column>
                    <el-table-column :label="$t('message.oper')" width:='150'>
                        <template slot-scope='scope'>
                            <el-tooltip :content="$t('message.delete')" placement="bottom"><el-button type='danger' icon="el-icon-delete" size='mini' @click='deletegroup(scope.row)'></el-button></el-tooltip>
                        </template>
                    </el-table-column>
                </el-table>
                <el-pagination
                layout="total, sizes, prev, pager, next, jumper"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :page-sizes="[10, 20]"
                :page-size="pagesize"
                :total="pageTotal" style="text-align: right;margin: 1em">
                </el-pagination>
                </div>
            </el-row>
            <el-dialog :title="$t('group.new')" width="30%" :close-on-click-modal="false" :visible.sync="creategroup" :before-close="handleClose">
                <el-form :model='groupform' :rules='grouprule' ref='groupform' label-width="30" class="demo-ruleForm">
                    <el-form-item :label="$t('group.name')" prop='groupname'>
                        <el-input v-model="groupform.groupname" :placeholder="$t('group.input1')" style='width:80%' clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="groupsubmit('groupform')">{{$t('message.submit')}}</el-button>
                        <el-button @click="groupreset('groupform')">{{$t('message.reset')}}</el-button>
                    </el-form-item>
                </el-form>
            </el-dialog>
            <el-dialog :title="$t('group.delete')" :visible.sync="deletelog" width="30%" center :close-on-click-modal="false">
                <p>{{$t('group.delete')}}：{{grouptarget}}?</p>
                <el-button type="primary" @click='deleteg()'>{{$t('message.sure')}}</el-button>
                <el-button @click='deletelog=false'>{{$t('message.cancel')}}</el-button>
            </el-dialog>
        </div>
    </div>
</template>
<script>
export default {
    name:'group',
    data(){
        return{
            getRowClass:{
                'background-color':'#009588',
                'color':'#fff'
            },
            groupdata:[],
            creategroup:false,
            pagesize: 10,
            currpage: 1,
            pageTotal: 0,
            groupform:{
                groupname:''
            },
            grouprule:{
                groupname:[
                    {required:true,message:this.$t('group.input1'),trigger: 'blur'},
                    {pattern:/^[0-9a-zA-Z_]+$/,message:this.$t('user.reg'),trigger:'blur'},
                    {min:3,message:this.$t('group.input2'),trigger:'blur'}
                ]
            },
            grouptarget:'',
            deletelog:false
        }
    },
    mounted(){
        this.getgroup()
    },
    watch:{
      pageTotal(){
        if(this.pageTotal==(this.currpage-1)*this.pagesize&& this.pageTotal!=0){
          this.currpage-=1;
        //   getuser(this);//获取列表数据
        }
      }
    },
    methods: {
        getgroup(){
            var _this=this
            this.$axios.get(this.$host+'group').then(function(res){
                _this.groupdata = res.data.data     
                _this.pageTotal = _this.groupdata.length        
            })
        },
        groupsubmit(formname){
            var _this=this
            this.$refs[formname].validate((valid)=>{
                if(valid){
                    _this.$axios.post(this.$host+'group',{name:_this.groupform.groupname}).then(res=>{
                        _this.creategroup=false
                        if(res.data.success){
                            _this.$message({
                                message:this.$t('message.success'),
                                type:'success',
                                offset:''
                            })
                        }
                        else
                             _this.$message.error(res.data.msg)
                        _this.getgroup()
                        _this.groupreset('groupform')
                    }).catch(error=>{
                        console.log(error)
                    })
                }
            })
        },
        handleClose(done){
            done();
            this.$refs['groupform'].resetFields();
        },
        groupreset(formname){
            this.$refs[formname].resetFields();
        },
        deletegroup(row){
            this.deletelog=true;
            this.grouptarget=row.name
        },
        deleteg(){
            var _this=this
            this.$axios.delete(this.$host+'group',{data:{name:_this.grouptarget}}).then(res=>{
                _this.deletelog=false
                if(res.data.success){
                    _this.$message({
                        message:this.$t('message.success'),
                        type:'success',
                        offset:''
                    })
                }
                else if(!res.data.success){
                    _this.$message.error(res.data.msg)
                }
                _this.getgroup()
                _this.groupreset('groupform')
            }).catch(error=>{
                console.log(error)
            })
        },
        handleCurrentChange(cpage) {
          this.currpage = cpage;
        },
        handleSizeChange(psize) {
          this.pagesize = psize;
        },
    },    
}
</script>
<style>

</style>