<template>
  <div class="login-container">
    <el-card class="login-card">
      <template #header>
        <h2>系统登录</h2>
      </template>
      <el-form :model="loginForm" :rules="rules" ref="loginFormRef">
        <el-form-item prop="phone">
          <el-input
            v-model="loginForm.phone"
            placeholder="请输入手机号"
            :prefix-icon="Phone"
          />
        </el-form-item>
        <el-form-item prop="code">
          <div class="code-input">
            <el-input
              v-model="loginForm.code"
              placeholder="请输入验证码"
              :prefix-icon="Key"
            />
            <el-button
              type="primary"
              :disabled="isCountingDown"
              @click="sendCode"
            >
              {{ countDownText }}
            </el-button>
          </div>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="login-button" @click="handleLogin">
            登录
          </el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { Phone, Key } from '@element-plus/icons-vue'

const router = useRouter()
const loginFormRef = ref(null)
const isCountingDown = ref(false)
const countDown = ref(60)
const countDownText = ref('获取验证码')

const loginForm = reactive({
  phone: '',
  code: ''
})

const rules = {
  phone: [
    { required: true, message: '请输入手机号', trigger: 'blur' },
    { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号', trigger: 'blur' }
  ],
  code: [
    { required: true, message: '请输入验证码', trigger: 'blur' },
    { len: 6, message: '验证码长度应为6位', trigger: 'blur' }
  ]
}

const startCountDown = () => {
  isCountingDown.value = true
  countDown.value = 60
  const timer = setInterval(() => {
    countDown.value--
    countDownText.value = `${countDown.value}秒后重试`
    if (countDown.value <= 0) {
      clearInterval(timer)
      isCountingDown.value = false
      countDownText.value = '获取验证码'
    }
  }, 1000)
}

const sendCode = () => {
  loginFormRef.value.validateField('phone', (res) => {
    if (res) {
      // 这里应该调用发送验证码的API
      ElMessage.success('验证码已发送')
      startCountDown()
    }
  })
}

const handleLogin = () => {
  loginFormRef.value.validate((valid) => {
    if (valid) {
      // 这里应该调用登录API
      localStorage.setItem('isLoggedIn', 'true')
      ElMessage.success('登录成功')
      router.push('/')
    }
  })
}
</script>

<style scoped>
.login-container {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f5f5f5;
}

.login-card {
  width: 400px;
}

.login-card :deep(.el-card__header) {
  text-align: center;
}

.code-input {
  display: flex;
  gap: 10px;
}

.code-input .el-button {
  width: 120px;
}

.login-button {
  width: 100%;
}
</style> 