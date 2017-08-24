<template>
  <div id="app">
    <canvas id="canvas" width="1000px" height="800px"></canvas>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      canvas: null,
      ctx: null,
      width: null,
      height: null,
      rainLoop: null,
      particles: [],
      maxParts: 1000
    }
  },
  mounted: function () {
    this.canvas = document.getElementById('canvas')
    this.ctx = this.canvas.getContext('2d')
    this.width = this.canvas.width
    this.height = this.canvas.height
    this.init()
    if (typeof this.rainLoop !== 'undefined') clearInterval(this.rainLoop)
    this.rainLoop = setInterval(this.draw, 30)
  },
  methods: {
    init () {
      const init = []
      this.ctx.strokeStyle = 'rgba(128,0,128,0.3)'
      this.ctx.lineWidth = 1
      this.ctx.lineCap = 'butt'

      for (var a = 0; a < this.maxParts; a++) {
        init.push({
          x: Math.random() * this.width,
          y: Math.random() * this.height,
          l: 0.8,
          xs: -4 + Math.random() * 4 + 2,
          ys: Math.random() * 10 + 10
        })
      }

      for (var b = 0; b < this.maxParts; b++) {
        this.particles[b] = init[b]
      }
    },
    draw () {
      this.ctx.clearRect(0, 0, this.width, this.height)
      for (let c = 0; c < this.particles.length; c += 1) {
        const p = this.particles[c]
        this.ctx.beginPath()
        this.ctx.moveTo(p.x, p.y)
        this.ctx.lineTo(p.x + p.l * p.xs, p.y + p.l * p.ys)
        this.ctx.stroke()
      }
      this.move()
    },
    move () {
      for (var b = 0; b < this.particles.length; b += 1) {
        var p = this.particles[b]
        p.x += p.xs
        p.y += p.ys
        if (p.x > this.width || p.y > this.height) {
          p.x = Math.random() * this.width
          p.y = -20
        }
      }
    }
  }
}
</script>

<style>
html {
    background: rgba(221,160,221,0.9);
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

#youtube-audio {
  display: none;
}

canvas {
  background: transparent;
  width: 100%;
  height: 100%;
}
</style>
