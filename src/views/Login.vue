<template>
    <div class="login-wrap">
        <div class="ms-login">
            <div class="ms-title">后台管理系统</div>
            <el-form :model="param" :rules="rules" ref="login" label-width="0px" class="ms-content">
                <el-form-item prop="username">
                    <el-input v-model="param.phone" placeholder="手机号">
                        <template #prepend>
                            <el-button icon="el-icon-user"></el-button>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input
                        type="password"
                        placeholder="密码"
                        v-model="param.password"
                        @keyup.enter="submitForm()"
                    >
                        <template #prepend>
                            <el-button icon="el-icon-lock"></el-button>
                        </template>
                    </el-input>
                </el-form-item>
                <div class="login-btn">
                    <el-button type="primary" @click="submitForm()">登录</el-button>
                </div>
                <p class="login-tips">Tips : 手机号:17391856792 密码：123456。</p>
            </el-form>
        </div>
    </div>
</template>

<script>
import { login } from "../api/login";
export default {
    data() {
        return {
            param: {
              phone: "",
                password: ""
            },
            rules: {
              phone: [
                    { required: true, message: "请输入手机号", trigger: "blur" }
                ],
                password: [
                    { required: true, message: "请输入密码", trigger: "blur" }
                ]
            }
        };
    },
    created() {
        this.$store.commit("clearTags");
    },
    methods: {
        submitForm() {
            // 验证是否输入完整
            this.$refs.login.validate(valid => {
                if (valid) {
                  // 登录获取用户信息
                  login(this.param).then(res => {
                    if(res.status!==200){
                      this.$message.error(res.data)
                      return
                    }
                    sessionStorage.setItem("token", res.data.token);
                    sessionStorage.setItem("userInfo", res.data.userInfo);
                    this.$message.success('登录成功')
                    // 跳转首页
                    this.$router.push('/index')
                  })
                } else {
                    this.$message.error("请输入账号和密码");
                    return false;
                }
            });

        }
    }
};
</script>

<style scoped>
.login-wrap {
    position: relative;
    width: 100%;
    height: 100%;
    /*background-image: url(../assets/img/login-bg.jpg);*/
    background-size: 100%;
}
.ms-title {
    width: 100%;
    line-height: 50px;
    text-align: center;
    font-size: 20px;
    color: #fff;
    border-bottom: 1px solid #ddd;
}
.ms-login {
    position: absolute;
    left: 50%;
    top: 50%;
    width: 350px;
    margin: -190px 0 0 -175px;
    border-radius: 5px;
    background: rgba(255, 255, 255, 0.3);
    overflow: hidden;
}
.ms-content {
    padding: 30px 30px;
}
.login-btn {
    text-align: center;
}
.login-btn button {
    width: 100%;
    height: 36px;
    margin-bottom: 10px;
}
.login-tips {
    font-size: 12px;
    line-height: 30px;
    color: #fff;
}
</style>