<template>
    <form class="row">
        <div class="form-group">
            <p id="title">请设置6位数字作为支付密码</p>
            <div class="pwd-box">
                <input type="tel" class="pwd-input" maxlength="6" v-model="newPwd" id="newPwd" name="newPwd" value="" ref="newPwd">
                <div class="faKe">
                    <span class="fake-box"><i class="dot1"></i></span>
                    <span class="fake-box"><i class="dot2"></i></span>
                    <span class="fake-box"><i class="dot3"></i></span>
                    <span class="fake-box"><i class="dot4"></i></span>
                    <span class="fake-box"><i class="dot5"></i></span>
                    <span class="fake-box"><i class="dot6"></i></span>
                </div>
            </div>
        </div>
        <div class="submit-box">
            <p>钱包支付时，启用支付密码</p>
            <a type="submit" class="submit-btn grey-bg" @click="onSave($event)" v-if="!goSub">确认</a>
            <a type="submit" class="submit-btn grey-bg" @click="onSumbit($event)" v-if="goSub">再次确认</a>
        </div>
    </form>
</template>

<style>
    .row {
        font-size: 36px;
        color: #1a191e;
        margin: 20px 0 0;
        padding: 0 36px;
    }

    .form-group {
        margin: 0 0 46px;
    }

    .form-group p {
        line-height: 96px;
    }

    .submit-box {
        color: #1a191e;
        position: fixed;
        left: 36px;
        bottom: 42px;
        text-align: center;
        font-size: 30px;
        line-height: 120px;
    }

    .submit-btn {
        font-size: 36px;
        display: block;
        width: 678px;
        height: 88px;
        line-height: 88px;
        border-radius: 44px;
        margin: 0 auto;
    }

    .pwd-box {
        position: relative;
    }

    .pwd-input {
        position: absolute;
        top: 200px;
        left: 0;
        width: 100%;
        height: 104px;
        opacity: 0;
        background: #fff;
        z-index: -1;
    }

    .pwd-input:focus{
        left:-1000px;
        top: -100px;
    }

    .faKe {
        overflow: hidden;
        width: 100%;
        margin: 0 auto;
    }

    .fake-box {
        display: block;
        float: left;
        width: 100px;
        height: 100px;
        border: 2px solid #ccc;
        background: #fff;
        margin-right: 10px;
    }

    .fake-box i {
        width: 20px;
        height: 20px;
        background: #1a191e;
        border-radius: 10px;
        margin: 40px auto;
        display: none;
    }

    .fake-box:last-child {
        margin-right: 0;
    }

    /*表单错误提示*/
    .has-error label {
        color: #e73d4a !important;
    }

    .has-error .checkbox, .has-error .checkbox-inline, .has-error .control-label, .has-error .form-control-feedback, .has-error .help-block, .has-error .radio, .has-error .radio-inline, .has-error.checkbox label, .has-error.checkbox-inline label, .has-error.radio label, .has-error.radio-inline label {
        color: #e73d4a !important;
    }

    .help-block {
        display: block;
        width: 100%;
        float: left;
        margin: 16px 0;
        color: #6789ab;
        font-size: 26px;
        line-height: 36px;
    }
</style>

<script type="es6">
    import './lib/jquery.min.js';
    import './lib/jquery.validate.min.js';
    import './lib/validateInit.js';
    import './lib/jquery.blockui.min.js';

    export default {
        data() {
            return {
                _passForm: null,
                newPwd: '',
                goBack: '',
                goSub: false,
                oldPwd: ''
            }
        },
        methods: {
            onSave: function (e) {
                e.preventDefault();
                let $input = $(".pwd-input");
                if (this._passForm.valid()) { //验证支付密码是否合法
                    $("#title").text('请再次输入支付密码'); //修改提示
                    this.goSub = true; //控制按钮显示
                    $(".fake-box i").css('display', 'none'); //清除span中的小圆点
                    this.oldPwd= $input.val();  //储存第一次输入的支付密码
                    $input.val('');     //清除input的value
                    this.newPwd = '';   //清除v-model绑定的值
                }
            },
            onSumbit: function (e) {
                e.preventDefault();
                if (this._passForm.valid()) { //jq的表单验证
                    if (this.newPwd === this.oldPwd) { //判断两次输入的支付密码是否相同
                        $.ajax({
                            url: '/api/user/changePassword.do',
                            type: 'post',
                            data: {
                                newPwd: this.newPwd,
                                action: 'payPwd'
                            }
                        }).done((result) => {
                            if (result.errCode === 0) {
                                if (this.goBack === 'user') {
                                    window.location.href = '/user/index.html';
                                } else {
                                    window.location.href = '/order/list.html';
                                }
                            }
                        });
                    } else {
                        //blockUI的错误提示插件
                        $.blockUI({
                            message: '<div class="loading-message-erro loading-message">两次输入的密码不一样</div>',
                            css: {
                                width: '54%',
                                top: '30%',
                                left: '23%',
                                cursor: "wait",
                                border: '0',
                                '-webkit-border-radius': '10px',
                                background: "#ededed"
                            }
                        });
                        setTimeout(function () {
                            console.log('setTimeout');
                            $.unblockUI();
                        }, 500);
                    }
                }
            },
        },
        created() {
            this.goBack = window.location.hash.split('#')[1]; //判断来源，修改成功跳转相应的页面
        },
        watch: {
            newPwd: {
                handler: function (val, oldval) {
                    let valL = val.length;
                    let ovalL = oldval.length;
                    if (valL < ovalL) {         //通过比对val和oldval的长度来判断用户是在输入还是在删除
                        $('.dot' + ovalL).css('display', 'none');   //通过val的长度拼接$选择的类名来与html中的类名对应起来
                    } else {
                        $('.dot' + valL).css('display', 'block');
                    }
                }
            },
            deep: true
        }
        ,
        mounted() {
            //jq的表单验证
            this._passForm = $('#passForm');
            this._passForm.validate({
                rules: {
                    newPwd: {
                        required: true,
                        number: true
                    }
                }
            });

            $('.fake-box').click(function () {
                $(".pwd-input").focus();
            });
        }
    }
</script>