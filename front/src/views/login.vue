<template>
<div class="login">
    <!-- Star Background -->
    <div class="stars"></div>
    <div class="twinkling"></div>
    
    <div class="login-container" @keydown.enter.native="submitLogin">
        <div class="login-header">
            <span class="tech-line"></span>
            <span class="title">固定资产管理系统</span>
            <span class="tech-line"></span>
        </div>
        
        <div class="login-card">
            <div class="card-glow"></div>
            <Tabs v-model="tabName" @on-click="changeTabName" class="loginTab">
                <TabPane label="账号登陆" name="userAndPassword">
                    <Form ref="usernameLoginForm" :model="form" :rules="usernameLoginFormRules" class="form">
                        <FormItem prop="username">
                            <Input v-model="form.username" size="large" clearable placeholder="请输入账号" autocomplete="off" class="tech-input">
                                <Icon class="iconfont icon-yonghu" slot="prefix" />
                            </Input>
                        </FormItem>
                        <FormItem prop="password">
                            <Input type="password" v-model="form.password" size="large" placeholder="请输入密码" password autocomplete="off" class="tech-input">
                                <Icon class="iconfont icon-mima1" slot="prefix" />
                            </Input>
                        </FormItem>
                        <FormItem prop="imgCode">
                            <Row type="flex" justify="space-between" align="middle">
                                <Input v-model="form.imgCode" size="large" clearable placeholder="验证码" :maxlength="10" class="tech-input input-verify" />
                                <div class="code-image">
                                    <Spin v-if="loadingCaptcha" fix></Spin>
                                    <img :src="captchaImg" @click="getCaptchaImg" alt="验证码" />
                                </div>
                            </Row>
                        </FormItem>
                    </Form>
                    
                    <div class="login-options">
                        <Checkbox v-model="saveLogin" class="tech-checkbox">自动登陆</Checkbox>
                        <router-link to="/regist" class="tech-link">注册账号</router-link>
                    </div>

                    <Button class="tech-btn" type="primary" size="large" :loading="loading" @click="submitLogin" long>
                        <span v-if="!loading">SYSTEM LOGIN</span>
                        <span v-else>ACCESSING...</span>
                    </Button>
                </TabPane>
                
                <TabPane label="扫码登陆" name="mobile">
                    <div id="qywxsmqywxsm" class="qr-container"></div>
                </TabPane>
            </Tabs>
        </div>

        <div class="login-footer">
            <p>COPYRIGHT © 2025 HIGH-TECH ASSETS SYSTEM</p>
        </div>
    </div>
</div>
</template>

<script>
// ... existing script logic keeping user's original logic ...
import {
    login,
    userInfo,
    initCaptcha,
    drawCodeImage
} from "@/api/index";
import Cookies from "js-cookie";
import util from "@/libs/util.js";
export default {
    components: {},
    data() {
        return {
            saoMaFx: false,
            captchaId: "",
            captchaImg: "",
            loadingCaptcha: false,
            error: false,
            tabName: "userAndPassword",
            saveLogin: true,
            loading: false,
            form: {
                username: "admin",
                password: "123456",
                mobile: "",
                code: ""
            },
            usernameLoginFormRules: {
                username: [{
                    required: true,
                    message: "账号不能为空",
                    trigger: "blur"
                }],
                password: [{
                    required: true,
                    message: "密码不能为空",
                    trigger: "blur"
                }],
                imgCode: [{
                    required: true,
                    message: "验证码不能为空",
                    trigger: "blur"
                }]
            }
        };
    },
    methods: {
        getCaptchaImg() {
            this.loadingCaptcha = true;
            initCaptcha().then(res => {
                this.loadingCaptcha = false;
                if (res.success) {
                    this.captchaId = res.result;
                    this.captchaImg = drawCodeImage + this.captchaId;
                }
            });
        },
        changeTabName(e) {
            if (e != "userAndPassword") {
                window.WwLogin({
                    "id": "qywxsmqywxsm",
                    "appid": "wwf94bb44e76e308f8",
                    "agentid": "1000002",
                    "redirect_uri": "https://artskyhome.com:8080/%23/login",
                    "state": "ZWZ1314520",
                    "href": "",
                });
            }
        },
        afterLogin(res) {
            let accessToken = res.result;
            this.setStore("accessToken", accessToken);
            userInfo().then((res) => {
                if (res.success) {
                    delete res.result.permissions;
                    let roles = [];
                    res.result.roles.forEach((e) => {
                        roles.push(e.name);
                    });
                    delete res.result.roles;
                    this.setStore("roles", roles);
                    this.setStore("saveLogin", this.saveLogin);
                    if (this.saveLogin) {
                        Cookies.set("userInfo", JSON.stringify(res.result), {
                            expires: 7,
                        });
                    } else {
                        Cookies.set("userInfo", JSON.stringify(res.result));
                    }
                    this.setStore("userInfo", res.result);
                    this.$store.commit("setAvatarPath", res.result.avatar);
                    util.initRouter(this);
                    this.$router.push({
                        name: "home_index",
                    });
                } else {
                    this.loading = false;
                }
            });
        },
        submitLogin() {
            this.$refs.usernameLoginForm.validate(valid => {
                if (valid) {
                    this.loading = true;
                    login({
                        username: this.form.username,
                        password: this.form.password,
                        code: this.form.imgCode,
                        captchaId: this.captchaId,
                        saveLogin: this.saveLogin
                    }).then(res => {
                        if (res.success) {
                            this.afterLogin(res);
                        } else {
                            this.loading = false;
                            this.getCaptchaImg();
                        }
                    });
                }
            });
        }
    },
    mounted() {
        this.getCaptchaImg();
    }
};
</script>

