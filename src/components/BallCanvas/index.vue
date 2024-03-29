<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const canvas = ref();
const context = ref();
const balls = [];

onMounted(() => {
  context.value = canvas.value?.getContext('2d') || undefined;

  for (let i = 0; i < 100; i++) {
    balls.push({
      x: Math.random() * canvas.value.width,
      y: Math.random() * canvas.value.height,
      targetX: 0,
      targetY: 0,
      speed: 1.5,
      radius: 15,
      number: 0
    });
  }

  initialCanvas();
  animate();
});

onUnmounted(() => {
  cancelAnimationFrame(animationFrameId);

  if (context.value) {
    context.value.clearRect(0, 0, 0, 0);
  }
});

let animationFrameId;

function initialCanvas() {
  const devicePixelRatio = window.devicePixelRatio || 1;
  const main = document.querySelector('main');
  const mainPosition = main.getBoundingClientRect();
  const randomTargetX = Math.floor(Math.random() * (mainPosition.width - 30));
  const randomTargetY = Math.floor(Math.random() * (mainPosition.height - 62.5));
  canvas.value.width = (mainPosition.width) * devicePixelRatio;
  canvas.value.height = (mainPosition.height - 62.5) * devicePixelRatio;
  canvas.value.style.width = `${mainPosition.width}px`;
  canvas.value.style.height = `${mainPosition.height - 62.5}px`;
  context.value.scale(devicePixelRatio, devicePixelRatio);

  for (const ball of balls) {
    ball.x = Math.random() * ((canvas.value.width - 30) / devicePixelRatio);
    ball.y = Math.random() * ((canvas.value.height - 62.5) / devicePixelRatio);
    ball.targetX = randomTargetX;
    ball.targetY = randomTargetY;
  }
}

function animate() {
  animationFrameId = requestAnimationFrame(animate);

  if (!context.value) {
    return;
  }

  context.value.clearRect(0, 0, canvas.value.width, canvas.value.height);

  balls.forEach((ball) => {
    updateBall(ball);
    drawBall(ball);

    let allBallsReachedTarget = true;

    balls.forEach((ball) => {
      if (Math.abs(ball.x - ball.targetX) > 2) {
        allBallsReachedTarget = false;
        return;
      }
    });

    if (allBallsReachedTarget) {
      console.log('all balls reached target')
      cancelAnimationFrame(animationFrameId);
    }
  });
}

function updateBall(ball) {
  const dx = ball.targetX - ball.x;
  const dy = ball.targetY - ball.y;
  const distance = Math.sqrt(dx * dx + dy * dy);

  if (distance > ball.speed) {
    ball.x += (dx / distance) * ball.speed;
    ball.y += (dy / distance) * ball.speed;
  }
}

function drawBall(ball) {
  if (!context.value) {
    return;
  }

  context.value.beginPath();
  context.value.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
  context.value.fillStyle = "#A5F12B";
  context.value.fill();
  context.value.closePath();

  // 在球的裡面加上0
  context.value.fillStyle = "#000";
  context.value.font = "15px sans-serif";
  context.value.textAlign = "center";
  context.value.textBaseline = "middle";
  context.value.fillText(ball.number.toString(), ball.x, ball.y);
}
</script>

<template>
  <canvas ref="canvas" />
</template>

<style lang="scss" scoped>
@import './style.scss';
</style>