<template>

    <div class="login">
        <!-- 登录 -->
        <mochi-box v-show="showLoginForm" class="loginClass"  shiba="monaka" mood="drool" blush left-eye="open" right-eye="laugh" left-ear="down" right-ear="down">
            <el-form ref="loginForm" :model="loginForm" :rules="loginRules" autocomplete="off" >
                <el-form-item >
                    <h2 class="loginH2">宠物之家后台登录</h2>
                </el-form-item>
                <!-- 账号 -->
                <el-form-item prop="name" >
                    <el-input v-model="loginForm.name" placeholder="请输入用户名" ></el-input>
                </el-form-item>
                <!-- 密码 -->
                <el-form-item prop="password" >
                    <el-input type="password" v-model="loginForm.password" placeholder="请输入密码" show-password ></el-input>
                </el-form-item>
                <!-- 验证码 -->
                <el-form-item prop="yzm" >
                    <el-row :gutter="20">
                        <el-col :span="10">
                            <!-- @keyup.enter.native添加回车事件，按回车直接登录 -->
                            <el-input v-model="loginForm.yzm" @keyup.enter.native="submitLoginForm('loginForm')"  placeholder="输入验证码"  ></el-input>
                        </el-col>
                        <el-col :span="8">
                            <img :src= "url" alt="加载失败">
                        </el-col>
                        <el-col :span="6">
                            <el-button  type="text" @click="getCode()" >换一换</el-button>
                        </el-col>
                    </el-row>
                </el-form-item>
                <el-form-item >
                    <el-button class="loginBotton" type="primary" @click="submitLoginForm('loginForm')">登录</el-button>
                    <el-button class="regBotton" type='text' @click="showLoginForm = false,showRegForm = true">还没有账号？点击注册</el-button>
                </el-form-item>
            </el-form>
	    </mochi-box>
        <!-- 注册 -->
        <mochi-box v-show="showRegForm" class="regClass"  shiba="monaka" mood="drool" blush left-eye="open" right-eye="laugh" left-ear="down" right-ear="down">
            <el-form status-icon ref="regForm" :model="regForm" :rules="regRules" >
                <el-form-item >
                    <h2 class="loginH2">宠物之家后台注册</h2>
                </el-form-item>
                <!-- 账号 -->
                <el-form-item prop="userName" >
                    <el-input v-model="regForm.userName" placeholder="请输入用户名" @blur="checkUserName(userName)"></el-input>
                </el-form-item>
                <!-- 密码 -->
                <el-form-item prop="password" >
                    <el-input type="password" v-model="regForm.password" placeholder="请输入密码" show-password ></el-input>
                </el-form-item>
                <!-- 确认密码 -->
                <el-form-item prop="confirmPassword" >
                    <el-input type="password" v-model="regForm.confirmPassword" @keyup.enter.native="submitRegForm('regForm')" placeholder="请再次输入密码" show-password ></el-input>
                </el-form-item>
                <!-- 头像上传 -->
                <el-form-item prop="pic" >
                    <el-upload
                        name="file"
                        class="upload-demo"
                        :action="path + '/upload/fileUpload'"
                        :on-success="picSuccess"
                        :limit="1"
                        :on-exceed="exceed"
                        :before-upload="beforeUpload"
                        list-type="picture">
                        <el-button size="small" type="primary">上传头像（只能上传一张）</el-button>
                        <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过2MB</div>
                    </el-upload>
                </el-form-item>
                <!-- 注册和返回 -->
                <el-form-item >
                    <el-button size="medium" type="primary" @click="submitRegForm('regForm')">注册</el-button>
                    <el-button size="medium" class="resetClass" @click="resetRegist('regForm')" >返回</el-button>
                </el-form-item>
            </el-form>
	    </mochi-box>
    </div>
</template>

