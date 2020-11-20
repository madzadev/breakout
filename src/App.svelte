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

  const initGame = () => {
    game.active = !game.active;
    if (game.active) {
      let current = 30;
      let up = true;
      setInterval(() => {
        console.log("this moves");
        if (up) {
          current += 5;
        } else {
          current -= 5;
        }

        if (current > 335) {
          up = false;
        } else if (current < 35) {
          up = true;
        }

        const el2 = document.getElementsByClassName("ball")[0];
        el2.style.bottom = current + "px";
      }, 15);
    }
    console.log("game initiated");
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
    background-color: aliceblue;
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
