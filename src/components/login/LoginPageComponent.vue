<template>
  <div class="root-wrapper">
    <ToastNotification 
      :show="showToast" 
      :message="toastMessage" 
      @hide="showToast = false" 
    />

    <HeaderSection />

    <div class="common-footer-notice">
      <span class="footer-text">개인정보 처리방침</span>
      <span class="footer-separator">|</span>
      <span class="footer-copyright">Copyright ⓒ EULGPT. All Rights Reserved</span>
    </div>

    <div class="main-content">
      <div class="login-container">
        <div class="title-section">
          <span class="login-title">
            내가 찾던 정보, 이제 쉽게 만나요.
          </span>
        </div>

        <div class="divider-section">
          <div v-if="isDevMode" class="dev-login-box">
            <div class="dev-header">
              <span class="dev-tag">DEVELOPER ONLY</span>
              <p class="dev-info">ID: admin / PW: admin123</p>
            </div>
            <div class="dev-inputs">
              <input v-model="devId" type="text" placeholder="아이디" class="dev-field" />
              <input v-model="devPw" type="password" placeholder="비밀번호" class="dev-field" @keyup.enter="handleDevLogin" />
            </div>
            <button @click="handleDevLogin" class="dev-enter-btn">
              로그인 없이 바로 시작하기
            </button>
          </div>

          <div class="alternative-login">
            <button class="kakao-button" @click="handleKakaoLogin">
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M9 2C5.13 2 2 4.58 2 7.75c0 2.03 1.33 3.82 3.35 4.85l-.89 3.27c-.08.3.26.54.51.36L8.66 14c.11 0 .22.01.34.01 3.87 0 7-2.58 7-5.75S12.87 2 9 2z" fill="currentColor"/>
              </svg>
              <span class="kakao-text">카카오 계정으로 로그인</span>
            </button>
          </div>
          
          <div class="login-footer-link">
            <span class="question-text">이미 계정이 있으신가요?</span>
            <span class="login-link" @click="handleKakaoLogin" style="cursor: pointer;">로그인</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import ToastNotification from '../common/ToastNotification.vue'
import HeaderSection from '../main/HeaderSection.vue'
import { setAccessToken, setUserInfo } from '../../utils/auth'
import { getApiBaseUrl } from '@/utils/ports-config'

const router = useRouter()
const API_BASE_URL = getApiBaseUrl()

// 🚀 개발자 모드 변수 설정
const isDevMode = import.meta.env.DEV 
const devId = ref('')
const devPw = ref('')

const showToast = ref(false)
const toastMessage = ref('')

// 🚀 백엔드 없이 들어가는 개발자 로그인 로직
const handleDevLogin = () => {
  if (devId.value === 'admin' && devPw.value === 'admin123') {
    // Pro 기능을 사용할 수 있도록 토큰과 유저 정보 강제 주입
    setAccessToken('dev-pro-token-2026-tester');
    setUserInfo({
      name: '지윤(개발자)',
      email: 'admin@eulgpt.com',
      is_pro: true
    });
    
    showToastMessage('개발자 모드로 입장합니다.');
    
    // 0.5초 후 채팅 페이지로 이동
    setTimeout(() => {
      router.push('/chat');
    }, 500);
  } else {
    showToastMessage('개발자 계정 정보를 확인해주세요.');
  }
}

const showToastMessage = (message: string) => {
  toastMessage.value = message
  showToast.value = true
}

const handleKakaoLogin = () => {
  window.location.href = `${API_BASE_URL}/member/kakao/login`
}
</script>

<style scoped>
/* 🚀 개발자 로그인 디자인 (파란색 포인트) */
.dev-login-box {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 2px dashed #02478A; /* 지윤님이 선호하는 파란색 */
  border-radius: 12px;
  background-color: #f0f7ff;
  margin-bottom: 10px;
}
.dev-header { display: flex; flex-direction: column; gap: 4px; }
.dev-tag { background: #02478A; color: white; font-size: 10px; font-weight: 800; padding: 2px 6px; border-radius: 4px; width: fit-content; }
.dev-info { font-size: 11px; color: #475569; margin: 0; }
.dev-inputs { display: flex; gap: 8px; }
.dev-field { flex: 1; padding: 10px; border: 1px solid #cbd5e1; border-radius: 8px; font-size: 13px; outline: none; }
.dev-field:focus { border-color: #02478A; }
.dev-enter-btn { background: #02478A; color: white; border: none; padding: 12px; border-radius: 8px; font-weight: 700; cursor: pointer; transition: 0.2s; }
.dev-enter-btn:hover { background: #013566; }

/* 기존 스타일 유지 */
.root-wrapper { min-height: 100vh; background-color: var(--color-bg-primary); position: relative; font-family: 'Pretendard', sans-serif; }
.common-footer-notice { display: flex; justify-content: flex-start; align-items: center; gap: 5px; width: 277px; position: absolute; left: calc(50% - 138px); top: 710px; }
.footer-text, .footer-separator, .footer-copyright { color: var(--color-text-tertiary); font-size: 10px; font-weight: 500; }
.main-content { display: flex; justify-content: center; align-items: center; flex:1; width:100%; min-height: calc(100vh - 150px); }
.login-container { display: flex; flex-direction: column; align-items: center; gap: 30px; width: 408px; margin: 0 auto; }
.login-title { color: var(--color-text-primary); font-size: 24px; font-weight: 700; line-height: 140%; text-align: center; width: 100%; margin-bottom: -10px; }
.divider-section { display: flex; flex-direction: column; gap: 30px; align-self: stretch; }
.alternative-login { display: flex; flex-direction: column; align-items: center; gap: 3px; align-self: stretch; }
.kakao-button { display: flex; justify-content: center; align-items: center; gap: 8px; border-radius: 40px; align-self: stretch; height: 46px; background-color: rgb(254, 229, 2); border: none; cursor: pointer; }
.kakao-text { color: black; font-size: 16px; font-weight: 600; }
.login-footer-link { font-size: 14px; color: #9CA3AF; display: flex; gap: 8px; border-top: 2px solid #E5E7EB; padding-top: 10px; width: 100%; justify-content: center; }
.login-link { color: #02478A; font-weight: 600; cursor: pointer; }
</style>