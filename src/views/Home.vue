<template>
  <div class="home" ref="home">
    <div class="background-animation"></div>
    <div class="content" ref="content">
      <h1 class="title" ref="title">OurPick</h1>
      <div class="section" v-for="(section, index) in sections" :key="index" :ref="'section'+index">
        <p class="text">{{ section.text }}</p>
      </div>
      <button class="start-button" ref="startButton" @click="showModal" @mousemove="handleMouseMove">
        시작하기
        <div class="button-background"></div>
      </button>
    </div>

    <!-- 로그인/회원가입 모달 -->
    <div class="modal-overlay" v-if="isModalVisible" @click="hideModal">
      <div class="modal-content" @click.stop>
        <h2 v-if="isLoginView">로그인</h2>
        <h2 v-else-if="!isEmailVerificationView">회원가입</h2>
        <h2 v-else>이메일 인증</h2>

        <!-- 로그인 폼 -->
        <form v-if="isLoginView" @submit.prevent="handleLogin" class="form">
          <div class="form-group">
            <input type="email" v-model="loginEmail" placeholder="이메일" required>
          </div>
          <div class="form-group">
            <input type="password" v-model="loginPassword" placeholder="비밀번호" required>
          </div>
          <button type="submit" class="submit-button">로그인</button>
          <div class="form-footer">
            <span>계정이 없으신가요? </span>
            <a href="#" @click.prevent="toggleView">회원가입</a>
          </div>
        </form>

        <!-- 회원가입 폼 -->
        <form v-else-if="!isEmailVerificationView" @submit.prevent="handleSignup" class="form" novalidate>
          <div class="form-group">
            <input type="email" v-model="signupEmail" placeholder="이메일" required @blur="validateEmail" @input="validateEmail">
            <span class="error-message" v-if="emailError">{{ emailError }}</span>
          </div>
          <div class="form-group">
            <input type="password" v-model="signupPassword" placeholder="비밀번호" required @blur="validatePassword" @input="validatePassword">
            <span class="error-message" v-if="passwordError">{{ passwordError }}</span>
          </div>
          <div class="form-group">
            <input type="password" v-model="confirmPassword" placeholder="비밀번호 확인" required @blur="validateConfirmPassword" @input="validateConfirmPassword">
            <span class="error-message" v-if="confirmPasswordError">{{ confirmPasswordError }}</span>
          </div>
          <button type="submit" class="submit-button" :disabled="!isFormValid">회원가입</button>
          <div class="form-footer">
            <span>이미 계정이 있으신가요? </span>
            <a href="#" @click.prevent="toggleView">로그인</a>
          </div>
        </form>

        <!-- 이메일 인증 폼 -->
        <div v-else class="form">
          <p class="verification-message">{{ signupEmail }}으로<br>인증 코드를 전송했습니다.</p>
          <div class="verification-inputs">
            <template v-for="n in 6" :key="n">
              <input
                type="text"
                v-model="verificationDigits[n-1]"
                maxlength="1"
                class="verification-digit"
                :ref="'digit' + (n-1)"
                @input="handleDigitInput($event, n-1)"
                @keydown="handleKeydown($event, n-1)"
                @focus="$event.target.select()"
                pattern="[0-9]*"
                inputmode="numeric"
                placeholder="-"
              >
              <span v-if="n < 6" class="verification-digit-spacer"></span>
            </template>
          </div>
          <span class="error-message" v-if="verificationError">{{ verificationError }}</span>
          <div class="verification-timer" v-if="remainingTime > 0">
            {{ formatTime(remainingTime) }}
          </div>
          <button @click="verifyEmail" class="submit-button" :disabled="!isVerificationComplete">
            인증하기
          </button>
          <button @click="resendVerificationCode" class="resend-button" :disabled="remainingTime > 0">
            인증코드 재전송
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

