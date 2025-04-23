<template>
  <div class="about">
    <div class="container">
      <h1>OurPick 소개</h1>
      <div class="content">
        <p>OurPick은 원하는 그룹과 장소와 일정을 공유하는 서비스에요.</p>
        <p>다양한 서비스를 제공해요</p>
        <ul>
          <li>장소 공유</li>
          <li>일정 공유</li>
          <li>채팅 기능</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'About'
}
</script>

<style scoped>
.about {
  padding: 4rem 0;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 0 1rem;
}

h1 {
  text-align: center;
  margin-bottom: 2rem;
  color: #2c3e50;
}

.content {
  background: white;
  padding: 2rem;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 1rem 0;
  padding-left: 1.5rem;
  position: relative;
}

li:before {
  content: "•";
  color: #42b983;
  position: absolute;
  left: 0;
}
</style> 