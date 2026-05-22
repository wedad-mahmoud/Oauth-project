<template>
  <div class="container">
    <div class="animated-box"></div>
    
    <div class="card">
      <div class="logo-3d">🔐</div>
      <h1>OAuth <span class="gradient">Authentication</span></h1>
      <p class="subtitle">تسجيل دخول آمن بدون كلمة سر</p>

      <!-- بعد تسجيل الدخول -->
      <div v-if="user" class="profile">
        <img :src="user.photoURL" class="avatar">
        <h2>{{ user.displayName }}</h2>
        <p>{{ user.email }}</p>
        <button @click="logout" class="btn logout">🚪 تسجيل خروج</button>
      </div>

      <!-- قبل تسجيل الدخول -->
      <div v-else>
        <button @click="loginWithGoogle" class="btn google" :disabled="loading">
          {{ loading ? '⏳ جاري...' : '🚀 سجل دخول بـ Google' }}
        </button>
        <p class="hint">✅ يعمل على الكمبيوتر والهاتف</p>
      </div>
    </div>

    <footer class="footer">
      <p>OAuth 2.0 | Vue 3 + Firebase</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { initializeApp } from 'firebase/app'
import { 
  getAuth, 
  signInWithPopup, 
  GoogleAuthProvider, 
  onAuthStateChanged, 
  signOut 
} from 'firebase/auth'

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_APIKey,
  authDomain: import.meta.env.VITE_FIREBASE_AuthDomain,
  projectId: import.meta.env.VITE_FIREBASE_ProjectId,
  appId: import.meta.env.VITE_FIREBASE_AppId
}

const app = initializeApp(firebaseConfig)
const auth = getAuth(app)

const user = ref(null)
const loading = ref(false)

const loginWithGoogle = async () => {
  loading.value = true
  const provider = new GoogleAuthProvider()
  
  try {
    const result = await signInWithPopup(auth, provider)
    user.value = result.user
  } catch (err) {
    console.error(err)
    alert('خطأ: ' + err.message)
  } finally {
    loading.value = false
  }
}

const logout = async () => {
  await signOut(auth)
  user.value = null
}

onMounted(() => {
  onAuthStateChanged(auth, (currentUser) => {
    user.value = currentUser
  })
})
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  min-height: 100vh;
  background: linear-gradient(145deg, #0f0c29 0%, #1a1a3e 50%, #24243e 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;
  font-family: system-ui, 'Segoe UI', sans-serif;
  position: relative;
  overflow-x: hidden;
}

.animated-box {
  position: absolute;
  width: 300px;
  height: 300px;
  background: linear-gradient(135deg, #667eea, #764ba2, #f093fb);
  border-radius: 30px;
  filter: blur(40px);
  opacity: 0.5;
  animation: moveBox 8s infinite alternate ease-in-out;
  z-index: 0;
}

@keyframes moveBox {
  0% { transform: translate(-100px, -100px) rotate(0deg); border-radius: 30% 70% 70% 30% / 30% 30% 70% 70%; }
  25% { transform: translate(100px, -50px) rotate(90deg); border-radius: 58% 42% 75% 25% / 43% 58% 42% 57%; }
  50% { transform: translate(50px, 100px) rotate(180deg); border-radius: 46% 54% 33% 67% / 58% 40% 60% 42%; }
  75% { transform: translate(-80px, 50px) rotate(270deg); border-radius: 25% 75% 41% 59% / 65% 33% 67% 35%; }
  100% { transform: translate(80px, -80px) rotate(360deg); border-radius: 30% 70% 70% 30% / 30% 30% 70% 70%; }
}

.card {
  position: relative;
  width: 100%;
  max-width: 450px;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(20px);
  border-radius: 48px;
  border: 1px solid rgba(255, 255, 255, 0.15);
  padding: 48px 32px;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.3);
  z-index: 2;
  text-align: center;
}

@media (max-width: 550px) {
  .card {
    padding: 32px 24px;
  }
}

.logo-3d {
  font-size: 64px;
  margin-bottom: 20px;
  filter: drop-shadow(0 8px 20px rgba(102, 126, 234, 0.5));
  animation: bounce 2s infinite ease;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}

h1 {
  font-size: clamp(1.5rem, 5vw, 2rem);
  font-weight: 700;
  color: white;
  margin-bottom: 8px;
}

.gradient {
  background: linear-gradient(135deg, #a0a0ff, #764ba2);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.subtitle {
  color: rgba(255, 255, 255, 0.6);
  font-size: clamp(0.75rem, 3vw, 0.85rem);
  margin-bottom: 32px;
}

.btn {
  width: 100%;
  padding: 14px 20px;
  border: none;
  border-radius: 60px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: 0.2s;
}

.google {
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
}

.google:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
}

.google:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.logout {
  background: rgba(255, 255, 255, 0.15);
  color: white;
  margin-top: 20px;
}

.logout:hover {
  background: rgba(255, 255, 255, 0.25);
}

.hint {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.4);
  margin-top: 20px;
}

.profile {
  text-align: center;
}

.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
  margin-bottom: 16px;
  border: 3px solid #667eea;
  box-shadow: 0 0 25px rgba(102, 126, 234, 0.4);
}

@media (max-width: 550px) {
  .avatar {
    width: 80px;
    height: 80px;
  }
}

.profile h2 {
  color: white;
  font-size: clamp(1.2rem, 4vw, 1.5rem);
  margin-bottom: 6px;
}

.profile p {
  color: rgba(255, 255, 255, 0.6);
  font-size: 14px;
  word-break: break-all;
}

.footer {
  margin-top: 32px;
  text-align: center;
  font-size: 11px;
  color: rgba(255, 255, 255, 0.25);
  z-index: 2;
}
</style>