<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li v-for="item in menuTab" :key="item.id" :class="{'current': item.current}" @click="toggleMenu(item)">
          {{ item.txt }}
        </li>
      </ul>
      <svg-icon icon-name="menu" class-name="menu"></svg-icon>
      <!--表单 start-->
      <el-form :model="ruleForm" status-icon :rules="rules" ref="loginForm" class="login-form" size="medium">
        <el-form-item prop="username" class="item-from">
          <label for="username">邮箱</label>
          <el-input id="username" type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item prop="password" class="item-from">
          <label for="password">密码</label>
          <el-input id="password" type="password" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="passwords" class="item-from" v-show="model === 'register'">
          <label>重复密码</label>
          <el-input type="password" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="code" class="item-from">
          <label>验证码</label>
          <el-row :gutter="10">
            <el-col :span="15">
              <el-input v-model="ruleForm.code" minlength="6" maxlength="6"></el-input>
            </el-col>
            <el-col :span="9">
              <el-button type="success" class="block" @click="getSms()" ></el-button>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item>
          <el-button type="danger" @click="submitForm('loginForm')" class="login-btn block" :disabled="loginButtonStatus">{{ model === 'login' ? "登录" : "注册" }}</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { GetSms } from "@/api/login";
import { stripscript } from "@/utils/validate";
import { reactive, ref, onMounted } from "vue";
export default {
  name: "Home",
  setup(props, { refs }) {
    let checkAge = (rule, value, callback) => {
      let reg = /^[a-z0-9]{6}$/;
      ruleForm.verify = stripscript(value);
      value = stripscript(value);
      if (!value) {
        return callback(new Error("请输入验证码"));
      } else if (!reg.test(value)) {
        return callback(new Error("验证码不符合规则"));
      } else {
        callback();
      }
    };
    let usernameEmail = (rule, value, callback) => {
      //验证邮箱
      let reg = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
      if (value === "") {
        callback(new Error("请输入邮箱"));
      } else if (!reg.test(value)) {
        callback(new Error("用户名格式有误"));
      } else {
        callback();
      }
    };
    //验证密码
    let Password = (rule, value, callback) => {
      //过滤字符
      ruleForm.Password = stripscript(value);
      value = stripscript(value);
      //字母+数字
      let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/;
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (!reg.test(value)) {
        callback(new Error("密码必须包含数字和字母"));
      } else if (value !== "123456a") {
        callback(new Error("密码错误"));
      } else {
        callback();
      }
    };
    //验证重复密码
    let Passwords = (rule, value, callback) => {
      if (model == "login") {
        callback();
      }
      ruleForm.passwords = stripscript(value);
      value = stripscript(value);
      console.log(value);
      //字母+数字
      let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/;
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (!reg.test(value)) {
        callback(new Error("密码必须包含数字和字母"));
      } else if (value != this.ruleForm.pass) {
        callback(new Error("密码不一致"));
      } else {
        callback();
      }
    };
    const menuTab = reactive([
      { txt: "登录", current: false, type: "login" },
      { txt: "注册", current: true, type: "register" }
    ]);
    //模块值
    const model = ref("register");
    const ruleForm = reactive({
      username: "",
      password: "",
      passwords: "",
      verify: ""
    });
    const rules = reactive({
      username: { validator: usernameEmail, trigger: "blur" },
      password: { validator: Password, trigger: "blur" },
      passwords: { validator: Passwords, trigger: "blur" },
      verify: { validator: checkAge, trigger: "blur" }
    });
    //切换高光
    const toggleMneu = data => {
      menuTab.forEach(elem => {
        elem.current = false;
      });
      data.current = true;
      console.log(data.type);
      model.value = data.type;
    };
    /**
     * 获取验证码
     */
    const getSms = (() =>{
      GetSms(ruleForm.username)
    });
    //表单方法
    const submitForm = formName => {
      // alert("11");
      // axios
      //   .request({
      //     method: "post",
      //     url: "/user/12345",
      //     data: {
      //       firstName: "Fred",
      //       lastName: "Flintstone"
      //     }
      //   })
      //   .then(function(response) {
      //     console.log(response);
      //   })
      //   .catch(function(error) {
      //     console.log(error);
      //   });
      refs[formName].validate(valid => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    };
    onMounted(() => {
    });
    return {
      menuTab,
      model,
      toggleMneu,
      submitForm,
      ruleForm,
      rules,
      getSms
    };
  }
};
</script>
<style lang="scss" scoped>
#login {
  height: 100vh;
  background-color: #344a5f;
}
.login-wrap {
  width: 330px;
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
    background-color: rgba(0, 0, 0, 0.1);
  }
}
.login-form {
  margin-top: 29px;
  label {
    display: block;
    margin-bottom: 3px;
    font-size: 14px;
    color: #fff;
  }
  .item-from {
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
