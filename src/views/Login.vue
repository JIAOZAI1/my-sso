<template>
  <div class="login-wrap">
    <el-card class="login-card">
      <div class="logo-container">
        <img src="/src/assets/logo.svg" alt="Logo" class="logo">
      </div>
      <h2 class="title">单点登录（SSO）</h2>
      <el-form :model="form" ref="formRef" class="login-form" @submit.native.prevent>
        <el-form-item>
          <el-input v-model="form.username" placeholder="账号" clearable />
        </el-form-item>
        <el-form-item>
          <el-input v-model="form.password" placeholder="密码" show-password />
        </el-form-item>
        <el-form-item>
          <el-checkbox v-model="remember">记住密码</el-checkbox>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" :loading="loading" @click="onSubmit" style="width:100%;">登录</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { ElMessage } from 'element-plus'

export default {
  name: 'Login',
  setup() {
    const formRef = ref(null)
    const form = ref({ username: '', password: '' })
    const remember = ref(false)
    const loading = ref(false)

    const params = new URLSearchParams(window.location.search)
    const redirectUrl = params.get('redirect') || ''

    onMounted(() => {
      const saved = localStorage.getItem('sso_remember')
      if (saved === 'true') {
        const u = localStorage.getItem('sso_username') || ''
        const p = localStorage.getItem('sso_password') || ''
        form.value.username = u
        form.value.password = p
        remember.value = true
      }
    })

    const onSubmit = async () => {
      loading.value = true
      try {
        const res = await axios.post('/api/login', {
          username: form.value.username,
          password: form.value.password
        })
        const token = res.data && res.data.token
        if (token) {
          // 保存 token（示例）
          localStorage.setItem('sso_token', token)

          if (remember.value) {
            localStorage.setItem('sso_remember', 'true')
            localStorage.setItem('sso_username', form.value.username)
            localStorage.setItem('sso_password', form.value.password)
          } else {
            localStorage.removeItem('sso_remember')
            localStorage.removeItem('sso_username')
            localStorage.removeItem('sso_password')
          }

          // 跳转回原始地址并携带 token
          if (redirectUrl) {
            const sep = redirectUrl.includes('?') ? '&' : '?'
            window.location.href = redirectUrl + sep + 'token=' + encodeURIComponent(token)
          } else {
            ElMessage.success('登录成功，未检测到 redirect，登录将在本页面保留 token。')
          }
        } else {
          ElMessage.error('登录失败：未返回 token')
        }
      } catch (err) {
        ElMessage.error('登录失败：' + (err.response?.data?.message || err.message))
      } finally {
        loading.value = false
      }
    }

    return { formRef, form, remember, loading, onSubmit }
  }
}
</script>

<style scoped>
.login-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  padding: 20px;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}
.login-card {
  width: 420px;
  max-width: 100%;
  border-radius: 10px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
  padding: 20px;
}
.logo-container {
  text-align: center;
  margin-bottom: 20px;
}
.logo {
  width: 80px;
  height: 80px;
}
.title {
  text-align: center;
  margin-bottom: 30px;
  font-size: 24px;
  font-weight: 600;
  color: #333;
}
.login-form {
  margin-top: 20px;
}
.el-form-item {
  margin-bottom: 25px;
}
.el-input__inner {
  height: 48px;
  line-height: 48px;
  border-radius: 8px;
}
.el-button {
  height: 48px;
  border-radius: 8px;
  font-size: 16px;
}
@media (max-width: 480px) {
  .login-card {
    width: 100%;
    padding: 15px;
  }
  .title {
    font-size: 20px;
    margin-bottom: 20px;
  }
  .el-form-item {
    margin-bottom: 20px;
  }
}
</style>
