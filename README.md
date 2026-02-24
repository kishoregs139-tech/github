<!DOCTYPE html>
<html>
<head>
  <title>Mini Subway Runner</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: #222;
      color: white;
      font-family: Arial, sans-serif;
    }
    canvas {
      display: block;
      margin: auto;
      background: #444;
    }
    #score {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 20px;
    }
  </style>
</head>
<body>

<div id="score">Score: 0</div>
<canvas id="gameCanvas" width="400" height="600"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const lanes = [100, 200, 300];
let currentLane = 1;

let player = {
  x: lanes[currentLane],
  y: 500,
  width: 40,
  height: 60,
  dy: 0,
  gravity: 0.6,
  jumpPower: -12,
  isJumping: false
};

let obstacles = [];
let speed = 5;
let score = 0;
let gameOver = false;

function spawnObstacle() {
  let lane = Math.floor(Math.random() * 3);
  obstacles.push({
    x: lanes[lane],
    y: -60,
    width: 40,
    height: 60
  });
}

setInterval(spawnObstacle, 1500);

function update() {
  if (gameOver) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Player gravity
  player.dy += player.gravity;
  player.y += player.dy;

  if (player.y > 500) {
    player.y = 500;
    player.isJumping = false;
  }

  player.x = lanes[currentLane];

  // Draw player
  ctx.fillStyle = "yellow";
  ctx.fillRect(player.x - player.width/2, player.y, player.width, player.height);

  // Obstacles
  ctx.fillStyle = "red";
  for (let i = 0; i < obstacles.length; i++) {
    obstacles[i].y += speed;
    ctx.fillRect(
      obstacles[i].x - obstacles[i].width/2,
      obstacles[i].y,
      obstacles[i].width,
      obstacles[i].height
    );

    // Collision detection
    if (
      player.x - player.width/2 < obstacles[i].x + obstacles[i].width/2 &&
      player.x + player.width/2 > obstacles[i].x - obstacles[i].width/2 &&
      player.y < obstacles[i].y + obstacles[i].height &&
      player.y + player.height > obstacles[i].y
    ) {
      gameOver = true;
      alert("Game Over! Final Score: " + score);
      location.reload();
    }
  }

  // Remove off-screen obstacles
  obstacles = obstacles.filter(o => o.y < canvas.height);

  score++;
  document.getElementById("score").innerText = "Score: " + score;

  requestAnimationFrame(update);
}

document.addEventListener("keydown", function(e) {
  if (e.key === "ArrowLeft" && currentLane > 0) {
    currentLane--;
  }
  if (e.key === "ArrowRight" && currentLane < 2) {
    currentLane++;
  }
  if (e.key === "ArrowUp" && !player.isJumping) {
    player.dy = player.jumpPower;
    player.isJumping = true;
  }
});

update();
</script>

</body>
</html>
