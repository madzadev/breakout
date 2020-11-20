<script>
  let game = {
    level: 0,
    lives: 3,
    score: 0,
    active: false,
  };

  let bricks = {
    green: 1,
    blue: 2,
    red: 3,
    grey: 4,
    hit: false,
  };

  let ball = {
    posX: 340,
    posY: 30,
    speed: 0,
    smash: false,
  };

  let paddle = {
    posX: 300,
    sticky: false,
  };

  const movePaddle = (e) => {
    if (e.layerX >= 50 && e.layerX <= 650) {
      paddle.posX = e.layerX - 50;
    }

    if (!game.active) {
      ball.posX = paddle.posX + 40;
    }
  };

  const gameOver = (bX, bY, pX) => {
    if (bY < 30 && (bX < pX || bX > pX + 100)) {
      console.log("Game over");
    }
  };

  const initGame = () => {
    console.log("game initiated");
    game.active = !game.active;
    if (game.active) {
      let up = 5;
      let right = 1;
      setInterval(() => {
        if (ball.posY > 350 || ball.posY < 0) {
          up = -up;
        }
        if (ball.posX > 680 || ball.posX < 0) {
          right = -right;
        }
        ball.posY += up;
        ball.posX += right;

        gameOver(ball.posX, ball.posY, paddle.posX);
      }, 20);
    } else {
      console.log("game stopped");
    }
  };
</script>

<style>
  h1 {
    text-align: center;
    margin: 20px 0;
  }

  main {
    width: 700px;
    height: 400px;
    border: 1px solid rgb(114, 114, 114);
    margin: 0 auto;
    position: relative;
  }

  .info-panel {
    display: flex;
    text-align: right;
    height: 30px;
    background-color: rgb(44, 44, 44);
    color: white;
    padding: 10px;
  }

  #ball {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: rgb(255, 0, 0);
    position: absolute;
  }

  #paddle {
    width: 100px;
    height: 30px;
    background-color: rgb(0, 150, 236);
    position: absolute;
    bottom: 0;
  }
</style>

<h1>Breakout game</h1>
<main on:mousemove={movePaddle} on:click={initGame}>
  <div class="info-panel">
    <p>Level: {game.level}</p>
    <p>Lives: {game.lives}</p>
    <p>Score: {game.score}</p>
  </div>
  <div id="ball" style="left:{ball.posX}px; bottom:{ball.posY}px" />
  <div id="paddle" style="left:{paddle.posX}px" />
</main>
