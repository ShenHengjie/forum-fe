<!--
 * @Author: hayyot
 * @Date: 2023-04-11 16:38:39
 * @Description: 铁沸物
 * @FilePath: \forum-fe\src\views\Login.vue
-->
<template>
    <div>
        <el-dialog v-bind="$attrs" v-on="$listeners" @open="onOpen" @close="onClose" title="登录享受更多权限" :close-on-click-modal ="false" append-to-body width="30%">
            <el-button class="cta" type="text" @click="outerVisible = true,showDialog = !showDialog">
                <span class="hover-underline-animation">点击注册</span>
                <svg viewBox="0 0 46 16" height="10" width="30" xmlns="http://www.w3.org/2000/svg" id="arrow-horizontal">
                    <path transform="translate(30)" d="M8,0,6.545,1.455l5.506,5.506H-30V9.039H12.052L6.545,14.545,8,16l8-8Z" data-name="Path 10" id="Path_10"></path>
                </svg>
                <cardCustomRightsDialog :visible.sync="showDialog"></cardCustomRightsDialog>
            </el-button>
            <el-button class="cta" type="text" @click="outerVisible = true,showForget = !showForget">
                <span class="hover-underline-animation">忘记密码</span>
                <svg viewBox="0 0 46 16" height="10" width="30" xmlns="http://www.w3.org/2000/svg" id="arrow-horizontal">
                    <path transform="translate(30)" d="M8,0,6.545,1.455l5.506,5.506H-30V9.039H12.052L6.545,14.545,8,16l8-8Z" data-name="Path 10" id="Path_10"></path>
                </svg>
                <forgetpassword :visible.sync="showForget"></forgetpassword>
            </el-button>
            <el-form ref="elForm" :model="formData" :rules="rules" size="medium" >
                <el-form-item label="" prop="email">
                    <el-input v-model="formData.email" placeholder="请输入邮箱" clearable :style="{width: '100%'}">
                    </el-input>
                </el-form-item>
                <el-form-item label="" prop="password">
                    <el-input v-model="formData.password" placeholder="请输入密码" clearable show-password
                              :style="{width: '100%'}"></el-input>
                </el-form-item>
                
            </el-form>
            <!-- <el-form-item label="" prop="yzm"> -->
                <el-input v-model="yzm" placeholder="请输入验证码" clearable :style="{width: '100%'}" >
                    <el-button slot="append" @click="pdYzm" v-show="flag">验证</el-button>
                </el-input>
                <verify v-show="flag" ref="verify" @changeOkLogin="callback"></verify>
            <!-- </el-form-item> -->
            <div slot="footer">
                <el-button type="info" @click="handelConfirm" :disabled="okLogin" class="edit">登录</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
import verify from "@/views/Verify.vue";
import axios from "axios";
export default {
    name:"mRegisters",
    inheritAttrs: false,
    components: { cardCustomRightsDialog: () => import('@/views/register.vue'),verify,forgetpassword:() => import('@/views/forgetpassword.vue') },
    props: [],
    data() {
        return {
            showDialog: false,
            showForget: false,
            formData: {
                email: undefined,
                password: undefined,
            },
            yzm: '',
            rules: {
                email: [{
                    required: true,
                    message: '请输入邮箱',
                    trigger: 'blur'
                },{
                    pattern: /^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/,
                    message: '请输入正确邮箱',
                    trigger: 'blur'
                }],
                password: [{
                    required: true,
                    message: '请输入密码',
                    trigger: 'blur'
                }],
                yzm: [{
                    required: true,
                    message: '请输入验证码',
                    trigger: 'blur'
                }],
            },
            flag:true,
            okLogin:true,
        }
    },
    computed: {},
    watch: {},
    created() {},
    mounted() {},
    methods: {
        onOpen() {},
        onClose() {
            this.$refs['elForm'].resetFields()
        },
        close() {
            this.$emit('update:visible', false)
        },
        handelConfirm() {
            // console.log(this.formData);
            this.$refs['elForm'].validate((valid) => {
                if (valid) {
                    var config = {
                        method: 'post',
                        url: 'http://47.107.225.176:8808/login',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        data : JSON.parse(JSON.stringify(this.formData))
                    };
                    axios(config).then(res => {
                        // console.log(res.data.code);
                        if(res.data.code == 201){
                            this.$message.error('登录失败，请检查账号密码是否正确');
                        }
                        if(res.data.code == 200){
                            // console.log(res.data);
                            localStorage.setItem('uid',res.data.data.uid);
                            localStorage.setItem('username', res.data.data.username);
                            localStorage.setItem('headImage',res.data.data.headImage);
                            localStorage.setItem('email',res.data.data.email);
                            localStorage.setItem('token',res.data.data.token);
                            this.$message({
                                message: '登录成功！',
                                type: 'success'
                            });
                            location.reload();
                        }
                    })
                }
                else {
                    // console.log('error submit!!');
                    this.$message.error('登录失败，请检查表单')
                    return false;
                }
            })
            /*this.$refs['elForm'].validate((valid) => {
                if (valid) {
                    let userList = {
                        // username: this.formData.username,
                        // password:this.formData.password,
                        username:'船长',
                        password:'1111',
                        email:'3229699520@qq.com'
                    };
                    var config = {
                        method: 'post',
                        url: 'http://47.107.225.176:8808/login',
                        headers: {
                            // 'User-Agent': 'Apifox/1.0.0 (https://www.apifox.cn)',
                            'Content-Type': 'application/json'
                        },
                        data : JSON.parse(JSON.stringify(this.formData))
                    };
                    // const _this = this
                    axios(config).then(res => {
                        // console.log(res.data.data)
                        // console.log(res.data.data.token)
                        // this.$emit('hasLogin',this.flag)
                        // const jwt = res.data.data.token
                        // const userInfo = res.data.data

                        // 把数据共享出去
                        // _this.$store.commit("SET_TOKEN", jwt)
                        // _this.$store.commit("SET_USERINFO", userInfo)

                        // 获取
                        // console.log(_this.$store.getters.getUser)
                        // location.reload();
                        // this.close()

                        // console.log(res);
                    })

                } else {
                    // console.log('error submit!!');
                    return false;
                }
            })*/
        },
        pdYzm(){
            this.$refs.verify.checkCode(this.yzm)
        },
        callback(flag){
            if(flag==1)this.okLogin = !this.okLogin
        }
    }
}

</script>
<style>
.cta {
    border: none;
    background: none;
}

.cta span {
    padding-bottom: 7px;
    letter-spacing: 4px;
    font-size: 14px;
    padding-right: 15px;
    text-transform: uppercase;
}

.cta svg {
    transform: translateX(-8px);
    transition: all 0.3s ease;
}

.cta:hover svg {
    transform: translateX(0);
}
、
.cta:active svg {
    transform: scale(0.9);
}

.hover-underline-animation {
    position: relative;
    color: black;
    padding-bottom: 20px;
}

.hover-underline-animation:after {
    content: "";
    position: absolute;
    width: 100%;
    transform: scaleX(0);
    height: 2px;
    bottom: 0;
    left: 0;
    background-color: #000000;
    transform-origin: bottom right;
    transition: transform 0.25s ease-out;
}

.cta:hover .hover-underline-animation:after {
    transform: scaleX(1);
    transform-origin: bottom left;
}
.edit {
  color: #fff;
  background-color: #66CC99;
  border-color: #66CC99;
}
.edit:hover,
.edit:focus {
  background: #66CC99;
  border-color: #66CC99;
  color: #fff;
}
</style>
