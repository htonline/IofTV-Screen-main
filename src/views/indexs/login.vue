<template>
  <!-- 背景设置 -->
  <div class="wrapper">
    <!-- 白色登录窗口设置 350X300 -->
    <div
        style="margin: 200px auto; background-color: #fff; width: 350px; height: 300px; padding: 20px; border-radius: 10px">
      <div style="margin: 20px 0; text-align:center; font-size: 24px;"><b>登 录</b></div>
      <el-form :model="user" :rules="rules" ref="userForm">

        <el-form-item prop="username">
          <el-input size="medium" style="margin: 10px 0" prefix-icon="el-icon-user" v-model="user.username"
                    placeholder="请输入用户名"></el-input>
        </el-form-item>

        <el-form-item prop="password">
          <el-input size="medium" style="margin: 10px 0" prefix-icon="el-icon-lock" v-model="user.password"
                    placeholder="请输入密码" show-password></el-input>
        </el-form-item>

        <div style="margin: 10px 0; text-align: right">
          <el-button type="primary" size="small" autocomplete="off" @click="login">登录</el-button>
          <el-button type="warning" size="small" autocomplete="off" @click="$router.push('/register')">注册</el-button>
        </div>
      </el-form>

    </div>

  </div>

</template>

<script>

import { loginRequest } from "api/modules";
export default {
  name: "Login",
  data() {
    return {
      // 定义一个user对象，给用户名绑定它的username, 密码绑定它的password
      user: {
        username: "",
        password: "",
      },

      rules: {
        // 这个username适合prop的名字对应的
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 3, max: 20, message: '长度在 3 到 20 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    login() {
      // 表单校验是否合法; 合法则发送请求
      this.$refs['userForm'].validate((valid) => {
        if (valid) {
          loginRequest(this.user).then((res) => {
            console.log("login",res)
            // 如果登录成功
            if (res) {
              this.$router.push("/index")
            } else {
              this.$message.error("用户名或密码错误")
            }
          })
        } else {
          return false;
        }
      })

    }
  }
}
</script>

<style>
.wrapper {
  /* 100vh: 表示100%的高度 */
  height: 100vh;
  /* 渐变背景色 */
  /* to bottom right：从左上角 往 右下角去渐变 */
  background-image: linear-gradient(to bottom right, #FC4668, #3F5EFB);
  /* 超出部分，隐藏掉 */
  overflow: hidden;
}
</style>
