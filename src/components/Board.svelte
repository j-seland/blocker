<script>
import { onMount } from "svelte";
import Info from '../components/Info.svelte';

const BOX = 40;
const BOARD = 500;
const BOARDOFFSET = 20;
const BOARDCENTER = BOARD / 2;
const WARNING = 10;

const topBoundary = BOARDOFFSET;
const leftBoundary = BOARDOFFSET;
const bottomBoundary = BOARDOFFSET + BOARD;
const rightBoundary = BOARDOFFSET + BOARD;

const soundHitBoundary = new Audio('/audio/boundary.mp3');
const soundGameOver = new Audio('/audio/gameover.mp3');

let tracker = { x: 0, y: 0, top:0, left:0, bottom:0, right:0 };
let detinfo = false;

let gameover    = false;
let enemyxdir   = [1,1,1,1]; // used in moveEnemy
let enemyydir   = [1,1,1,1]; // used in moveEnemy
let speed       = 0;         // used in moveEnemies
let gamecounter = 0;         // used in moveEnemies
let gameseconds = 0;
let gameticker;

function init() {
  const el = document.querySelector('#box');
  el.style.top = `${BOARDCENTER}px`;
  el.style.left = `${BOARDCENTER}px`;
}

function gameTick() {
  gameseconds++;
}

function startClock() {
  gameticker = setInterval(gameTick, 1000);
}

function gameOver() {
  document.querySelector('#box').removeEventListener('mousedown', handleMousedown);
  document.querySelector('.boxface').innerHTML = '☹️';
  soundGameOver.play();
  gameover = true;
  clearInterval(gameticker);
}

function checkBoundary() {
  const el = document.querySelector('.gameboard');
  // check if inside boundary
  if (tracker.top - WARNING < topBoundary || tracker.left - WARNING < leftBoundary || tracker.bottom + WARNING > bottomBoundary || tracker.right + WARNING > rightBoundary) {
    el.style.borderColor = 'red';
    soundHitBoundary.play();
  } else {
    el.style.borderColor = 'black';
  }
  // check if outside boundary
  if (tracker.top < topBoundary || tracker.left < leftBoundary || tracker.bottom > bottomBoundary || tracker.right > rightBoundary) {
    gameOver();
  }
}

function setNewpos(id, xpos, ypos)	{
  document.getElementById(id).style.top = `${ypos}px`;
  document.getElementById(id).style.left = `${xpos}px`;
}

function hitDetection(num) {
  let r1 = document.querySelector('#box').getBoundingClientRect();
  let r2 = document.querySelector(`#enemy${num}`).getBoundingClientRect();
  let r1W = BOX;
  let r1H = BOX;
  let r2W = r2.right - r2.left;
  let r2H = r2.bottom - r2.top;
  // AABB Axis-Aligned Bounding Box Hit Detection
  if (r1.x < r2.x + r2W && r1.x + r1W > r2.x && r1.y < r2.y + r2H && r1.y + r1H > r2.y) {
    gameOver();
  }
}

function moveEnemy(num, step_x, step_y) {
  let top, left, x, y, newposx, newposy;
  let r2 = document.querySelector(`#enemy${num}`).getBoundingClientRect();

  top = r2.top;
  left = r2.left;
  x = r2.right - left;
  y = r2.bottom - top;

  if (top >= (BOARD - y) || top <= BOARDOFFSET) {
    enemyydir[num] = -1 * enemyydir[num];
  }
  newposy = top + (step_y * enemyydir[num]);

  if (left >= (BOARD - x) || left <= BOARDOFFSET) {
    enemyxdir[num] = -1 * enemyxdir[num];
  }
  newposx = left + (step_x * enemyxdir[num]);

  setNewpos(`enemy${num}`, newposx, newposy);

  hitDetection(num);
}