<style lang="less">
/* Tech Theme Variables */
@tech-bg: #050510;
@tech-primary: #00f3ff;
@tech-secondary: #7000ff;
@tech-surface: rgba(20, 20, 35, 0.7);
@tech-text: #e0e0ff;

html, body {
    height: 100%;
    overflow: hidden;
    margin: 0;
    padding: 0;
}

.login {
    height: 100vh;
    width: 100vw;
    background: radial-gradient(ellipse at bottom, #1b2735 0%, @tech-bg 100%);
    position: relative;
    overflow: hidden;
    color: @tech-text;
    font-family: 'Orbitron', 'Microsoft YaHei', sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
}

/* Star Animation */
.stars, .twinkling {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    width: 100%; height: 100%;
    display: block;
}
.stars {
    background: #000 url('http://www.script-tutorials.com/demos/360/images/stars.png') repeat top center;
    z-index: 0;
}
.twinkling {
    background: transparent url('http://www.script-tutorials.com/demos/360/images/twinkling.png') repeat top center;
    z-index: 1;
    animation: move-twink-back 200s linear infinite;
}
@keyframes move-twink-back {
    from {background-position:0 0;}
    to {background-position:-10000px 5000px;}
}

.login-container {
    z-index: 2;
    width: 100%;
    max-width: 450px;
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.login-header {
    margin-bottom: 30px;
    display: flex;
    align-items: center;
    width: 100%;
    justify-content: center;
}

.tech-line {
    height: 2px;
    width: 50px;
    background: linear-gradient(90deg, transparent, @tech-primary, transparent);
    margin: 0 15px;
}

.title {
    font-size: 24px;
    font-weight: bold;
    color: @tech-primary;
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
    letter-spacing: 2px;
}

.login-card {
    width: 100%;
    background: @tech-surface;
    backdrop-filter: blur(10px);
    border: 1px solid rgba(0, 243, 255, 0.3);
    border-radius: 10px;
    padding: 40px;
    position: relative;
    box-shadow: 0 0 30px rgba(0, 0, 0, 0.5), inset 0 0 20px rgba(0, 243, 255, 0.05);
}

.card-glow {
    position: absolute;
    top: -2px; left: -2px; right: -2px; bottom: -2px;
    border-radius: 12px;
    background: linear-gradient(45deg, @tech-primary, transparent, @tech-secondary);
    z-index: -1;
    opacity: 0.5;
    animation: glow-pulse 3s infinite alternate;
}

@keyframes glow-pulse {
    from { opacity: 0.3; }
    to { opacity: 0.7; }
}

/* Tabs */
.ivu-tabs-nav .ivu-tabs-tab {
    color: rgba(224, 224, 255, 0.6);
    font-size: 16px;
    transition: all 0.3s;
}
.ivu-tabs-nav .ivu-tabs-tab-active {
    color: @tech-primary;
    text-shadow: 0 0 8px @tech-primary;
}
.ivu-tabs-ink-bar {
    background-color: @tech-primary;
    box-shadow: 0 0 10px @tech-primary;
}

/* Inputs */
.tech-input input {
    background: rgba(0, 0, 0, 0.3) !important;
    border: 1px solid rgba(255, 255, 255, 0.1) !important;
    color: @tech-primary !important;
    border-radius: 4px;
    height: 45px;
    transition: all 0.3s;
}
.tech-input input:focus {
    border-color: @tech-primary !important;
    box-shadow: 0 0 10px rgba(0, 243, 255, 0.2);
}
.tech-input .ivu-input-prefix i {
    color: @tech-primary;
    line-height: 45px;
}
.tech-input input::placeholder {
    color: rgba(224, 224, 255, 0.3);
}

/* Verify Code */
.input-verify {
    width: 65%;
}
.code-image {
    width: 30%;
    height: 45px;
    border: 1px solid rgba(0, 243, 255, 0.3);
    border-radius: 4px;
    cursor: pointer;
    overflow: hidden;
    position: relative;
    
    img {
        width: 100%;
        height: 100%;
    }
}

/* Options */
.login-options {
    display: flex;
    justify-content: space-between;
    margin-bottom: 25px;
    margin-top: 10px;
}
.tech-checkbox .ivu-checkbox-inner {
    background-color: transparent;
    border-color: @tech-primary;
}
.tech-checkbox .ivu-checkbox-checked .ivu-checkbox-inner {
    background-color: @tech-primary;
    border-color: @tech-primary;
}
.tech-checkbox span {
    color: rgba(224, 224, 255, 0.7);
}
.tech-link {
    color: @tech-primary;
    text-decoration: none;
    transition: color 0.3s;
}
.tech-link:hover {
    color: #fff;
    text-shadow: 0 0 5px @tech-primary;
}

/* Button */
.tech-btn {
    background: linear-gradient(90deg, rgba(0, 243, 255, 0.2), rgba(112, 0, 255, 0.2));
    border: 1px solid @tech-primary;
    color: @tech-primary;
    font-family: 'Orbitron', sans-serif;
    letter-spacing: 3px;
    font-weight: bold;
    transition: all 0.3s;
    height: 50px;
}
.tech-btn:hover {
    background: @tech-primary;
    color: #000;
    box-shadow: 0 0 20px @tech-primary;
    border-color: @tech-primary;
}

.login-footer {
    margin-top: 40px;
    color: rgba(255, 255, 255, 0.3);
    font-size: 12px;
    letter-spacing: 1px;
}

.qr-container {
    height: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>

