<template>
  <div id="app">
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script>
export default {
  mounted() {
    const grid = 70;
    // const angle = (1 / 3) * Math.PI;

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    const draw = {
      axes() {
        ctx.lineWidth = 1;
        const width = window.innerWidth;
        const height = window.innerHeight;

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

        ctx.strokeStyle = "black";
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(3 * grid, 0);
        ctx.lineTo(3 * grid, height);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(0, 10 * grid);
        ctx.lineTo(width, 10 * grid);
        ctx.stroke();
      },
      circle() {
        let point = [];
        const rows = Math.floor(window.innerWidth / grid);
        const cols = Math.floor(window.innerHeight / grid);
        // マジックナンバー直す
        const radius=Math.floor(Math.random()*8)/4
        for (let k = 0; k < 2; k++) {
          point[k]=Math.floor(Math.random()*10);
        }
        ctx.lineWidth = 1;
        ctx.strokeStyle = "black";
        ctx.beginPath();
        ctx.arc(point[0] * grid, point[1] * grid, radius * grid, 0, 2 * Math.PI);
        ctx.stroke();
      },
      fixedCircle([x, y], radius) {
        ctx.lineWidth = 1;
        ctx.strokeStyle = "black";
        ctx.beginPath();
        ctx.arc(x * grid, y * grid, radius * grid, 0, 2 * Math.PI);
        ctx.stroke();
      },
      axes3D() {
        ctx.lineWidth = 1;

        const width = window.innerWidth;
        const height = window.innerHeight;

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
        ctx.moveTo(-height / Math.tan(angle) + 11 * grid, height);
        ctx.lineTo(
          -height / Math.tan(angle) + 11 * grid + height / Math.tan(angle),
          0
        );
        ctx.stroke();
      },
    };

    const canvas = this.$refs.canvas;
    const ctx = canvas.getContext("2d");

    resize();
    draw.axes();
    for(let k=0;k<7;k++){
      draw.circle();
    }
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
