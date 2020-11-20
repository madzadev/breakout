<script>
  let game = {
    level: 0,
    lives: 3,
    score: 0,
    active: false,
    message: "",
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

  const reset = () => {
    game.message = "";
    ball.posX = 340;
    ball.posY = 30;
    paddle.posX = 300;
  };

  const gameOver = (bX, bY, pX, interval) => {
    if (bY < -20 && (bX < pX || bX > pX + 100)) {
      if (game.lives > 0) {
        --game.lives;
        game.message = "You lost 1 life ";
        game.active = !game.active;

        clearInterval(interval);

        setTimeout(() => {
          game.message = "Click to reset";

          ball.posY = 30;
          ball.posX = paddle.posX + 40;
        }, 1000);
      } else {
        game.message = "You lost the game!";
        game.active = !game.active;
        clearInterval(interval);
        setTimeout(() => {
          reset();
        }, 1000);
      }
    }
  };

  const initGame = () => {
    console.log("game initiated");
    game.active = !game.active;
    game.message = "";
    if (game.active) {
      let up = 8;
      let right = 1;
      const init = setInterval(() => {
        if (
          ball.posY > 530 ||
          ball.posY < -50 ||
          (ball.posY < 30 &&
            ball.posX > paddle.posX &&
            ball.posX < paddle.posX + 100)
        ) {
          up = -up;
        }
        if (ball.posX > 680 || ball.posX < 0) {
          right = -right;
        }
        ball.posY += up;
        ball.posX += right;

        gameOver(ball.posX, ball.posY, paddle.posX, init);
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
    height: 600px;
    border: 1px solid rgb(114, 114, 114);
    margin: 0 auto;
    position: relative;
    overflow: hidden;
  }

  .info-panel {
    display: flex;
    text-align: right;
    height: 30px;
    background-color: rgb(44, 44, 44);
    color: white;
    padding: 10px;
    /* flex-wrap: wrap; */
    gap: 32px;
    justify-content: flex-end;
    vertical-align: middle;
    align-items: center;
  }
  #brick-panel {
    /* margin-top: 10px; */
    padding: 10px;
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    /* grid-template-rows: repeat(3, 20px); */
    gap: 10px;
  }
  .brick {
    /* width: 100px; */
    height: 20px;
    background-color: rgb(0, 104, 17);
    /* margin: 10px; */
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
    height: 20px;
    background-color: rgb(0, 150, 236);
    position: absolute;
    bottom: 10px;
  }
</style>

<h1>Breakout game</h1>
<main on:mousemove={movePaddle} on:click={initGame}>
  <div class="info-panel">
    <p>Level: {game.level}</p>
    <p>Lives: {game.lives}</p>
    <p>Score: {game.score}</p>
  </div>
  <div id="brick-panel">
    {#each Array(18) as _, i}
      <div class="brick" />
    {/each}
  </div>

  <div id="ball" style="left:{ball.posX}px; bottom:{ball.posY}px" />
  <div id="paddle" style="left:{paddle.posX}px" />
</main>

<!-- <button on:click={reset}>Reset</button> -->
<h1>{!game.message ? '' : game.message}</h1>