export default {
  name: 'Home',
  data() {
    return {
      sections: [
        { text: '우리들만의 공유를 위한' },
        { text: '최적의 서비스' },
        { text: '지금 시작해보세요' }
      ],
      isModalVisible: false,
      isLoginView: true, // true: 로그인 뷰, false: 회원가입 뷰
      loginEmail: '',
      loginPassword: '',
      signupEmail: '',
      signupPassword: '',
      confirmPassword: '',
      emailError: '', // 이메일 에러 메시지
      passwordError: '', // 비밀번호 에러 메시지
      confirmPasswordError: '', // 비밀번호 확인 에러 메시지
      isEmailVerificationView: false,
      verificationDigits: ['', '', '', '', '', ''],
      verificationError: '',
      remainingTime: 0,
      timerInterval: null
    }
  },
  computed: {
    isFormValid() {
      return !this.emailError && 
             !this.passwordError && 
             !this.confirmPasswordError && 
             this.signupEmail && 
             this.signupPassword && 
             this.confirmPassword
    },
    isVerificationComplete() {
      return this.verificationDigits.every(digit => digit.length === 1 && /^\d$/.test(digit))
    },
    verificationCode() {
      return this.verificationDigits.join('')
    }
  },
  mounted() {
    this.initAnimations()
  },
  methods: {
    initAnimations() {
      const tl = gsap.timeline()

      // Title Animation
      tl.from(this.$refs.title, {
        y: 100,
        opacity: 0,
        duration: 2,
        ease: 'power4.out',
      })

      // Section Text Animations (Sequenced after title)
      this.sections.forEach((_, index) => {
        tl.from(this.$refs['section' + index], {
          x: -100,
          opacity: 0,
          duration: 1.5,
          ease: 'power2.out',
        }, '-=1.2')
      })

      // 모든 섹션 텍스트 페이드아웃 및 축소 (타이틀 제외)
      tl.to(this.sections.map((_, index) => this.$refs['section' + index]), {
        opacity: 0,
        height: 0,
        margin: 0,
        padding: 0,
        duration: 1,
        ease: 'power2.in',
      })

      // 버튼 애니메이션 (텍스트 축소 완료 직후 시작, 속도 유지)
      tl.from(this.$refs.startButton, {
        scale: 0,
        opacity: 0,
        duration: 1.0,
        ease: 'elastic.out(1, 0.5)',
        clearProps: "opacity,transform"
      }, "<1") // '<1' : 이전 애니메이션 시작 후 1초 뒤 = 이전 애니메이션 종료 시점
    },
    handleMouseMove(e) {
      const button = e.currentTarget
      const rect = button.getBoundingClientRect()
      const x = e.clientX - rect.left
      const y = e.clientY - rect.top

      gsap.to(button, {
        '--x': `${x}px`,
        '--y': `${y}px`,
        duration: 0.3,
        ease: 'power2.out',
      })
    },
    showModal() {
      this.isModalVisible = true
      this.isLoginView = true // 모달 열 때 항상 로그인 뷰로 시작
      // Reset form fields and errors when opening
      this.loginEmail = ''
      this.loginPassword = ''
      this.signupEmail = ''
      this.signupPassword = ''
      this.confirmPassword = ''
      this.clearErrors()
    },
    hideModal() {
      this.isModalVisible = false
      this.isEmailVerificationView = false
      this.clearVerificationInputs()
      this.verificationError = ''
      clearInterval(this.timerInterval)
      this.clearErrors()
    },
    toggleView() {
      if (this.isEmailVerificationView) {
        this.isEmailVerificationView = false
      } else {
        this.isLoginView = !this.isLoginView
      }
      this.clearErrors()
    },
    handleLogin() {
      // TODO: 로그인 로직 구현
      console.log('Login attempt:', this.loginEmail)
    },
    handleSignup() {
      // 각 필드 유효성 검사 실행
      const isEmailValid = this.validateEmail()
      const isPasswordValid = this.validatePassword()
      const isConfirmPasswordValid = this.validateConfirmPassword()

      if (isEmailValid && isPasswordValid && isConfirmPasswordValid) {
        try {
          // TODO: 실제 이메일 인증 코드 발송 API 호출
          console.log('Verification code sent to:', this.signupEmail)
          
          // 이메일 인증 뷰로 전환
          this.isEmailVerificationView = true
          this.startVerificationTimer()
        } catch (error) {
          console.error('Failed to send verification code:', error)
        }
      }
    },
    clearErrors() {
      this.emailError = ''
      this.passwordError = ''
      this.confirmPasswordError = ''
    },
    validateEmail() {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      if (!this.signupEmail) {
        this.emailError = '이메일을 입력해주세요.'
      } else if (!emailRegex.test(this.signupEmail)) {
        this.emailError = '올바른 이메일 형식이 아닙니다.'
      } else {
        this.emailError = ''
      }
      return !this.emailError
    },
    validatePassword() {
      // 8자 이상, 대문자, 소문자, 숫자, 특수문자(!@#$%^&*) 포함
      const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[!@#$%^&*])[A-Za-z\d!@#$%^&*]{8,}$/
      if (!this.signupPassword) {
        this.passwordError = '비밀번호를 입력해주세요.'
      } else if (!passwordRegex.test(this.signupPassword)) {
        this.passwordError = '8자 이상, 대/소문자, 숫자, 특수기호를 포함해야 합니다.'
      } else {
        this.passwordError = ''
      }
      // 비밀번호 확인 필드도 함께 검증 (비밀번호 변경 시)
      if (this.confirmPassword) {
        this.validateConfirmPassword()
      }
      return !this.passwordError
    },
    validateConfirmPassword() {
      if (!this.confirmPassword) {
        this.confirmPasswordError = '비밀번호 확인을 입력해주세요.'
      } else if (this.signupPassword !== this.confirmPassword) {
        this.confirmPasswordError = '비밀번호가 일치하지 않습니다.'
      } else {
        this.confirmPasswordError = ''
      }
      return !this.confirmPasswordError
    },
    startVerificationTimer() {
      this.remainingTime = 180 // 3분
      clearInterval(this.timerInterval)
      this.timerInterval = setInterval(() => {
        if (this.remainingTime > 0) {
          this.remainingTime--
        } else {
          clearInterval(this.timerInterval)
        }
      }, 1000)
    },
    formatTime(seconds) {
      const minutes = Math.floor(seconds / 60)
      const remainingSeconds = seconds % 60
      return `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`
    },
    async verifyEmail() {
      try {
        // TODO: 실제 이메일 인증 코드 확인 API 호출
        if (this.verificationCode === '123456') { // 임시 검증 로직
          console.log('Email verified successfully')
          alert('이메일 인증이 완료되었습니다.')
          this.hideModal()
          // TODO: 회원가입 완료 처리
        } else {
          this.verificationError = '잘못된 인증 코드입니다.'
        }
      } catch (error) {
        console.error('Failed to verify email:', error)
        this.verificationError = '인증 처리 중 오류가 발생했습니다.'
      }
    },
    async resendVerificationCode() {
      try {
        // TODO: 실제 인증 코드 재전송 API 호출
        console.log('Resending verification code to:', this.signupEmail)
        this.startVerificationTimer()
        this.verificationError = ''
      } catch (error) {
        console.error('Failed to resend verification code:', error)
        this.verificationError = '인증 코드 재전송에 실패했습니다.'
      }
    },
    startApp() {
      this.showModal()
    },
    handleDigitInput(event, index) {
      const value = event.target.value
      // 숫자만 허용
      if (!/^\d*$/.test(value)) {
        this.verificationDigits[index] = ''
        return
      }
      
      // 마지막 입력된 문자만 유지
      this.verificationDigits[index] = value.slice(-1)
      
      // 다음 입력 칸으로 이동
      if (value && index < 5) {
        this.$nextTick(() => {
          this.$refs['digit' + (index + 1)][0].focus()
        })
      }
    },
    handleKeydown(event, index) {
      // Backspace 처리
      if (event.key === 'Backspace' && !this.verificationDigits[index] && index > 0) {
        this.$nextTick(() => {
          this.$refs['digit' + (index - 1)][0].focus()
        })
      }
    },
    clearVerificationInputs() {
      this.verificationDigits = ['', '', '', '', '', '']
    }
  },
  beforeUnmount() {
    clearInterval(this.timerInterval)
  }
}
</script>

<style scoped>
.home {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f8f9fa; /* Match App background */
  color: #333; /* Darker text */
  overflow: hidden;
  position: relative;
}

.background-animation {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  /* Pastel gradient */
  background: linear-gradient(-45deg, #ffdde1, #ee9ca7, #a1c4fd, #c2e9fb);
  background-size: 400% 400%;
  animation: gradientBG 20s ease infinite; /* Slower animation */
  opacity: 0.6; /* Slightly less opaque */
  filter: blur(70px); /* More blur */
}

.content {
  position: relative;
  z-index: 1;
  text-align: center;
  padding: 1rem; /* 패딩 감소 (기존 2rem) */
}

.title {
  font-size: 4rem;
  margin-bottom: 0.5rem; /* 하단 마진 대폭 감소 (기존 1rem) */
  font-weight: bold;
  color: #35495e;
}

.section {
  margin: 0.8rem 0; /* 상하 마진 추가 감소 (기존 1rem) */
}

.text {
  font-size: 1.6rem;
  line-height: 1.5; /* 라인 높이 추가 줄임 (기존 1.6) */
  opacity: 0.8;
  color: #555;
}

.start-button {
  position: relative;
  padding: 1rem 2.5rem;
  font-size: 1.2rem;
  font-weight: bold;
  color: #ffffff;
  background: #a8e6cf; /* 파스텔톤 녹색 */
  border: none;
  border-radius: 30px;
  cursor: pointer;
  overflow: hidden;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  z-index: 2;
}

.start-button::before {
  content: '';
  position: absolute;
  top: var(--y, 50%);
  left: var(--x, 50%);
  transform: translate(-50%, -50%);
  width: 0;
  height: 0;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  transition: width 0.5s, height 0.5s;
  z-index: -1;
}

.start-button:hover {
  background: #8ed3b9; /* 호버 시 약간 더 진한 녹색 */
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
}

.start-button:hover::before {
  width: 300px;
  height: 300px;
}

@keyframes gradientBG {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

/* 반응형 스타일 */
@media (max-width: 768px) {
  .title {
    font-size: 3rem;
  }

  .text {
    font-size: 1.2rem;
  }
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
}

.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 20px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  animation: slideUp 0.3s ease;
}

.modal-content h2 {
  color: #35495e;
  margin-bottom: 1.5rem;
  font-size: 1.8rem;
  text-align: center;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.form-group {
  position: relative;
  margin-bottom: 1rem; /* 에러 메시지 공간 확보 */
}

.form-group input {
  width: 100%;
  padding: 1rem;
  border: 2px solid #eee;
  border-radius: 10px;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-group input:focus {
  border-color: #a8e6cf;
  outline: none;
  box-shadow: 0 0 0 3px rgba(168, 230, 207, 0.2);
}

.submit-button {
  background: #a8e6cf;
  color: white;
  padding: 1rem;
  border: none;
  border-radius: 10px;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1rem;
}

.submit-button:hover {
  background: #8ed3b9;
  transform: translateY(-2px);
}

.form-footer {
  margin-top: 1.5rem;
  text-align: center;
  color: #666;
}

.form-footer a {
  color: #a8e6cf;
  text-decoration: none;
  font-weight: bold;
  margin-left: 0.5rem;
}

.form-footer a:hover {
  color: #8ed3b9;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.error-message {
  color: #e74c3c; /* 빨간색 계열 */
  font-size: 0.8rem;
  position: absolute;
  left: 0;
  bottom: -1.2rem; /* 입력창 바로 아래 */
}

.verification-inputs {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 2rem 0;
  gap: 0.5rem;
}

.verification-digit {
  width: 40px;
  height: 50px;
  text-align: center;
  font-size: 1.5rem;
  border: 2px solid #ddd;
  border-radius: 8px;
  background: white;
  transition: all 0.3s ease;
}

.verification-digit:focus {
  border-color: #a8e6cf;
  outline: none;
  box-shadow: 0 0 0 3px rgba(168, 230, 207, 0.2);
}

.verification-digit-spacer {
  width: 8px;
}

.verification-message {
  text-align: center;
  color: #666;
  margin: 1.5rem 0;
  line-height: 1.6;
}

.verification-timer {
  text-align: center;
  color: #e74c3c;
  font-weight: bold;
  margin: 1rem 0;
}

.resend-button {
  background: transparent;
  color: #666;
  border: 1px solid #ddd;
  padding: 0.8rem;
  border-radius: 10px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1rem;
  width: 100%;
}

.resend-button:hover:not(:disabled) {
  background: #f8f9fa;
  border-color: #a8e6cf;
  color: #a8e6cf;
}

.submit-button:disabled,
.resend-button:disabled {
  background: #ccc;
  cursor: not-allowed;
  transform: none;
}

.resend-button:disabled {
  border-color: #ccc;
  color: #999;
}
</style> 