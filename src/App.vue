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
    const grid = 70;
    // const angle = (1 / 3) * Math.PI;
    let circles = [];

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    function circlesOverlap(newCircle) {
      // 最後の円がこれまでの円と被っていないかを判定する関数
      if (circles.length !== 0) {
        for (let circle of circles) {
          const length = Math.hypot(circle.x - newCircle.x,circle.y - newCircle.y);
          const radiusSum = circle.radius + newCircle.radius;
          // 円同士の位置が外部で無ければpushせずに作り直す
          if (length < radiusSum) return;
        }
      }
      circles.push(newCircle);
    }

    const make = {
      circle() {
        let newCircle = {};
        let point = [];
        const rows = Math.floor(window.innerWidth / grid);
        const cols = Math.floor(window.innerHeight / grid);
        // マジックナンバー直す
        newCircle.radius = Math.floor(Math.random() * 8) / 4;
        for (let axis of ["x", "y"]) {
          newCircle[axis] = Math.floor(Math.random() * 10);
        }
        ctx.stroke();
        circlesOverlap(newCircle);
      },
    };

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
        for (let circle of circles) {
          ctx.lineWidth = 1;
          ctx.strokeStyle = "black";
          ctx.beginPath();
          ctx.arc(
            circle.x * grid,
            circle.y * grid,
            circle.radius * grid,
            0,
            2 * Math.PI
          );
          ctx.stroke();
        }
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

    resize();
    draw.axes();
    while (circles.length !== 7) {
      make.circle();
    }
    draw.circle();
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
