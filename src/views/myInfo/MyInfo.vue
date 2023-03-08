<template>
    <div class="myInfo">
        <el-card class="box-card">
            <div slot="header" class="clearfix">
                <h2 style="margin-left: 45%" >个人信息</h2>
            </div>
            <div class="text item">
                <el-row>
                    <el-col :span="6">
                        <div class="avatarAndUpload">
                            <div class="userAvatar">
                                <el-image class="avatarImage" :src="updateUserFrom.pic == ''?defaultUrl: path + updateUserFrom.pic"></el-image>
                            </div>
                            <div>
                                <el-upload
                                    class="upload-demo"
                                    :action="path + '/upload/fileUpload'"
                                    multiple
                                    :limit="1"
                                    :on-success="success"
                                    :on-exceed="handleExceed"
                                    :file-list="fileList">
                                    <el-button size="small" type="primary">点击上传</el-button>
                                    <div slot="tip" class="el-upload__tip">只能上传jpg/png文件(只能上传一张)</div>
                                </el-upload>
                            </div>
                        </div>
                    </el-col>
                    <el-col :span="8">
                        <el-form ref="updateUserFrom" :model="updateUserFrom" label-width="80px">
                            <el-form-item label="用户权限:">
                                <el-tag>
                                    <div>{{updateUserFrom.role === 1?'管理员': '普通用户'}}</div>
                                </el-tag>
                            </el-form-item>
                            <el-form-item label="用户姓名:">
                                <el-input v-model="updateUserFrom.userName" ></el-input>
                            </el-form-item>
                            <!-- <el-form-item  label="用户密码:">
                                <el-input v-model="updateUserFrom.password"></el-input>
                            </el-form-item> -->
                            <el-form-item label="用户性别:">
                                <el-select v-model="updateUserFrom.sex" placeholder="选择性别">
                                <el-option label="男" :value="1"></el-option>
                                <el-option label="女" :value="0"></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="用户年龄:">
                                <el-input v-model="updateUserFrom.age"></el-input>
                            </el-form-item>
                            <el-form-item label="出生日期:">
                                <el-date-picker
                                    v-model="updateUserFrom.birth"
                                    type="date"
                                    value-format="yyyy-MM-dd"
                                    placeholder="选择日期:">
                                </el-date-picker>
                            </el-form-item>
                            <el-form-item label="联系电话:">
                                <el-input v-model="updateUserFrom.phone"></el-input>
                            </el-form-item>
                            <el-form-item label="用户邮箱:">
                                <el-input v-model="updateUserFrom.email"></el-input>
                            </el-form-item>
                            <el-form-item>
                                <el-button type="primary" @click="updateUserInfo('updateUserFrom')">修改信息</el-button>
                                <el-button>重置</el-button>
                            </el-form-item>
                        </el-form>
                    </el-col>
                </el-row>
                
            </div>
        </el-card>
    </div>
</template>

<script>
export default {
    data(){
        return{
            defaultUrl:'touxiang.png',
            updateUserFrom:{
                id:'',
                userName:'',
                password:'',
                pic:'',
                sex:'',
                age:'',
                birth:'',
                phone:'',
                email:'',
                role:''
            },
            fileList:[]
        }
    },
    methods:{
        
        //超出文件个数提示
        handleExceed(){
            this.$message.error('头像只能上传一张')
        },
        updateUserInfo(formName){
            //修改信息时校验用户名是否重复
            this.$http.get('/user/checkUserNameByIdAndUserName/',{params:{
                    userName:this.updateUserFrom.userName,
                    id:this.updateUserFrom.id
            }}).then(res=>{
                if(res.data.code === 200){
                    this.$http.post('/user/updateUserInfo',this.updateUserFrom).then(res=>{
                        if(res.data.code === 200){
                            this.$message.success('修改成功')
                            sessionStorage.setItem('user',JSON.stringify(this.updateUserFrom))
                            // 强制页面刷新
                            setTimeout(()=>{
                                location.reload()   
                            },500)
                        }else{
                            this.$message.error('修改失败')
                        }
                    })
                }else{
                    this.$message.error('用户名已存在')
                }
            })
        },
        success(response,file,fileList){
            this.updateUserFrom.pic = response.data
        }
    },
    created(){
        // 从sessionStorage取用户信息
        let user = JSON.parse(sessionStorage.getItem('user'))
        if(user !== null){
            this.updateUserFrom = user
        }
    }
}
</script>

<style scoped>
    @import url('MyInfo.css');
</style>