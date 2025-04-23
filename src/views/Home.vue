<template>
  <div class="home" ref="home">
    <div class="background-animation"></div>
    <div class="content" ref="content">
      <h1 class="title" ref="title">OurPick</h1>
      <div class="section" v-for="(section, index) in sections" :key="index" :ref="'section'+index">
        <p class="text">{{ section.text }}</p>
      </div>
      <button class="start-button" ref="startButton" @click="showLoginModal" @mousemove="handleMouseMove">
        시작하기
        <div class="button-background"></div>
      </button>
    </div>

    <!-- 로그인 모달 -->
    <div class="modal-overlay" v-if="isLoginModalVisible" @click="hideLoginModal">
      <div class="login-modal" @click.stop>
        <h2>로그인</h2>
        <form @submit.prevent="handleLogin" class="login-form">
          <div class="form-group">
            <input type="email" v-model="email" placeholder="이메일" required>
          </div>
          <div class="form-group">
            <input type="password" v-model="password" placeholder="비밀번호" required>
          </div>
          <button type="submit" class="login-button">로그인</button>
        </form>
        <div class="login-footer">
          <span>계정이 없으신가요? </span>
          <a href="#" @click.prevent="toggleSignup">회원가입</a>
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
        { text: '당신의 최고의 선택을 위한' },
        { text: '최적의 플랫폼' },
        { text: '지금 시작해보세요' }
      ],
      isLoginModalVisible: false,
      email: '',
      password: ''
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
    showLoginModal() {
      this.isLoginModalVisible = true
    },
    hideLoginModal() {
      this.isLoginModalVisible = false
    },
    handleLogin() {
      // TODO: 로그인 로직 구현
      console.log('Login attempt:', this.email)
    },
    toggleSignup() {
      // TODO: 회원가입 전환 로직 구현
      console.log('Toggle signup')
    },
    startApp() {
      this.showLoginModal()
    }
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

.login-modal {
  background: white;
  padding: 2rem;
  border-radius: 20px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  animation: slideUp 0.3s ease;
}

.login-modal h2 {
  color: #35495e;
  margin-bottom: 1.5rem;
  font-size: 1.8rem;
  text-align: center;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.form-group {
  position: relative;
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

.login-button {
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

.login-button:hover {
  background: #8ed3b9;
  transform: translateY(-2px);
}

.login-footer {
  margin-top: 1.5rem;
  text-align: center;
  color: #666;
}

.login-footer a {
  color: #a8e6cf;
  text-decoration: none;
  font-weight: bold;
  margin-left: 0.5rem;
}

.login-footer a:hover {
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
</style> 