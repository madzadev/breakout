<script>
  import { onMount } from "svelte";

  let game = {
    level: 1,
    lives: 3,
    score: 0,
    speed: 20,
    active: false,
    gameOver: false,
  };

  let ball = {
    x: 490,
    y: 32,
    width: 20,
    height: 20,
  };

  let paddle = {
    x: 400,
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
    game.active = !game.active;
    ball.x = paddle.x + 90;
    ball.y = 32;
  };

  const isCollide = (a, b) => {
    return !(
      a.y + a.height < b.y ||
      a.y > b.y + b.height ||
      a.x + a.width < b.x ||
      a.x > b.x + b.width
    );
  };

  const bounceAngle = (a, b, c) => {
    const l = b - a + 1;
    const p = (c * 100) / l;
    return p <= 20 ? -3 : p <= 40 ? -2 : p <= 60 ? 1 : p <= 80 ? 2 : 3;
  };

  const flashBricks = () => {
    console.log(bricksArray);
    let all = document.getElementsByClassName("brick");
    bricksArray.forEach((el, index) => {
      if (!el.destroyed) {
        let times = 0;
        all[index].style.backgroundColor = "tomato";
        let interval = setInterval(() => {
          if (times % 2 === 0) {
            all[index].style.backgroundColor = "rgb(60, 238, 43)";
          } else {
            all[index].style.backgroundColor = "tomato";
          }
          ++times;
          if (times === 5) {
            clearInterval(interval);
          }
        }, 100);
      }
    });
  };

  const gameOver = (bX, bY, pX, interval) => {
    if (bY < -20 && (bX < pX || bX > pX + 200)) {
      if (game.lives > 0) {
        --game.lives;
        clearInterval(interval);
        setTimeout(() => {
          flashBricks();
          reset();
        }, 0);
      } else {
        clearInterval(interval);
        game.gameOver = true;
      }
    }
  };

  const playAgain = () => {
    game.gameOver = false;
    game.level = 1;
    game.lives = 3;
    game.score = 0;
    game.speed = 20;
    setTimeout(() => {
      reset();
      nextLevel();
    }, 0);
  };

  let nextLevel = () => {
    bricksArray = [];
    let all = document.getElementsByClassName("brick");
    setTimeout(() => {
      [...all].forEach((el, index) => {
        all[index].style.backgroundColor = "rgb(60, 238, 43)";
        bricksArray.push({
          x: el.offsetLeft,
          y: 580 - el.offsetTop,
          height: el.clientHeight,
          width: el.clientWidth,
          destroyed: false,
        });
      });
    }, 0);
  };

  let bricksArray = [];
  onMount(() => {
    let all = document.getElementsByClassName("brick");
    [...all].forEach((el) => {
      bricksArray.push({
        x: el.offsetLeft,
        y: 580 - el.offsetTop,
        height: el.clientHeight,
        width: el.clientWidth,
        destroyed: false,
      });
    });
  });

  const initGame = () => {
    game.active = !game.active;
    if (game.active) {
      let up = 8;
      let right = 1;

      const init = setInterval(() => {
        let leftBricks = 0;
        bricksArray.forEach((el, index) => {
          if (isCollide(ball, el) && !el.destroyed) {
            const all = document.getElementsByClassName("brick");
            all[index].style.backgroundColor = "transparent";
            el.destroyed = true;
            ++game.score;
            up = -up;
          }
          if (!el.destroyed) {
            ++leftBricks;
          }
          if (leftBricks === 0 && index + 1 === bricksArray.length) {
            ++game.level;
            if (game.speed) {
              --game.speed;
            }
            setTimeout(() => {
              clearInterval(init);
              reset();
              nextLevel();
            }, 0);
          }
        });

        // bounce against the ceilings and floor
        if (ball.y > 530 || ball.y < -50) {
          up = -up;
        }

        // bounce against paddle
        if (ball.y < 30 && ball.x + 20 > paddle.x && ball.x < paddle.x + 200) {
          let res = bounceAngle(
            paddle.x,
            paddle.x + 200,
            ball.x + 10 - paddle.x
          );
          if (res === 1) {
            up = -up;
            right = right / Math.abs(right);
          } else {
            up = -up;
            right = 1 * res;
          }
        }

        // bounce against side walls
        if (ball.x > 980 || ball.x < 0) {
          right = -right;
        }

        ball.y += up;
        ball.x += right;

        gameOver(ball.x, ball.y, paddle.x, init);
      }, game.speed);
    }
  };
</script>

<style>
  main {
    width: 1000px;
    height: 600px;
    margin: 100px auto 0 auto;
    position: relative;
    overflow: hidden;
  }

  .info-panel {
    display: flex;
    text-align: right;
    height: 30px;
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
    gap: 5px;
  }
  .brick {
    height: 20px;
    background-color: rgb(60, 238, 43);
    border-radius: 10px;
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
    border-radius: 10px;
  }

  h1 {
    color: white;
    font-size: 60px;
    text-align: center;
  }
</style>

{#if !game.gameOver}
  <main on:mousemove={movePaddle} on:click={initGame}>
    <div class="info-panel">
      <p>Level: {game.level}</p>
      <p>Lives: {game.lives}</p>
      <p>Score: {game.score}</p>
    </div>
    <div
      id="brick-panel"
      style="grid-template-columns: repeat({game.level + 2}, 1fr);">
      {#each Array((game.level + 2) * 6) as _, i}
        <div class="brick" />
      {/each}
    </div>

    <div id="ball" style="left:{ball.x}px; bottom:{ball.y}px" />
    <div id="paddle" style="left:{paddle.x}px" />
  </main>
{:else}
  <main>
    <h1>Game Over</h1>
    <h1>Level: {game.level}, Points: {game.score}</h1>
    <button on:click={playAgain}>Play again!</button>
  </main>
{/if}
