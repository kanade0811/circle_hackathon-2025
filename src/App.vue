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
    const fps = 30;
    const grid = 70;
    // const angle = (1 / 3) * Math.PI;
    let circles = [];
    const elasticity = 1;

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    function collision(circleA, circleB) {
      const e = elasticity;
      const mA = circleA.mass;
      const vAx = circleA.velocity.x;
      const vAy = circleA.velocity.y;
      const mB = circleB.mass;
      const vBx = circleB.velocity.x;
      const vBy = circleB.velocity.y;

      circleA.velocity.x =
        (mA * vAx + mB * vBx - e * mB * (vAx - vBx)) / (mA + mB);
      circleA.velocity.y =
        (mA * vAy + mB * vBy - e * mB * (vAy - vBy)) / (mA + mB);
      circleB.velocity.x =
        (mA * vAx + mB * vBx + e * mA * (vAx - vBx)) / (mA + mB);
      circleB.velocity.y =
        (mA * vAy + mB * vBy + e * mA * (vAy - vBy)) / (mA + mB);
    }

    function circlesOverlap(circleA, circleB) {
      // 最後の円がこれまでの円と被っていないかを判定する関数
      if (circles.length === 0) return false;
      const length = Math.hypot(
        circleB.coordinate.x - circleA.coordinate.x,
        circleB.coordinate.y - circleA.coordinate.y
      );
      /*
      if (
        length > circleA.radius + circleB.radius ||
        length < Math.abs(circleA.radius - circleB.radius)
      ) {
        return false;
      }
      */
      if (length > circleA.radius + circleB.radius) return false;
      return true;
    }

    function separation(circleA, circleB) {
      if (circlesOverlap(circleA, circleB)) {
        for (let circle of [circleA, circleB]) {
          circle.coordinate.x -= circle.velocity.x;
          circle.coordinate.y -= circle.velocity.y;
        }
      }
    }

    const make = {
      circles() {
        let newCircle = {
          coordinate: {},
          velocity: {},
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
        for (let circle of circles) {
          if (circlesOverlap(circle, newCircle)) return;
        }
        newCircle.mass = (4 / 3) * Math.PI * newCircle.radius ** 3;
        newCircle.velocity.x = Math.random() - 1 / 2;
        newCircle.velocity.y = Math.random() - 1 / 2;
        circles.push(newCircle);
      },
      move() {
        for (let circle of circles) {
          circle.coordinate.x += circle.velocity.x;
          circle.coordinate.y += circle.velocity.y;
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

    function update() {
      ctx.clearRect(0, 0, window.innerWidth, window.innerHeight);
      draw.circles();
      make.move();
      for (let circleA of circles) {
        for (let circleB of circles) {
          if (circleA === circleB) {
            continue;
          } else if (circlesOverlap(circleA, circleB)) {
            separation(circleA, circleB);
            collision(circleA, circleB);
          }
        }
      }
    }

    resize();
    // draw.axes();
    while (circles.length !== 5) make.circles();
    setInterval(update, 1000 / fps);
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
