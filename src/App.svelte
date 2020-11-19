<script>
  let gameStats = {
    level: 0,
    lives: 3,
    score: 0,
  };

  let blocks = {
    green: 1,
    blue: 2,
    red: 3,
    grey: 4,
  };

  let gameActive = false;

  let ball = {
    posX: 0,
    posY: 0,
    speed: 0,
    smash: false,
  };

  let paddle = {
    posX: 0,
    width: 0,
    sticky: false,
  };

  const movePaddle = (e) => {
    if (e.layerX > 50 && e.layerX < 650) {
      paddle.posX = e.layerX - 50;
    }
    const el = document.getElementsByClassName("paddle")[0];
    el.style.left = paddle.posX + "px";

    if (!gameActive) {
      const el2 = document.getElementsByClassName("ball")[0];
      el2.style.left = paddle.posX + "px";
      el2.style.bottom = "30px";
    }
  };

  const initGame = () => {
    gameActive = !gameActive;
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
    border: 1px solid black;
    margin: 0 auto;
    position: relative;
  }

  .info-panel {
    display: flex;
    text-align: right;
    background-color: aliceblue;
    padding: 10px;
  }

  .ball {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: rgb(255, 0, 0);
    position: absolute;
  }

  .paddle {
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
    <p>Level: {gameStats.level}</p>
    <p>Lives: {gameStats.lives}</p>
    <p>Score: {gameStats.score}</p>
  </div>
  <div class="ball" />
  <div class="paddle" style="left:{paddle.posX}px" />
</main>
