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
    const grid = 30;
    const fps = 30;
    const angle = (1 / 3) * Math.PI;
    let circles = [];
    const elasticity = 1;
    const speed = 10;

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    function circlesOverlap(circleA, circleB) {
      const length = Math.hypot(
        circleB.coordinate.x - circleA.coordinate.x,
        circleB.coordinate.y - circleA.coordinate.y
      );
      if (!(length > circleA.radius + circleB.radius)) return true;
      else return false;
    }

    class Circle {
      constructor() {
        remake: while (true) {
          this.radius = 100;
          this.coordinate = {
            x: Math.random() * window.innerWidth,
            y: Math.random() * window.innerHeight,
          };

          if (!(circles.length === 0)) {
            for (let circle of circles) {
              const length = Math.hypot(
                circle.coordinate.x - this.coordinate.x,
                circle.coordinate.y - this.coordinate.y
              );
              if (length < circle.radius + this.radius) {
                continue remake;
              }
            }
          }
          break remake;
        }
        this.mass = (4 / 3) * Math.PI * this.radius ** 3;
        this.velocity = {
          x: (Math.random() - 1 / 2) * speed,
          y: (Math.random() - 1 / 2) * speed,
        };
        circles.push(this);
      }
      move() {
        this.coordinate.x += this.velocity.x;
        this.coordinate.y += this.velocity.y;
      }
      circle() {
        for (let target of circles) {
          if (this === target) continue;
          if (circlesOverlap(this, target)) {
            while (circlesOverlap(this, target)) {
              for (let circle of [this, target]) {
                circle.coordinate.x -= circle.velocity.x;
                circle.coordinate.y -= circle.velocity.y;
              }
            }
            collision(this,target)
          }
        }
      }
      border(){
        if(this.coordinate.x-this.radius<0){
          this.coordinate.x=this.radius;
          this.velocity.x*=-elasticity;
        }
        if(this.coordinate.x+this.radius>window.innerWidth){
          this.coordinate.x=window.innerWidth-this.radius;
          this.velocity.x*=-elasticity;
        }
        if(this.coordinate.y-this.radius<0){
          this.coordinate.y=this.radius;
          this.velocity.y*=-elasticity;
        }
        if(this.coordinate.y+this.radius>window.innerHeight){
          this.coordinate.y=window.innerHeight-this.radius;
          this.velocity.y*=-elasticity;
        }
      }
      draw() {
        ctx.lineWidth = 1;
        ctx.strokeStyle = "black";
        ctx.beginPath();
        ctx.arc(
          this.coordinate.x,
          this.coordinate.y,
          this.radius,
          0,
          2 * Math.PI
        );
        ctx.stroke();
      }
      update() {
        this.move();
        this.circle();
        this.border();
        this.draw();
      }
    }

    function collision(circleA, circleB) {
      // collision
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
      for (let circle of circles) {
        circle.update();
      }
    }

    resize();
    // draw.axes();
    while (circles.length !== 5) new Circle();
    setInterval(update, 1000 / fps);
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
