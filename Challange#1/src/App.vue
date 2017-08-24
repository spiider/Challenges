<template>
  <div id="app">
    <img src="./assets/snake.svg" width="150">
    <canvas id="canvas" width="500px" height="500px" ></canvas>
  </div>
</template>

<script>
const SNAKE_SPEED = 10;
export default {
  name: 'app',
  data() {
    return {
      canvas: null,
      ctx: null,
      scale: 20,
      snake: {
        positionX: 0,
        positionY: 0,
        color: 'green',
        velocityX: 1,
        velocityY: 0,
        total: 0,
        tail: [],
      },
      food: {
        color: 'red',
        positionX: null,
        positionY: null,
      },
      width: 500,
      height: 500,
    };
  },
  mounted: function mountedApp() {
    window.addEventListener('keydown', this.handleKeydown);
    this.canvas = document.getElementById('canvas');
    this.ctx = this.canvas.getContext('2d');
    this.respawnFood();
    setInterval(this.renderSnake, 1000 / SNAKE_SPEED);
  },
  computed: {

  },
  methods: {
    handleKeydown(evt) {
      switch (evt.keyCode) {
        case 38: // up arrow
        case 87: // W
          this.moveSnake(0, -1);
          break;
        case 39: // right arrow
        case 68: // D
          this.moveSnake(1, 0);
          break;
        case 40: // down arrow
        case 83: // S
          this.moveSnake(0, 1);
          break;
        case 37: // left arrow
        case 65: // A
          this.moveSnake(-1, 0);
          break;
        default:
          break;
      }
    },
    moveSnake(x, y) {
      this.snake.velocityX = x;
      this.snake.velocityY = y;
    },
    updateSnake() {
      for (let i = 0; i < this.snake.total - 1; i += 1) {
        this.snake.tail[i] = this.snake.tail[i + i];
      }
      this.snake.tail[this.snake.total - 1] = [this.snake.positionX, this.snake.positionY];
      this.snake.positionX += this.snake.velocityX * this.scale;
      this.snake.positionY += this.snake.velocityY * this.scale;
      if (this.snake.positionX > this.width - this.scale || this.snake.positionX < 0) {
        this.snake.positionX = (this.snake.positionX > 0) ? 0 : this.width - this.scale;
      }
      if (this.snake.positionY > this.height - this.scale || this.snake.positionY < 0) {
        this.snake.positionY = (this.snake.positionY > 0) ? 0 : this.height - this.scale;
      }
    },
    drawFood() {
      this.ctx.beginPath();
      this.ctx.fillStyle = this.food.color;
      this.ctx.rect(this.food.positionX, this.food.positionY, this.scale / 1.5,
         this.scale / 1.5);
      this.ctx.fill();
    },
    respawnFood() {
      const cols = Math.floor(this.width - this.scale);
      const rows = Math.floor(this.height - this.scale);
      this.food.positionX = Math.floor(Math.random() * cols);
      this.food.positionY = Math.floor(Math.random() * rows);
    },
    eatFood() {
      const a = this.snake.positionX - this.food.positionX;
      const b = this.snake.positionY - this.food.positionY;
      const c = Math.sqrt((a * a) + (b * b));
      if (c < 15) {
        this.snake.total += 1;
        this.respawnFood();
      }
    },
    renderSnake() {
      this.ctx.clearRect(0, 0, 500, 500); // clear trail
      this.drawFood();
      this.updateSnake();
      this.eatFood();
      this.ctx.beginPath();
      this.ctx.fillStyle = this.snake.color;
      for (let i = 0; i < this.snake.total; i += 1) {
        this.ctx.rect(this.snake.tail[i][0], this.snake.tail[i][1], this.scale, this.scale);
      }
      this.ctx.rect(this.snake.positionX, this.snake.positionY, this.scale, this.scale);
      this.ctx.fill();
    },
  },
};
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 10px;
  }

  canvas {
    background: darkgrey;
  }
</style>