<script>
import MochiBox from "@/components/Mochi/MochiBox.vue";
import { Result } from 'element-ui';
export default {
    //
    // name: "WaveItem",

    components:{
        MochiBox
    },
    data(){

        //验证两次输入的密码是否一样
        var validatePass = (rule, value, callback) => {
            if (value === '') {
            callback(new Error('请再次输入密码'));
            } else if (value !== this.regForm.password) {
            callback(new Error('两次输入密码不一致!'));
            } else {
            callback();
            }
        };
        //定义初始化变量
        return{
            checkedUserName:true, //用户名重复不能注册设置
            url:'',
            code:'',
            showLoginForm:true,
            showRegForm:false,
            loginForm:{
                name:'',
                password:'',
                yzm:''
            },
            regForm:{
                userName:'',
                password:'',
                confirmPassword:'',
                pic:''
            },
            loginRules:{
                name: [
                    { required: true, message: '请输入用户名', trigger: 'blur' }
                ],
                password:[
                    { required: true, message: '请输入密码', trigger: 'blur' }
                ],
                yzm:[
                    { required: true, message: '请输入验证码', trigger: 'blur' }
                ]
            },
            regRules:{
                userName: [
                    { required: true, message: '请输入用户名', trigger: 'blur' }
                ],
                password:[
                    { required: true, message: '请输入密码', trigger: 'blur' }
                ],
                confirmPassword:[
                    { validator:validatePass, required: true, trigger: 'blur' }
                ]
            }
        }
    },

    //JS定义方法的地方，所有的方法都应该在此书写
    methods:{
        
        //重置注册表单
        resetRegist(formName){
            this.$refs[formName].resetFields()
            this.showLoginForm = true
            this.showRegForm = false
        },
        //校验用户名是否重复
        checkUserName(){
            this.$http.get('/user/checkUserName/'+this.regForm.userName).then(res=>{
                if(res.data.code !== 200){
                    this.$message.error('用户名重复')
                    this.checkedUserName = false
                }else{
                    this.checkedUserName = true
                }
            })
        },
        //获取后端验证码图片的函数
        getCode(){
            this.$http.get('/user/getCode',{responseType: 'blob'}).then(res=>{
                //将后端返回的二进制图片信息转化为Blob类型
                let blob = new Blob([res.data])
                //将blob对象转化为可以直接访问的url对象
                this.url = window.URL.createObjectURL(blob)
                //将后台生成的验证码内容存储到页面中，用来和用户输入的验证码信息作比对
                this.code = res.headers.code
            })
        },
        //文件上传成功的回调函数
        picSuccess(response,flie,fileList){
            this.regForm.pic = response.data
        },
        //文件超出上传个数时触发的函数
        exceed(){
            this.$message.error('头像只能上传一张！')
        },
        //文件上传之前的校验的函数
        beforeUpload(file){
            if(file.type !== 'image/png' && file.type !== 'image/jpeg'){
                this.$message.error('头像格式只能是jpg/png')
                return false
            }
            return true
        },
        //点击登录按钮触发的函数
        submitLoginForm(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    //判断输入的验证码是否正确
                    if(this.loginForm.yzm.toLowerCase() === this.code.toLowerCase()){
                        //此处将向后台服务器发送异步请求，get()表示请求类型 
                        //then()表示后台接口返回的响应数据的回调函数 res表示后台返回的数据
                        this.$http.get('/user/login',{params:{
                            userName:this.loginForm.name,
                            password:this.loginForm.password
                        }}).then(res=>{
                            if(res.data.code === 200){
                                this.$message.success('登录成功')
                                //sessionStorage.setItem用来对浏览器输入的数据存储
                                //value 存储的值必须是字符串，如果存储的值为对象则需要使用JSON.stringify()将js对象转化为JSON字符串
                                sessionStorage.setItem('user',JSON.stringify(res.data.data))
                                //this.$router.push(path) js路由跳转 path为跳转到哪个页面的
                                this.$router.push('/home')
                            } else{
                                this.$message.error('登录失败')
                            }
                        })
                    } else{
                        this.$message.error('验证码错误')
                    }
                } else {
                    this.$message.error('表单校验失败请检查参数')
                    return false;
                }
            });
        },
        //点击注册按钮触发的函数
        submitRegForm(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    if(this.checkedUserName){
                        if(this.regForm.password === this.regForm.confirmPassword){
                            //axios发送post请求传一个js对象的时候直接把js对象放到方法参数里
                            //参数类型默认为JSON字符串
                            this.$http.post('/user/addUser',this.regForm).then(res=>{
                            if(res.data.code === 200){
                                this.$message.success('注册成功')
                                this.$refs[formName].resetFields()
                                this.showRegForm = false
                                this.showLoginForm = true
                                }else{
                                    this.$message.error('注册失败')
                                }
                            })
                        }else{
                            this.$message.error('两次密码输入不一致')
                        }
                    }else{
                        this.$message.error('用户名重复')
                    }
                } else {
                    this.$message.error('表单校验失败请检查参数')
                    return false;
                }
            });
        }
    },
    //当页面一加载完毕就会执行的方法
    created(){
        this.getCode()
    }
}
</script>

<!--scoped作用是为了让本页面书写的样式不会影响到其他页面，只对本页面有效-->
<style scoped>
    .el-form{
        width: 315px;
    }
    @import url('Login.css');
</style>