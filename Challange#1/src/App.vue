<template>
  <div id="app">
    <img src="./assets/snake.svg" width="150">
    <canvas id="canvas" width="500px" height="500px" ></canvas>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      canvas: null,
      ctx: null,
      gameLoop: null,
      scale: 10,
      score: 0,
      direction: 'right',
      snake: {
        initialSize: 5,
        tail: [],
      },
      food: null,
      width: 0,
      height: 0,
    };
  },
  mounted: function mountedApp() {
    window.addEventListener('keydown', this.handleKeydown);
    this.canvas = document.getElementById('canvas');
    this.ctx = this.canvas.getContext('2d');
    this.width = this.canvas.width;
    this.height = this.canvas.height;
    this.init();
  },
  computed: {

  },
  methods: {
    handleKeydown(evt) {
      switch (evt.keyCode) {
        case 38: // up arrow
        case 87: // W
          if (this.direction !== 'down') this.direction = 'up';
          break;
        case 39: // right arrow
        case 68: // D
          if (this.direction !== 'left') this.direction = 'right';
          break;
        case 40: // down arrow
        case 83: // S
          if (this.direction !== 'up') this.direction = 'down';
          break;
        case 37: // left arrow
        case 65: // A
          if (this.direction !== 'right') this.direction = 'left';
          break;
        default:
      }
    },
    spawnFood() {
      this.food = {
        x: Math.round((Math.random() * (this.width - this.scale)) / this.scale),
        y: Math.round((Math.random() * (this.height - this.score)) / this.scale),
      };
    },
    spawnSnake() {
      this.snake.tail = []; // reset snake tail
      for (let i = this.snake.initialSize - 1; i >= 0; i -= 1) {
        this.snake.tail.push({ x: i, y: 0 });
      }
    },
    drawSqr(x, y, type = 0) {
      this.ctx.fillStyle = (type === 0) ? 'blue' : 'red';
      this.ctx.fillRect(x * this.scale, y * this.scale, this.scale, this.scale);
      this.ctx.strokeStyle = 'white';
      this.ctx.strokeRect(x * this.scale, y * this.scale, this.scale, this.scale);
    },
    init() {
      this.direction = 'right'; // set start direction
      this.spawnSnake();
      this.spawnFood();
      this.score = 0; // reset score
      if (typeof this.gameLoop !== 'undefined') clearInterval(this.gameLoop);
      this.gameLoop = setInterval(this.renderGame, 60);
    },
    isCollision(x, y, array) {
      for (let i = 0; i < array.length; i += 1) {
        if (array[i].x === x && array[i].y === y) return true;
      }
      return false;
    },
    renderGame() {
      this.ctx.fillStyle = 'white';
      this.ctx.fillRect(0, 0, this.width, this.height);
      this.ctx.strokeStyle = 'black';
      this.ctx.strokeRect(0, 0, this.width, this.height);
      let nx = this.snake.tail[0].x;
      let ny = this.snake.tail[0].y;
      switch (this.direction) {
        case 'right':
          nx += 1;
          break;
        case 'left':
          nx -= 1;
          break;
        case 'up':
          ny -= 1;
          break;
        case 'down':
          ny += 1;
          break;
        default:
          break;
      }
      if (nx === -1 || nx === this.width / this.scale || ny === -1 ||
        ny === this.height / this.scale || this.isCollision(nx, ny, this.snake.tail)) {
        this.init();
        return;
      }
      let tail;
      if (nx === this.food.x && ny === this.food.y) {
        tail = { x: nx, y: ny };
        this.score += 1;
        this.spawnFood();
      } else {
        tail = this.snake.tail.pop();
        tail.x = nx;
        tail.y = ny;
      }
      this.snake.tail.unshift(tail);
      for (let i = 0; i < this.snake.tail.length; i += 1) {
        const c = this.snake.tail[i];
        this.drawSqr(c.x, c.y);
      }
      this.drawSqr(this.food.x, this.food.y, 1);
      this.ctx.fillText(`Score: ${this.score}`, 5, this.height - 5);
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
    background: white;
    border: 1px solid #000;
  }
</style>
