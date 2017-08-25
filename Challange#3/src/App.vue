<template>
  <div id="app">
    <canvas id="canvas" width="600px" height="600px"></canvas>
  </div>
</template>

<script>

export default {
  name: 'app',
  components: {
  },
  data() {
    return {
      canvas: null,
      ctx: null,
      width: 0,
      height: 0,
      r: 4,
      k: 30,
      grid: [],
      w: 0,
      active: [],
      cols: null,
      rows: null,
      ordered: [],
    };
  },
  mounted: function monted() {
    this.canvas = document.getElementById('canvas');
    this.ctx = this.canvas.getContext('2d');
    this.width = this.canvas.width;
    this.height = this.canvas.height;
    this.w = this.r / Math.sqrt(2);
    this.init();
  },
  methods: {
    init() {
      this.ctx.lineWidth = 4; // strokeWeight(4);
      // STEP 0
      this.cols = Math.floor(this.width / this.w);
      this.rows = Math.floor(this.height / this.w);
      for (let i = 0; i < this.cols * this.rows; i += 1) {
        this.grid[i] = undefined;
      }
      // STEP 1
      const x = this.width / 2;
      const y = this.height / 2;
      const i = Math.floor(this.x / this.w);
      const j = Math.floor(this.y / this.w);
      const pos = { x, y };
      this.grid[i + (j * this.cols)] = pos;
      this.active.push(pos);
      this.draw();
    },
    draw() {
      for (let total = 0; total < 25; total += 1) {
        if (this.active.length > 0) {
          const randIndex = Math.floor(Math.random() * this.active.length);
          // const pos = this.active[randIndex];
          let found = false;
          for (let n = 0; n < this.k; n += 1) {
            const angle = Math.random() * (Math.PI * 2);
            const sample = { x: Math.cos(angle), y: Math.sin(angle), z: 0 };
            // const m = (Math.random() * (this.r - (2 * this.r))) + (2 * this.r);
            // sample = this.normalize().mult(n)
            // sample.setMag(m);
            // sample.add(pos);

            const col = Math.floor(sample.x / this.w);
            const row = Math.floor(sample.y / this.w);

            if (col > -1 && row > -1 && col < this.cols && row < this.rows &&
              !this.grid[col + (row * this.cols)]) {
              let ok = true;
              for (let i = -1; i <= 1; i += 1) {
                for (let j = -1; j <= 1; j += 1) {
                  const index = ((col + i) + (row + j)) * this.cols;
                  const neighbor = this.grid[index];
                  if (neighbor) {
                    const a = sample.x - neighbor.x;
                    const b = sample.y - neighbor.y;
                    const d = Math.sqrt((a * a) + (b * b));
                    if (d < this.r) {
                      ok = false;
                    }
                  }
                }
              }
              if (ok) {
                found = true;
                this.grid[(col + row) * this.cols] = sample;
                this.active.push(sample);
                this.ordered.push(sample);
                // Should we break?
                break;
              }
            }
          }
          if (!found) {
            this.active.splice(randIndex, 1);
          }
        }
      }

      for (let i = 0; i < this.ordered.length; i += 1) {
        if (this.ordered[i]) {
          // stroke(i % 360, 100, 100);
          this.ctx.strokeStyle = `rgb(${i % 360}, 100, 100)`;
          this.ctx.lineWidth = this.r * 0.5;
          // point(this.ordered[i].x, this.ordered[i].y);
          this.ctx.strokeRect(this.ordered[i].x, this.ordered[i].y, 1, 1);
        }
      }
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
  margin-top: 60px;
}
canvas {
  background: black;
}
</style>
