<template>
  <div class="login">
    <div class="login-content">
      <div class="title">中医药互联网+问卷调查系统</div>
      <el-form ref="form" :model="form" :rules="loginRules">
        <el-form-item prop="username">
          <el-input
            v-model="form.username"
            placeholder="用户"
          >
            <i slot="prefix" class="el-input__icon el-icon-s-custom" />
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="form.password" type="password" placeholder="密码">
            <i slot="prefix" class="el-input__icon el-icon-lock" />
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="success" class="login-btn" :loading="loading" @click="login">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
const validateUsername = (rule, value, callback) => {
  if (!value) {
    callback(new Error('请输入用户名！'))
  } else {
    callback()
  }
}
const validatePassword = (rule, value, callback) => {
  if (value.length < 6) {
    callback(new Error('密码长度不能少于6位！'))
  } else {
    callback()
  }
}
export default {
  name: 'Login',
  components: { },
  data() {
    return {
      form: {
        username: '',
        password: ''
      },
      loginRules: {
        username: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      },
      loading: false
    }
  },
  created() {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {},
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    login() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          this.loading = true
          this.$store.dispatch('user/login', this.form).then(res => {
            this.loading = false
            if (res.rolename === 'admin user') {
              // 管理员
              this.$router.push({ path: '/userGl' })
            } else {
              // 不是管理员,并且机构信息未提交跳转到填写信息页面，已填写跳转到问卷

              if (res.status === '0') {
                this.$router.push({ path: '/userBasicInfor' })
              } else {
                this.$router.push({ path: '/answerSheet' })
              }
            }
          }).catch(err => {
            console.log(err)
            this.loading = false
          })
        }
      })
    }
  }
}
</script>
<style lang="scss" scoped>
.login {
  display: flex;
  width: 100%;
  height: 100vh;
  justify-content: center;
  .login-content {
    width: 300px;
    margin-top: 15%;
    .title {
      text-align: center;
      font-size: 18px;
      font-weight: 600;
      margin-bottom: 100px;
    }
    .login-btn {
      width: 100%;
    }
  }
}
</style>
