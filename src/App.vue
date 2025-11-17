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
    // const elasticity=1;

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    function circlesOverlap(newCircle) {
      // 最後の円がこれまでの円と被っていないかを判定する関数
      if (circles.length !== 0) {
        for (let circle of circles) {
          const length = Math.hypot(
            circle.coordinate.x - newCircle.coordinate.x,
            circle.coordinate.y - newCircle.coordinate.y
          );
          // 円同士の位置が外部で無ければpushせずに作り直す
          if (
            !(
              length > circle.radius + newCircle.radius ||
              length < Math.abs(circle.radius - newCircle.radius)
            )
          )
            return;
        }
      }
      circles.push(newCircle);
    }

    const make = {
      circles() {
        while (circles.length !== 7) {
          let newCircle = {
            coordinate:{}
          };
          const rows = Math.floor(window.innerWidth / grid);
          const cols = Math.floor(window.innerHeight / grid);
          // マジックナンバー直す
          if (rows > cols) {
            newCircle.radius = Math.random() * rows;
          } else {
            newCircle.radius = Math.random() * cols;
          }
          newCircle.coordinate.x = Math.random() * (rows + newCircle.radius);
          newCircle.coordinate.y = Math.random() * (cols + newCircle.radius);
          circlesOverlap(newCircle);
        }
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
      circles() {
        for (let circle of circles) {
          ctx.lineWidth = 1;
          ctx.strokeStyle = "black";
          ctx.beginPath();
          ctx.arc(
            circle.coordinate.x * grid,
            circle.coordinate.y * grid,
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
    // draw.axes();
    make.circles();
    draw.circles();
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
