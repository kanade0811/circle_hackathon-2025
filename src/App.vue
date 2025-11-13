<template>
  <div id="app">
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script>
export default {
  mounted() {
    const canvas = this.$refs.canvas;
    const ctx = canvas.getContext("2d");

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    const draw = {
      axis() {
        ctx.lineWidth = 1;

        const width = window.innerWidth;
        const height = window.innerHeight;

        const grid = 50;
        const angle = (1 / 3) * Math.PI;

        ctx.strokeStyle = "red";
        for (let x = 0; x < width; x += grid) {
          ctx.beginPath();
          ctx.moveTo(x, 0);
          ctx.lineTo(x, height);
          ctx.stroke();
        }

        ctx.strokeStyle = "blue";
        for (let y = 0; y < height; y += grid) {
          ctx.beginPath();
          ctx.moveTo(0, y);
          ctx.lineTo(width, y);
          ctx.stroke();
        }
        ctx.strokeStyle = "green";
        for (let x = -height / Math.tan(angle); x < width; x += grid) {
          ctx.beginPath();
          ctx.moveTo(x, height);
          ctx.lineTo(x + height / Math.tan(angle), 0);
          ctx.stroke();
        }

        ctx.strokeStyle = "black";
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(3 * grid, 0);
        ctx.lineTo(3 * grid, height);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(0, 14 * grid);
        ctx.lineTo(width, 14 * grid);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(-height / Math.tan(angle)+11*grid, height);
        ctx.lineTo(-height / Math.tan(angle)+11*grid + height / Math.tan(angle), 0);
        ctx.stroke();
      },
    };

    resize();
    draw.axis();
  },
};
</script>

<style>
body,
#app {
  margin: 0;
  padding: 0;
  overflow: hidden;
}
canvas {
  display: block;
  background-color: white;
}
</style>
