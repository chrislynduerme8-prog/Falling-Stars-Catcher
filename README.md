<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Square Dodger</title>
  <style>
    body, html {
      margin: 0;
      padding: 0;
      overflow: hidden;
      height: 100vh;
      background: #111;
      font-family: Arial, sans-serif;
      color: white;
    }

    #game {
      position: relative;
      width: 100vw;
      height: 100vh;
      background-color: #222;
    }

    #player {
      position: absolute;
      bottom: 20px;
      left: 50%;
      width: 50px;
      height: 50px;
      background: lime;
      transform: translateX(-50%);
    }

    .block {
      position: absolute;
      width: 50px;
      height: 50px;
      background: red;
    }

    #score {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 24px;
    }
  </style>
</head>
<body>
  <div id="game">
    <div id="player"></div>
    <div id="score">Score: 0</div>
  </div>

  <script>
    const game = document.getElementById('game');
    const player = document.getElementById('player');
    const scoreDisplay = document.getElementById('score');
    let score = 0;
    let gameOver = false;
    let speed = 5;

    // Movement
    let moveLeft = false;
    let moveRight = false;

    document.addEventListener('keydown', (e) => {
      if (e.key === 'ArrowLeft') moveLeft = true;
      if (e.key === 'ArrowRight') moveRight = true;
    });

    document.addEventListener('keyup', (e) => {
      if (e.key === 'ArrowLeft') moveLeft = false;
      if (e.key === 'ArrowRight') moveRight = false;
    });

    function movePlayer() {
      const left = parseInt(window.getComputedStyle(player).left);
      if (moveLeft && left > 0) {
        player.style.left = `${left - 7}px`;
      }
      if (moveRight && left < window.innerWidth - 50) {
        player.style.left = `${left + 7}px`;
      }
    }

    function createBlock() {
      const block = document.createElement('div');
      block.classList.add('block');
      block.style.left = `${Math.random() * (window.innerWidth - 50)}px`;
      block.style.top = '-50px';
      game.appendChild(block);

      let fall = setInterval(() => {
        if (gameOver) {
          clearInterval(fall);
          return;
        }

        const top = parseInt(block.style.top);
        block.style.top = `${top + speed}px`;

        const blockRect = block.getBoundingClientRect();
        const playerRect = player.getBoundingClientRect();

        // Collision
        if (
          blockRect.bottom > playerRect.top &&
          blockRect.top < playerRect.bottom &&
          blockRect.left < playerRect.right &&
          blockRect.right > playerRect.left
        ) {
          endGame();
          clearInterval(fall);
          return;
        }

        if (top > window.innerHeight) {
          clearInterval(fall);
          block.remove();
          score++;
          scoreDisplay.textContent = `Score: ${score}`;
          // Increase speed every 5 points
          if (score % 5 === 0) {
            speed += 0.5;
          }
        }
      }, 20);
    }

    function endGame() {
      gameOver = true;
      alert('Game Over! Your score: ' + score);
      location.reload();
    }

    // Game Loop
    setInterval(() => {
      if (!gameOver) movePlayer();
    }, 16); // ~60fps

    setInterval(() => {
      if (!gameOver) createBlock();
    }, 1000); // Drop a new block every second
  </script>
</body>
</html>
