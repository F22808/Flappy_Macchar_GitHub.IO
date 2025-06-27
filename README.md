<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1.0" />
  <title>Flappy Macchar</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <script src="script.js"></script>
</body>
</html>
* { margin: 0; padding: 0; box-sizing: border-box; }
body, html { width: 100%; height: 100%; overflow: hidden; background: #70c5ce; }
canvas { display: block; margin: auto; background: url('background.png') no-repeat center; background-size: cover; }
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
canvas.width = 800; canvas.height = 600;

const mosquito = new Image();
mosquito.src = 'mosquito.png';

let frame = 0, gravity = 0.5;
const flap =  -10;

const player = { x:150, y:300, width:60, height:45, dy:0 };

const pipes = [];
const pipeWidth = 80;
const spacing = 180;
const pipeSpeed = 2;

// Input
window.addEventListener('keydown', e => {
  if(e.code === 'Space') player.dy = flap;
});
canvas.addEventListener('mousedown', () => { player.dy = flap; });

function createPipe() {
  const topHeight = Math.random() * (canvas.height - spacing - 100) + 50;
  pipes.push({ x: canvas.width, y:0, height:topHeight });
  pipes.push({ x: canvas.width, y:topHeight + spacing, height: canvas.height - (topHeight + spacing) });
}

function draw() {
  ctx.clearRect(0,0,canvas.width,canvas.height);
  ctx.drawImage(mosquito, player.x, player.y, player.width, player.height);

  pipes.forEach(pipe => {
    ctx.fillStyle = '#228B22';
    ctx.fillRect(pipe.x, pipe.y, pipeWidth, pipe.height);
  });

  ctx.fillStyle = '#fff';
  ctx.font = '30px sans-serif';
  ctx.fillText('Score: ' + Math.floor(frame/100), 10, 50);
}

function update() {
  frame++;
  if(frame % 100 === 0) createPipe();

  player.dy += gravity;
  player.y = Math.max(0, Math.min(canvas.height - player.height, player.y + player.dy));

  pipes.forEach(pipe => pipe.x -= pipeSpeed);
  if(pipes.length && pipes[0].x + pipeWidth < 0) pipes.splice(0, 2);

  // Collision
  pipes.forEach(pipe => {
    if(
      player.x < pipe.x + pipeWidth &&
      player.x + player.width > pipe.x &&
      player.y < pipe.y + pipe.height &&
      player.y + player.height > pipe.y
    ) {
      // Reset
      pipes.length = 0;
      player.y = 300; player.dy = 0;
      frame = 0;
    }
  });
}

function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

mosquito.onload = () => {
  loop();
};
