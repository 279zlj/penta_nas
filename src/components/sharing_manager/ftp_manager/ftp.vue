<template>
    <div class="content">
        <div class="tip_bg">
            <span class="tip">FTP、SFTP {{$t('message.management')}}</span>
        </div>
        <div>
            <el-row class="other_table">
              <div style="width:96%;margin:0 auto">
                    <el-table :data='ftpdata' border class="table_cell" :header-cell-style="getRowClass" style="width:100%;min-height:10rem;" >
                        <el-table-column :label="$t('message.service')" prop='service'></el-table-column>
                        <el-table-column :label="$t('message.user')" prop="user" >
                            <template slot-scope="scope">
                                <span v-for='(i,index) in scope.row.user' :key='index'>
                                    {{i}}
                                </span>
                            </template>
                        </el-table-column>
                        <el-table-column :label="$t('message.oper')">
                            <template slot-scope="scope">
                                <el-switch v-model="scope.row.state" :active-text="$t('message.open')" :inactive-text="$t('message.close')" @change="changestate(scope.row)" :disabled="scope.row.operchange"></el-switch>
                                <span style="font-size:small;color:red" v-show="scope.row.user.indexOf('—') === 0">（{{$t('user.nouser')}}）</span>
                            </template>
                        </el-table-column>
                    </el-table>
              </div>
            </el-row>
        </div>
    </div>    
</template>
<script>
export default {
    name:'ftp',
    data(){
        return{
            getRowClass:{
                'background-color':'#009588',
                'color':'#fff'
            },
            ftpdata:[]
        }
    },
    mounted(){
        this.getftp()
    },
    methods:{
        getftp(){
            var _this=this
            this.$axios.get(this.$host+'ftp').then(res=>{
                _this.ftpdata=res.data.data
                _this.getuser()
            }).catch(error=>{
                console.log(error)
            })
            
        },
        getuser(){
            this.$axios.get(this.$host+'users').then(res=>{
                var ftp_users=[]
                var sftp=[]
                for(let i=0;i<res.data.data.length;i++){
                    if(res.data.data[i].last_name == 'ftp')
                        ftp_users.push(res.data.data[i].username)
                    if(res.data.data[i].last_name == 'sftp')
                        sftp.push(res.data.data[i].username)
                }
                if(ftp_users.length == 0){
                    ftp_users.push('—')
                    this.ftpdata[0].operchange=true
                }
                if(sftp.length == 0){
                    sftp.push('—')
                    this.ftpdata[1].operchange=true
                }
                this.ftpdata[0].user = ftp_users
                this.ftpdata[1].user = sftp
            }).catch(error=>{
                console.log(error)
            })
        },
        changestate(row){
            this.$axios.post(this.$host+'ftp',{target:row.service,state:row.state}).then(res=>{
                if(res.data.success){
                    this.$message({
                        message:this.$t('message.success'),
                        type:'success',
                        offset:''
                    })
                }
                else if(!res.data.success){
                    this.$message.error(res.data.msg)
                }
                this.getuser()
            }).catch(error=>{
                console.log(error)
            })
        }
    }
}
</script>
<style>

</style>

