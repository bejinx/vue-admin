<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li :class="{'current':item.current}" @click="toggleMneu(item)" v-for="item in menuTab" :key="item.id">{{item.txt}}</li>
      </ul>
      <!-- 表单 start -->
      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm"  class="class-from" size="medium ">
      <el-form-item  prop="username" class="item-from">
        <label>邮箱</label>
        <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item  prop="pass" class="item-from">
        <label>密码</label>
        <el-input type="password" v-model="ruleForm.pass" autocomplete="off" maxlength="20" minlength="6"></el-input>
      </el-form-item>
      <el-form-item  prop="verify" class="item-from">
        <label>验证码</label>
        <el-row :gutter="20">
          <el-col :span="15"> 
            <el-input v-model.number="ruleForm.verify" maxlength="6" minlength="6"></el-input>
          </el-col>
          <el-col :span="9">
            <el-button type="success" class="block">获取验证码</el-button>
          </el-col>
        </el-row>
       
      </el-form-item>
      <el-form-item>
        <el-button type="danger" @click="submitForm('ruleForm')" class="block">提交</el-button>
      </el-form-item>
       </el-form>
    </div>
  </div>
</template>
<script>
export default {
  name: "Home",
  // components: {},
  data(){
      var checkAge = (rule, value, callback) => {
        let reg = /^[a-z0-9]{6}$/
        if (!value) {
          return callback(new Error('请输入验证码'));
        }else if(!reg.test(value)){
          return callback(new Error('验证码不符合规则'));
        }
        else {
              callback();
          }
      };
      var validatePass = (rule, value, callback) => {
        //验证邮箱
        let reg = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
        if (value === '') {
          callback(new Error('请输入邮箱'));
        } else if(!reg.test(value)) {
          callback(new Error('用户名格式有误'));
        }
        else {
          callback();
        }
      };
      var validatePass2 = (rule, value, callback) => {
        //字母+数字
        let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/
        if (value === '') {
          callback(new Error('请输入密码'));
        } else if (!reg.test(value)) {
          callback(new Error('密码必须包含数字和字母'));
        }else if (value !== "123456") {
          callback(new Error('密码错误'));
        } else {
          callback();
        }
      };
    return {
      menuTab:[
        { txt:'登录',current:false},
        { txt:'注册',current:true}
      ],
      //表单的数据
       ruleForm: {
          username: '',
          pass: '',
          verify: ''
        },
        rules: {
          username: [
            { validator: validatePass, trigger: 'blur' }
          ],
          pass: [
            { validator: validatePass2, trigger: 'blur' }
          ],
          verify: [
            { validator: checkAge, trigger: 'blur' }
          ]
        }
    }
  },
  created(){},
  mounted(){},
  methods:{
    //vue 数据驱动视频渲染
    toggleMneu(data){
      //打印
      //console.log(data)
      this.menuTab.forEach(elem => {
        elem.current = false
      });
        data.current = true
    },
    //表单方法
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
};
</script>
<style lang="scss" scoped>
#login {
  height: 100vh;
  background-color: #344a5f;
}
.login-wrap{
  width: 300px;
  margin: auto;
}
.menu-tab{
  text-align: center;
  li{
    display: inline-block;
    width: 88px;
    line-height: 36px;
    font-size: 14px;
    color: #fff;
    //圆角
    border-radius: 2px;
    //切换鼠标手势
    cursor: pointer;
  }
}
.current{
  background-color: rgba($color: #000000, $alpha: 0.1);
}
.class-from{
  margin-top: 29px;
  label{
    display: block;
    margin-bottom: 3px;
    font-size: 14px;
    color: #fff;
  }
  .item-from{
    margin-bottom: 13px;
  }
  .block{
    display: block;
    width: 100%;
  }
}
</style>