function moveEnemies() {
  gamecounter++;
  if (gamecounter >= 0 && gamecounter < 100) {
    speed = 80;
  } else if (gamecounter >= 100 &&  gamecounter < 200) {
    speed = 60;
  } else if (gamecounter >= 200 &&  gamecounter < 300) {
    speed = 40;
  } else if (gamecounter >= 300 &&  gamecounter < 400) {
    speed = 30;
  } else if (gamecounter >= 400 &&  gamecounter < 500) {
    speed = 20;
  } else {
    speed = 10;
  }
  if (!gameover) {
    moveEnemy(0, -10, 12);
    moveEnemy(1, -12, -20);
    moveEnemy(2, 15, -13);
    moveEnemy(3, 17, 11);
    setTimeout(moveEnemies, speed);
  }
}

function handleMousedown(e) {
  const el = document.querySelector('#box');

  window.addEventListener('mousemove', handleMousemove);
  window.addEventListener('mouseup', handleMouseup);

  if (!gameover) {
    startClock();
    moveEnemies();
  }

  tracker.x = e.clientX;
  tracker.y = e.clientY;

  function handleMousemove(e) {
    let newX = tracker.x - e.clientX;
    let newY = tracker.y - e.clientY;
    let r1 = el.getBoundingClientRect();
    el.style.left = `${r1.left - newX}px`;
    el.style.top = `${r1.top - newY}px`;
    tracker.x = e.clientX;
    tracker.y = e.clientY;
    tracker.top = r1.top;
    tracker.left = r1.left;
    tracker.bottom = r1.bottom;
    tracker.right = r1.right;
    checkBoundary();
  }

  function handleMouseup(e) {
    window.removeEventListener('mousemove', handleMousemove);
    window.removeEventListener('mouseup', handleMouseup);
    gameOver();
  }
}

onMount(() => {
  init();
})
</script>

<style>
#box {
  position: absolute;
  width: 40px;
  height: 40px;
  /*background-color: red;*/
}

.boxface {
  font-size: 32px;
  cursor: crosshair;
}

#enemy0 {
  background-color: #000099;
  position: absolute;
  top: calc(var(--board-offset) + 60px);
  left: calc(var(--board-offset) + 270px);
  width: 60px;
  height: 50px;
}

#enemy1 {
  background-color: #009900;
  position: absolute;
  top: calc(var(--board-offset) + 330px);
  left: calc(var(--board-offset) + 300px);
  width: 100px;
  height: 20px;
}

#enemy2 {
  background-color: #990099;
  position: absolute;
  top: calc(var(--board-offset) + 320px);
  left: calc(var(--board-offset) + 70px);
  width: 30px;
  height: 60px;
}

#enemy3 {
  background-color: #aabb00;
  position: absolute;
  top: calc(var(--board-offset) + 70px);
  left: calc(var(--board-offset) + 70px);
  width: 60px;
  height: 60px;
}

.gameboard {
  box-sizing: border-box;
  position: absolute;
  top: 20px;
  left: 20px;
  width: 500px;
  height: 500px;
  border: 5px solid black;
}

.scoreboard {
  position: absolute;
  top: 540px;
  left: 20px;
}

.gameover {
  font-size: 1.5rem;
  font-weight: bold;
  color: #ff0000;
}

.playing {
  font-size: 1.5rem;
  font-weight: bold;
  color: #009900;
}
</style>

<div class="gameboard"></div>
<div id="enemy0"></div>
<div id="enemy1"></div>
<div id="enemy2"></div>
<div id="enemy3"></div>
<div id="box" on:mousedown={handleMousedown}><span class="boxface">😃</span></div>
<div class="scoreboard">
  <p>Drag the 😃, avoid the colored rectangles and don't cross the boundary.</p>
  <p>{#if gameover}<span class="gameover">GAMEOVER</span>{:else}<span class="playing">PLAYING</span>{/if}</p>
  <h2>Counter: {gamecounter} Seconds: {gameseconds}</h2>
  <button on:click={() => {document.location.reload()}}>Game Reset</button>
  <label>
  	<input type=checkbox bind:checked={detinfo}> Detailed Information
  </label>
</div>

{#if detinfo}
  <Info>{JSON.stringify(tracker, null, 2)}</Info>
{/if}
