<template>
  <div class="home" ref="home">
    <div class="background-animation"></div>
    <div class="content" ref="content">
      <h1 class="title" ref="title">OurPick</h1>
      <div class="section" v-for="(section, index) in sections" :key="index" :ref="'section'+index">
        <p class="text">{{ section.text }}</p>
      </div>
      <button class="start-button" ref="startButton" @click="startApp" @mousemove="handleMouseMove">
        시작하기
        <div class="button-background"></div>
      </button>
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
      ]
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
    startApp() {
      gsap.to(this.$refs.content, {
        scale: 0.8,
        opacity: 0,
        duration: 0.5,
        onComplete: () => {
          console.log('Animation complete - start application')
        },
      })
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
</style> 