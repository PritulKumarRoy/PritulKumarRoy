<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Snake Game Animation</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; user-select: none; }
  body {
    background: linear-gradient(135deg, #1a472a, #0a2a1a);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: 'Courier New', monospace;
    padding: 20px;
  }
  .game-wrapper {
    background: #0a1f12;
    border-radius: 48px;
    padding: 20px;
    box-shadow: 0 20px 35px rgba(0,0,0,0.5);
  }
  canvas {
    display: block;
    margin: 0 auto;
    border-radius: 20px;
    box-shadow: 0 0 0 3px #5a9e4b, 0 10px 25px black;
    background-color: #1e3a2f;
    cursor: pointer;
  }
  .info {
    display: flex;
    justify-content: space-between;
    margin-top: 15px;
    background: #000000aa;
    padding: 10px 20px;
    border-radius: 50px;
    backdrop-filter: blur(5px);
  }
  .score, .best {
    font-size: 1.5rem;
    font-weight: bold;
    color: #f5f9c6;
    text-shadow: 0 0 5px #6eff8e;
  }
  .score span, .best span {
    color: #ffb347;
    font-size: 0.9rem;
    margin-right: 8px;
  }
  .controls {
    display: flex;
    gap: 12px;
    justify-content: center;
    margin-top: 15px;
    flex-wrap: wrap;
  }
  button {
    background: #2c2f3a;
    border: none;
    font-size: 1.3rem;
    font-weight: bold;
    padding: 8px 18px;
    border-radius: 40px;
    color: white;
    cursor: pointer;
    font-family: monospace;
    box-shadow: 0 4px 0 #121212;
    transition: 0.05s linear;
  }
  button:active {
    transform: translateY(3px);
    box-shadow: 0 1px 0 #121212;
  }
  .restart {
    background: #8b3c2c;
    color: #ffdd99;
  }
  .status {
    text-align: center;
    margin-top: 12px;
    background: #00000099;
    display: inline-block;
    width: auto;
    padding: 6px 18px;
    border-radius: 60px;
    color: #d4ff78;
    font-weight: bold;
    font-size: 0.9rem;
  }
  .status-container {
    text-align: center;
  }
  @media (max-width: 550px) {
    .score, .best { font-size: 1.1rem; }
    button { font-size: 1rem; padding: 5px 12px; }
  }
</style>
</head>
<body>
<div>
<div class="game-wrapper">
  <canvas id="gameCanvas" width="500" height="500"></canvas>
  <div class="info">
    <div class="score"><span>🍎 SCORE</span> <span id="currentScore">0</span></div>
    <div class="best"><span>🏆 BEST</span> <span id="bestScore">0</span></div>
  </div>
  <div class="status-container">
    <div class="status" id="gameMsg">▶ PLAY WITH ARROWS / WASD</div>
  </div>
  <div class="controls">
    <button id="btnUp">▲ UP</button>
    <button id="btnLeft">◀ LEFT</button>
    <button id="btnDown">▼ DOWN</button>
    <button id="btnRight">▶ RIGHT</button>
    <button id="btnRestart" class="restart">🔄 RESTART</button>
  </div>
</div>
</div>

<script>
(function(){
  const SIZE = 20;
  const CELL = 25;
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  
  let snake = [
    {x: 10, y: 10},
    {x: 9, y: 10},
    {x: 8, y: 10}
  ];
  let food = {x: 15, y: 10};
  let dir = 'RIGHT';
  let nextDir = 'RIGHT';
  let score = 0;
  let best = 0;
  let running = true;
  let interval = null;
  let speed = 130;
  
  try {
    let saved = localStorage.getItem('snake_best_git');
    if(saved) best = parseInt(saved);
    document.getElementById('bestScore').innerText = best;
  } catch(e) {}
  
  function saveBest() {
    if(score > best) {
      best = score;
      document.getElementById('bestScore').innerText = best;
      try { localStorage.setItem('snake_best_git', best); } catch(e) {}
    }
  }
  
  function updateScoreUI() {
    document.getElementById('currentScore').innerText = score;
    if(score > best) saveBest();
  }
  
  function randomFood() {
    let free = [];
    for(let i=0; i<SIZE; i++) {
      for(let j=0; j<SIZE; j++) {
        let occupied = snake.some(s => s.x === i && s.y === j);
        if(!occupied) free.push({x:i, y:j});
      }
    }
    if(free.length === 0) {
      if(running) winGame();
      return;
    }
    let rand = Math.floor(Math.random() * free.length);
    food = free[rand];
  }
  
  function winGame() {
    if(!running) return;
    running = false;
    if(interval) clearInterval(interval);
    interval = null;
    document.getElementById('gameMsg').innerHTML = '🎉 PERFECT WIN! YOU BEAT THE GRID! 🎉';
    document.getElementById('gameMsg').style.color = '#ffd966';
    draw();
  }
  
  function gameOver() {
    if(!running) return;
    running = false;
    if(interval) clearInterval(interval);
    interval = null;
    document.getElementById('gameMsg').innerHTML = '💀 GAME OVER · PRESS RESTART 💀';
    document.getElementById('gameMsg').style.color = '#ffaa88';
    draw();
  }
  
  function step() {
    if(!running) return;
    dir = nextDir;
    
    let head = {...snake[0]};
    switch(dir) {
      case 'RIGHT': head.x += 1; break;
      case 'LEFT': head.x -= 1; break;
      case 'UP': head.y -= 1; break;
      case 'DOWN': head.y += 1; break;
    }
    
    let willEat = (head.x === food.x && head.y === food.y);
    let newSnake = [head, ...snake];
    if(!willEat) newSnake.pop();
    
    let collide = false;
    if(head.x < 0 || head.x >= SIZE || head.y < 0 || head.y >= SIZE) collide = true;
    for(let i=1; i<newSnake.length; i++) {
      if(newSnake[i].x === head.x && newSnake[i].y === head.y) { collide = true; break; }
    }
    
    if(collide) {
      gameOver();
      draw();
      return;
    }
    
    snake = newSnake;
    if(willEat) {
      score++;
      updateScoreUI();
      let newSpeed = Math.max(70, 130 - Math.floor(score/7)*4);
      if(newSpeed !== speed) {
        speed = newSpeed;
        if(interval) {
          clearInterval(interval);
          interval = setInterval(step, speed);
        }
      }
      randomFood();
      if(!running) return;
    }
    draw();
  }
  
  function startLoop() {
    if(interval) clearInterval(interval);
    interval = setInterval(step, speed);
  }
  
  function resetGame() {
    if(interval) clearInterval(interval);
    snake = [
      {x: 10, y: 10},
      {x: 9, y: 10},
      {x: 8, y: 10}
    ];
    dir = 'RIGHT';
    nextDir = 'RIGHT';
    score = 0;
    running = true;
    speed = 130;
    updateScoreUI();
    document.getElementById('gameMsg').innerHTML = '🐍 GAME ACTIVE · USE ARROWS';
    document.getElementById('gameMsg').style.color = '#d4ff78';
    randomFood();
    startLoop();
    draw();
  }
  
  function changeDirection(newDir) {
    if(!running) return;
    const opposite = { 'UP': 'DOWN', 'DOWN': 'UP', 'LEFT': 'RIGHT', 'RIGHT': 'LEFT' };
    if(opposite[newDir] !== dir) {
      nextDir = newDir;
    }
  }
  
  function draw() {
    ctx.clearRect(0, 0, 500, 500);
    
    // grid
    ctx.strokeStyle = '#2f6b47';
    ctx.lineWidth = 0.5;
    for(let i=0; i<=SIZE; i++) {
      ctx.beginPath();
      ctx.moveTo(i*CELL, 0);
      ctx.lineTo(i*CELL, 500);
      ctx.stroke();
      ctx.moveTo(0, i*CELL);
      ctx.lineTo(500, i*CELL);
      ctx.stroke();
    }
    
    // draw food
    ctx.shadowBlur = 0;
    ctx.fillStyle = '#e74c3c';
    ctx.beginPath();
    ctx.arc(food.x*CELL + CELL/2, food.y*CELL + CELL/2, CELL/2 - 3, 0, Math.PI*2);
    ctx.fill();
    ctx.fillStyle = '#f1c40f';
    ctx.beginPath();
    ctx.arc(food.x*CELL + CELL/2 - 3, food.y*CELL + CELL/2 - 3, 3, 0, Math.PI*2);
    ctx.fill();
    ctx.fillStyle = '#2c3e2f';
    ctx.fillRect(food.x*CELL + CELL/2 - 1, food.y*CELL + 4, 2, 6);
    
    // draw snake
    for(let i=0; i<snake.length; i++) {
      let s = snake[i];
      let grad = ctx.createLinearGradient(s.x*CELL, s.y*CELL, s.x*CELL+CELL, s.y*CELL+CELL);
      if(i===0) {
        grad.addColorStop(0, '#8eff6e');
        grad.addColorStop(1, '#4cd964');
      } else {
        grad.addColorStop(0, '#3aa84a');
        grad.addColorStop(1, '#248f3c');
      }
      ctx.fillStyle = grad;
      ctx.shadowBlur = 3;
      ctx.shadowColor = '#00000060';
      ctx.beginPath();
      ctx.roundRect(s.x*CELL+2, s.y*CELL+2, CELL-4, CELL-4, 7);
      ctx.fill();
      if(i===0) {
        ctx.fillStyle = 'white';
        ctx.beginPath();
        ctx.arc(s.x*CELL+CELL-8, s.y*CELL+8, 3, 0, Math.PI*2);
        ctx.arc(s.x*CELL+8, s.y*CELL+8, 3, 0, Math.PI*2);
        ctx.fill();
        ctx.fillStyle = '#1a2a1a';
        ctx.beginPath();
        ctx.arc(s.x*CELL+CELL-7, s.y*CELL+7, 1.8, 0, Math.PI*2);
        ctx.arc(s.x*CELL+9, s.y*CELL+7, 1.8, 0, Math.PI*2);
        ctx.fill();
      }
    }
    ctx.shadowBlur = 0;
    
    if(!running && interval === null) {
      ctx.globalAlpha = 0.75;
      ctx.fillStyle = '#071a0e';
      ctx.fillRect(0, 0, 500, 500);
      ctx.globalAlpha = 1;
      ctx.font = 'bold 26px "Courier New", monospace';
      ctx.fillStyle = '#ffb347';
      ctx.fillText('☠️ GAME OVER ☠️', 140, 260);
      ctx.font = '14px monospace';
      ctx.fillStyle = '#b0f7b0';
      ctx.fillText('press RESTART', 195, 320);
    }
  }
  
  if (!CanvasRenderingContext2D.prototype.roundRect) {
    CanvasRenderingContext2D.prototype.roundRect = function(x, y, w, h, r) {
      if (w < 2 * r) r = w / 2;
      if (h < 2 * r) r = h / 2;
      this.moveTo(x+r, y);
      this.lineTo(x+w-r, y);
      this.quadraticCurveTo(x+w, y, x+w, y+r);
      this.lineTo(x+w, y+h-r);
      this.quadraticCurveTo(x+w, y+h, x+w-r, y+h);
      this.lineTo(x+r, y+h);
      this.quadraticCurveTo(x, y+h, x, y+h-r);
      this.lineTo(x, y+r);
      this.quadraticCurveTo(x, y, x+r, y);
      return this;
    };
  }
  
  window.addEventListener('keydown', (e) => {
    let key = e.key;
    if(key === 'ArrowUp' || key === 'w' || key === 'W') { e.preventDefault(); changeDirection('UP'); }
    else if(key === 'ArrowDown' || key === 's' || key === 'S') { e.preventDefault(); changeDirection('DOWN'); }
    else if(key === 'ArrowLeft' || key === 'a' || key === 'A') { e.preventDefault(); changeDirection('LEFT'); }
    else if(key === 'ArrowRight' || key === 'd' || key === 'D') { e.preventDefault(); changeDirection('RIGHT'); }
    else if(key === ' ' || key === 'Space') { e.preventDefault(); if(!running) resetGame(); }
  });
  
  document.getElementById('btnUp').addEventListener('click', () => changeDirection('UP'));
  document.getElementById('btnDown').addEventListener('click', () => changeDirection('DOWN'));
  document.getElementById('btnLeft').addEventListener('click', () => changeDirection('LEFT'));
  document.getElementById('btnRight').addEventListener('click', () => changeDirection('RIGHT'));
  document.getElementById('btnRestart').addEventListener('click', () => resetGame());
  
  randomFood();
  startLoop();
  draw();
})();
</script>
</body>
</html>
