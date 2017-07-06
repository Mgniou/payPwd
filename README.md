# payPwd
vuejs移动端支付密码设置
思路：只用一个input来输入内容，使用6个span盒子来显示输入情况 。将input定位到span盒子下方，给span绑定点击事件给input添加focus来唤起键盘。
通过vue的watch方法来监控input的输入情况，通过比对val和oldval的长度来判断用户是在输入还是在删除，如果是输入则显示span中i标签绘制的小圆点，
删除则取消小圆点的显示。

html：
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

css:
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

js:
export default {
        watch: {
            newPwd: {
                handler: function (val, oldval) {
                    let valL = val.length;
                    let ovalL = oldval.length;
                    if (valL < ovalL) {
                        $('.dot' + ovalL).css('display', 'none'); //通过val的长度拼接$选择的类名来与html中的类名对应起来
                    } else {
                        $('.dot' + valL).css('display', 'block');
                    }
                }
            },
            deep: true
        }
        ,
        mounted() {
            $('.fake-box').click(function () {
                $(".pwd-input").focus();
            });
        },
    }
</script>
