<template>
    <div id="login">
        <div class="login-wrap">
            <ul class="menu-tab">
                <li :class="{'current': item.current}" v-for="item in menuTab" :key="item.id" @click="toggleMenu(item)">{{ item.txt }}</li>
            </ul>
            <!-- 表单 start -->
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size="medium">
                <el-form-item prop="userName" class="item-form">
                    <label>邮箱</label>
                    <el-input type="text" v-model="ruleForm.userName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item prop="password" class="item-form">
                    <label>密码</label>
                    <el-input type="text" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
                </el-form-item>
                <el-form-item prop="passwords" class="item-form" v-show="model === 'register'">
                    <label>重复密码</label>
                    <el-input type="text" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
                </el-form-item>
                <el-form-item prop="code" class="item-form">
                    <label>验证码</label>
                    <el-row :gutter="10">
                        <el-col :span="14"><el-input v-model.number="ruleForm.code" minlength="6" maxlength="6"></el-input></el-col>
                        <el-col :span="10"><el-button type="success" class="block">获取验证码</el-button></el-col>
                    </el-row>
                </el-form-item>
                <el-form-item>
                    <el-button type="danger" @click="submitForm('ruleForm')" class="block login-btn">提交</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>
<script>
import { stripscript, validateEmail, validatePass, validateCode } from '@/utils/validate';
export default {
    name: "login",
    components: {},
    data(){
        // 验证用户名
        var validateUserName = (rule, value, callback) => {
            if (value === '') {
                callback(new Error('请输入用户名'));
            } else if(validateEmail(value)) {
                callback(new Error('用户名格式有误'));
            } else {
                callback();
            }
        };
        // 验证密码
        var validatePassword = (rule, value, callback) => {
            this.ruleForm.password = stripscript(value);
            value = this.ruleForm.password;
            if (value === '') {
                callback(new Error('请输入密码'));
            } else if (validatePass(value)) {
                callback(new Error('密码为6-20位数字+字母'));
            } else {
                callback();
            }
        };
        //验证重复密码
        var validatePasswords = (rule, value, callback) => {
            // 如果模块值为login，直接通过
            if(this.model === 'login') {
                callback();
            }
            this.ruleForm.passwords = stripscript(value);
            value = this.ruleForm.passwords;
            if (value === '') {
                callback(new Error('请输入密码'));
            } else if (value != this.ruleForm.password) {
                callback(new Error('两次密码输入不一致'));
            } else {
                callback();
            }
        };
        // 验证验证码
        var checkCode = (rule, value, callback) => {
            if (value === '') {
                return callback(new Error('验证码不能为空'));
            } else if(validateCode(value)) {
                return callback(new Error('验证码格式有误'));
            } else {
                callback();
            }
        };
        return {
            menuTab: [
                {txt: '登陆', current: true, model: 'login'},
                {txt: '注册', current: false, model: 'register'}
            ],
            // 模块
            model: 'login',
            // 表格数据
            ruleForm: {
                userName: '',
                password: '',
                passwords: '',
                code: ''
                },
            rules: {
                userName: [
                    { validator: validateUserName, trigger: 'blur' }
                ],
                password: [
                    { validator: validatePassword, trigger: 'blur' }
                ],
                passwords: [
                    { validator: validatePasswords, trigger: 'blur' }
                ],
                code: [
                    { validator: checkCode, trigger: 'blur' }
                ]
            }
        };
    },
    created(){},
    mounted(){},
    methods: {
        toggleMenu(data){
            this.menuTab.forEach(element => {
                element.current = false;
            });
            data.current = true;
            this.model = data.model;
        },
        submitForm(formName) {
            this.$refs[formName].validate((valid) => {
            if (valid) {
                alert('submit!');
            } else {
                console.log('error submit!!');
                return false;
            }
            });
        }
    }
}
</script>
<style lang="scss" scope>
    #login {
        height: 100vh;
        background-color: #344a5f;
    }
    .login-wrap {
        width: 300px;
        margin: auto;
    }
    .menu-tab {
        text-align: center;
        li {
            display: inline-block;
            width: 88px;
            line-height: 36px;
            font-size: 14px;
            color: #fff;
            border-radius: 2px;
            cursor: pointer;
        }
        .current {
            background-color: rgba(0,0,0, .1);
        }
    }
    .login-form {
        margin-top: 29px;
        label {
            display: block;
            font-size: 14px;
            color: #fff;
            margin-bottom: 3px;
        }
        .item-form {
            margin-bottom: 13px;
        }
        .block {
            display: block;
            width: 100%;
        }
        .login-btn {
            margin-top: 19px;
        }
    }
</style>