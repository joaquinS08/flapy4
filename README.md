<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mi primer juego para Cami</title>
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #70c5ce;
      font-family: Arial, sans-serif;
    }
    canvas {
      background-color: #fff;
      display: block;
      border: 2px solid #000;
    }
  </style>
</head>
<body>

  <canvas id="gameCanvas" width="320" height="480"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const GRAVITY = 0.5;
    const JUMP_STRENGTH = -10;
    const SPAWN_RATE = 100;  // Decreased for better gameplay
    let score = 0;
    let isGameOver = false;

    let heart = {
      x: 50,
      y: canvas.height / 2,
      width: 30,
      height: 30,
      velocityY: 0
    };

    let obstacles = [];
    let frame = 0;

    // Función para dibujar el corazón
    function drawHeart() {
      ctx.fillStyle = "#FF0000";
      ctx.beginPath();
      ctx.moveTo(heart.x, heart.y);
      ctx.arc(heart.x - 10, heart.y, 10, Math.PI, 0, true);
      ctx.arc(heart.x + 10, heart.y, 10, Math.PI, 0, true);
      ctx.lineTo(heart.x, heart.y + 20);
      ctx.fill();
    }

    // Función para dibujar los obstáculos
    function drawObstacles() {
      ctx.fillStyle = "#00FF00";
      obstacles.forEach(obstacle => {
        ctx.fillRect(obstacle.x, obstacle.top, 20, obstacle.height);
        ctx.fillRect(obstacle.x, obstacle.bottom, 20, canvas.height - obstacle.bottom);
      });
    }

    // Función para actualizar la posición de los obstáculos
    function updateObstacles() {
      if (frame % SPAWN_RATE === 0) {
        let obstacleHeight = Math.floor(Math.random() * (canvas.height / 2)) + 50;
        let gap = 150;
        obstacles.push({
          x: canvas.width,
          top: 0,
          bottom: obstacleHeight + gap,
          height: obstacleHeight
        });
      }

      obstacles.forEach(obstacle => {
        obstacle.x -= 2;
      });

      obstacles = obstacles.filter(obstacle => obstacle.x + 20 > 0);
    }

    // Función para comprobar las colisiones
    function checkCollisions() {
      obstacles.forEach(obstacle => {
        if (heart.x + heart.width > obstacle.x && heart.x < obstacle.x + 20) {
          if (heart.y < obstacle.height || heart.y + heart.height > obstacle.bottom) {
            isGameOver = true;
          }
        }
      });

      if (heart.y > canvas.height || heart.y < 0) {
        isGameOver = true;
      }
    }

    // Función para mostrar la puntuación
    function drawScore() {
      ctx.fillStyle = "#000";
      ctx.font = "20px Arial";
      ctx.fillText("Puntos: " + score, 10, 30);
    }

    // Función para saltar
    function jump() {
      if (!isGameOver) {
        heart.velocityY = JUMP_STRENGTH;
      }
    }

    // Función para mostrar el mensaje final
    function showGameOver() {
      ctx.fillStyle = "rgba(0, 0, 0, 0.5)";
      ctx.fillRect(0, canvas.height / 2 - 30, canvas.width, 60);
      ctx.fillStyle = "#fff";
      ctx.font = "20px Arial";
      ctx.fillText("Mi primer juego para Cami TQM ❤️", 20, canvas.height / 2);
    }

    // Función para actualizar el juego
    function update() {
      if (isGameOver) {
        showGameOver();
        return;
      }

      frame++;
      heart.velocityY += GRAVITY;
      heart.y += heart.velocityY;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawHeart();
      updateObstacles();
      drawObstacles();
      drawScore();
      checkCollisions();

      score++;
      if (score >= 10) {
        isGameOver = true;
        showGameOver();
      }
    }

    // Control del teclado y el tactil
    window.addEventListener("keydown", (e) => {
      if (e.key === " " || e.code === "Space") {
        jump();
      }
    });

    canvas.addEventListener("touchstart", (e) => {
      jump();
    });

    // Llamada a la función de actualización en un intervalo
    setInterval(update, 1000 / 60);
  </script>

</body>
</html>
