<template>
    <el-dialog :title="$t('raidMgr.set')" :visible.sync="modifyraid" width="50%" center :before-close="handleClose" :close-on-click-modal="false">
        <el-form :model="modifydata" :rules="modifyrule" ref="modifydata" label-position="left" label-width="130px" class="demo-ruleFrom">
            <el-form-item :label="$t('raid.name')">
                {{nowdata.Name}}
            </el-form-item>
            <el-form-item :label="$t('raid.level')">
                {{nowdata.TYPE}}
            </el-form-item>
            <!-- <el-form-item :label="$t('raid.can')" prop="disks">
                <template>
                    <el-transfer v-model="modifydata.disks" :data="own_disks" :titles="[$t('raid.disk1'),$t('raid.disk2')]"></el-transfer>
                </template>
            </el-form-item> -->
            <el-form-item :label="$t('raid.read')" prop='readplot'>
                <el-select v-model="modifydata.readplot" :placeholder="$t('raid.select6')">
                <el-option :label="$t('raid.notinit')" value="nora"></el-option>
                <el-option :label="$t('raid.quit')" value='ra'></el-option>
                <el-option :label="$t('raid.all')" value='adra'></el-option>
                </el-select>
            </el-form-item>
            <el-form-item :label="$t('raid.write')" prop='writeplot'>
                <el-select v-model="modifydata.writeplot" :placeholder="$t('raid.select7')">
                <el-option :label="$t('raid.aa')" value="wt"></el-option>
                <el-option :label="$t('raid.aa1')" value='wb'></el-option>
                </el-select>
            </el-form-item>
            <el-form-item >
                <el-button type="primary" @click="modifysubmit('modifydata')">{{$t('message.submit')}}</el-button>
                <el-button @click="globalreset('modifydata')">{{$t('message.reset')}}</el-button>
            </el-form-item>
        </el-form>
    </el-dialog>
</template>
<script>
export default {
    name:'modify',
    props:['mraid','nowdata','own_disks'],
    data(){
        return{
            modifydata:{
                disks:[],
                readplot:'',
                writeplot:''
            },
            modifyrule:{
                // disks:[
                //     {validator:checkdisks, trigger: 'blur'}
                // ],
                // readplot:[
                //     {validator:checkread, trigger: 'blur'}
                // ],
                // writeplot:[
                //     {validator:checkwrite, trigger: 'blur'}
                // ]
            },
            modifyraid:false,
            
        }
    },
    watch:{
        mraid(val){
            this.modifyraid=val
        }
    },
    methods:{
        handleClose(done){
            done();
            this.$refs['modifydata'].resetFields();
            this.$emit('changemodify' ,false)
        },
        modifysubmit(name){
            this.$refs[name].validate((valid)=>{
                if(valid){

                }
            })
        },
        
        globalreset(name){
            this.$refs[name].resetFields()
        },
    }
}
</script>

