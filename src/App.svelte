<script>
  import { onMount } from "svelte";

  let game = {
    level: 1,
    lives: 3,
    score: 0,
    active: false,
    message: "",
  };

  let bricks = {
    count: 0,
    green: {
      color: "green",
      hits: 1,
    },
    green: 1,
    blue: 2,
    red: 3,
    grey: 4,
    hit: false,
  };

  let ball = {
    x: 490,
    y: 32,
    width: 20,
    height: 20,
    speed: 0,
    smash: false,
  };

  let paddle = {
    x: 400,
    sticky: false,
  };

  const movePaddle = (e) => {
    if (e.layerX >= 100 && e.layerX <= 900) {
      paddle.x = e.layerX - 100;
    }

    if (!game.active) {
      ball.x = paddle.x + 90;
    }
  };

  const reset = () => {
    game.message = "";
    ball.x = 490;
    ball.y = 32;
    paddle.x = 400;
  };

  const isCollide = (a, b) => {
    return !(
      a.y + a.height < b.y ||
      a.y > b.y + b.height ||
      a.x + a.width < b.x ||
      a.x > b.x + b.width
    );
  };

  const gameOver = (bX, bY, pX, interval) => {
    if (bY < -20 && (bX < pX || bX > pX + 200)) {
      if (game.lives > 0) {
        --game.lives;
        game.message = "You lost 1 life ";
        game.active = !game.active;

        clearInterval(interval);

        setTimeout(() => {
          game.message = "Click to reset";

          ball.y = 32;
          ball.x = paddle.x + 90;
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

  let all;
  let bricksArray = [];
  onMount(() => {
    all = document.getElementsByClassName("brick");
    bricksArray = [];
    [...all].forEach((el, index) => {
      bricksArray.push({
        x: el.offsetLeft,
        y: 580 - el.offsetTop,
        height: el.clientHeight,
        width: el.clientWidth,
        destroyed: false,
      });
    });

    console.log(bricksArray);
  });

  const initGame = () => {
    game.active = !game.active;
    game.message = "";
    if (game.active) {
      let up = 8;
      let right = 1;
      const init = setInterval(() => {
        bricksArray.forEach((el, index) => {
          if (isCollide(ball, el) && !el.destroyed) {
            const all = document.getElementsByClassName("brick");
            all[index].style.backgroundColor = "transparent";
            el.destroyed = true;
            ++game.score;
            --bricks.count;

            if (bricks.count === 0) {
              game.message = "Congrats, you just won!";
              ++game.level;
              clearInterval(init);
              reset();
            }
            up = -up;
          }
        });

        if (
          ball.y > 530 ||
          ball.y < -50 ||
          (ball.y < 30 && ball.x + 20 > paddle.x && ball.x < paddle.x + 200)
        ) {
          up = -up;
        }
        if (ball.x > 980 || ball.x < 0) {
          right = -right;
        }

        ball.y += up;
        ball.x += right;

        gameOver(ball.x, ball.y, paddle.x, init);
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
    color: white;
  }
  main {
    width: 1000px;
    height: 600px;
    /* border: 3px solid rgb(255, 255, 255); */
    margin: 100px auto 0 auto;
    position: relative;
    overflow: hidden;
  }

  .info-panel {
    display: flex;
    text-align: right;
    height: 30px;
    /* background-color: rgb(44, 44, 44); */
    color: white;
    padding: 10px;
    gap: 32px;
    font-size: 20px;
    justify-content: flex-end;
    vertical-align: middle;
    align-items: center;
    font-weight: bold;
  }
  #brick-panel {
    padding: 80px;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 5px;
  }
  .brick {
    height: 20px;
    background-color: rgb(60, 238, 43);
    border-radius: 10px;
    /* border: 3px solid white; */
  }

  #ball {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: rgb(255, 96, 75);
    position: absolute;
  }

  #paddle {
    width: 200px;
    height: 20px;
    background-color: rgb(255, 255, 255);
    position: absolute;
    bottom: 10px;
    /* border: 3px solid white; */
    /* box-sizing: border-box; */
    border-radius: 10px;
  }

  #message {
    color: white;
    text-align: center;
    margin-top: 20px;
  }
</style>

<!-- <h1>=</h1> -->
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

  <div id="ball" style="left:{ball.x}px; bottom:{ball.y}px" />
  <div id="paddle" style="left:{paddle.x}px" />
</main>

<p id="message">{!game.message ? '' : game.message}</p>
