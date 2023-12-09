

<template>
    <div class="box">
        <div class="title">登录</div>
        <el-form :rules="loginRules" :model="loginForm" ref="loginRef">
            <el-form-item prop="username">
                <el-input v-model="loginForm.username" prefix-icon="User" placeholder="请输入账号" />
            </el-form-item>
            <el-form-item prop="password">
                <el-input v-model="loginForm.password" prefix-icon="Lock" placeholder="请输入密码" />
            </el-form-item>
            <el-form-item>
                <el-button @click="clickLogin" type="primary" style="width:100%">登入</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                loginForm:{
                    username: "",
                    password: ""
                },
                // 校验账号密码规则
                loginRules:{
                    username:[
                        { required: true, message: '用户名账号不能为空', trigger: 'blur' },
                    ],
                    password:[
                        { required: true, message: '密码不能为空', trigger: 'blur' },
                    ]
                }
            }
        },
        methods:{
            async loginSubmit(){
                const response = await this.$api.login(this.loginForm)
                console.log('响应内容',response)
                if (response.data.code === 1000){
                    this.$message({
                        type:'success',
                        message:'登入成功'
                    })
                    //路由跳转
                    this.$router.push({name:'home'})
                }else{
                    this.$message({
                        type:'error',
                        message:response.data.message
                    })
                }
            },
            clickLogin(){
                //表单校验
                this.$refs['loginRef'].validate(res => {
                    console.log('校验结果',res)
                    this.loginSubmit()
                })
            }
        }
    }
</script>

<style scoped>
/* <!-- 登入框的样式 --> */
.box {
    width: 500px;
    height: 350px;
    margin: calc((100vh - 350px)/2) auto;
    box-shadow: 0 0 5px #52a2e4;
    border-radius: 20px;
}

/* 登入框的title */
.title {
    color: #00aaff;
    height: 80px;
    font: bold 28px/80px '微软雅黑';
    text-align: center;
}

/* 表单输入框及按钮样式 */
.el-form {
    margin: 20px;
}
</style>
