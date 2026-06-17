<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-blue-400 via-teal-400 to-green-400 relative overflow-hidden">
    <!-- Decorative background elements -->
    <div class="absolute top-0 left-0 w-full h-full overflow-hidden z-0 pointer-events-none">
      <div class="absolute -top-[10%] -left-[10%] w-96 h-96 bg-white opacity-20 rounded-full blur-3xl"></div>
      <div class="absolute top-[20%] right-[10%] w-[30rem] h-[30rem] bg-teal-300 opacity-30 rounded-full blur-3xl"></div>
      <div class="absolute -bottom-[10%] left-[20%] w-[40rem] h-[40rem] bg-blue-300 opacity-20 rounded-full blur-3xl"></div>
    </div>

    <div class="bg-white/90 backdrop-blur-xl p-10 rounded-3xl shadow-2xl w-full max-w-md z-10 border border-white/50 transition-all duration-300 hover:shadow-teal-500/20">
      <div class="text-center mb-8">
        <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-teal-500 text-white mb-4 shadow-lg shadow-teal-500/30">
          <el-icon :size="32"><School /></el-icon>
        </div>
        <h2 class="text-3xl font-extrabold text-gray-900 tracking-tight">e-Sekolah Pintar</h2>
        <p class="text-gray-500 mt-2 text-sm font-medium">Portal Belajar Mandiri Sekolah</p>
      </div>
      
      <el-form :model="form" @submit.prevent="handleLogin" label-position="top">
        <el-form-item>
          <el-input 
            v-model="form.email" 
            placeholder="Email atau NIS / NIP" 
            size="large"
            :prefix-icon="User"
            class="!rounded-xl"
          />
        </el-form-item>
        <el-form-item class="mt-6">
          <el-input 
            v-model="form.password" 
            type="password" 
            placeholder="Kata Sandi" 
            size="large"
            show-password
            :prefix-icon="Lock"
            class="!rounded-xl"
          />
        </el-form-item>
        
        <div class="flex items-center justify-between mt-4 mb-8">
          <el-checkbox v-model="form.remember" label="Ingat saya" class="!text-gray-600" />
          <a href="#" class="text-sm text-teal-600 hover:text-teal-800 font-semibold transition-colors">Lupa sandi?</a>
        </div>

        <el-button 
          native-type="submit" 
          type="primary" 
          size="large" 
          class="w-full !rounded-xl !h-12 !text-base !font-bold !bg-teal-500 hover:!bg-teal-600 !border-none shadow-lg shadow-teal-500/30 transition-all duration-200"
          :loading="loading"
        >
          Masuk Sekarang
        </el-button>
      </el-form>
      
      <div class="mt-8 text-center">
        <p class="text-xs text-gray-400">&copy; {{ new Date().getFullYear() }} e-Sekolah Pintar. All rights reserved.</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../stores/auth'
import { School, User, Lock } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

const router = useRouter()
const authStore = useAuthStore()

const loading = ref(false)
const form = reactive({
  email: '',
  password: '',
  remember: false
})

const handleLogin = async () => {
  if (!form.email || !form.password) {
    ElMessage.warning('Email/NIS dan password wajib diisi')
    return
  }
  
  loading.value = true
  try {
    await authStore.login(form.email, form.password)
    ElMessage.success('Login berhasil')
    router.push('/')
  } catch (error) {
    ElMessage.error(error.response?.data?.message || 'Login gagal. Periksa kembali kredensial Anda.')
  } finally {
    loading.value = false
  }
}
</script>

<style>
/* Custom overrides for Element Plus inputs to make them more modern */
.el-input__wrapper {
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05) !important;
  border-radius: 0.75rem !important;
  padding: 0.5rem 1rem !important;
  transition: all 0.2s ease !important;
}
.el-input__wrapper.is-focus {
  box-shadow: 0 0 0 2px rgba(20, 184, 166, 0.2) !important; /* teal-500 */
}
</style>
