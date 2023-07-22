<template>
  <div class="splashscreen">
    <img
      src="@/assets/logo.png"
      style="transform: scaleX(-1)"
      alt="Logo"
      title="Logo"
      height="250"
      width="250"
    />
    <h1><span>Care</span>Fines</h1>
    <div class="loading">
      <div class="square-holder">
        <div class="square"></div>
      </div>
    </div>
    <div class="copyright">
      <p>© CareFines 2023</p>
    </div>
  </div>
</template>

<style scoped>
.splashscreen {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  height: 100vh;
  background: #121f3d;
}
h1 {
  color: var(--brand-yellow);
  font-family: LilitaOne, serif;
  letter-spacing: 0.2rem;
}
h1 span {
  color: var(--brand-gold);
}
.loading .square {
  animation: loading 1.5s cubic-bezier(0.17, 0.37, 0.43, 0.67) infinite;
}
.square {
  width: 12px;
  height: 12px;
  border-radius: 4px;
  background-color: #00b7f9;
}
@keyframes loading {
  0% {
    transform: translate(-70px, 0) rotate(0deg);
  }
  50% {
    transform: translate(70px, 0) rotate(360deg);
  }
  100% {
    transform: translate(-70px, 0) rotate(0deg);
  }
}
.copyright {
  bottom: 0;
  position: absolute;
  right: 10px;
  color: white;
  font-size: 10px;
  opacity: 0.7;
}
</style>
<script setup></script>
