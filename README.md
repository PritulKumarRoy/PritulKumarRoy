<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>🐍 Classic Snake Game | GitHub README Animation</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: linear-gradient(145deg, #1a472a 0%, #0e2a1a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            font-family: 'Segoe UI', 'Courier New', 'Fira Code', monospace;
        }

        /* Game Card */
        .game-container {
            background: #0d2b1a;
            border-radius: 64px;
            padding: 20px 25px 25px 25px;
            box-shadow: 0 25px 40px rgba(0, 0, 0, 0.5), inset 0 1px 2px rgba(255, 255, 255, 0.1);
        }

        .game-panel {
            background: #000000cc;
            border-radius: 32px;
            padding: 20px;
            backdrop-filter: blur(2px);
        }

        canvas {
            display: block;
            margin: 0 auto;
            border-radius: 28px;
            box-shadow: 0 12px 28px rgba(0, 0, 0, 0.5), inset 0 0 0 2px #6fbf4c;
            background-color: #1e3a2f;
            cursor: pointer;
        }

        .info-bar {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            margin-top: 20px;
            margin-bottom: 10px;
            background: #000000aa;
            padding: 12px 24px;
            border-radius: 60px;
            backdrop-filter: blur(4px);
            font-weight: bold;
        }

        .score-box, .high-box {
            background: #010b04;
            padding: 6px 18px;
            border-radius: 40px;
            font-size: 1.6rem;
            letter-spacing: 1px;
            font-family: 'Courier New', monospace;
            color: #b9fbc0;
            text-shadow: 0 0 5px #2eff7a;
            box-shadow: inset 0 1px 3px #00000055, 0 2px 4px #00000033;
        }

        .score-box span, .high-box span {
            color: #ffd966;
            font-size: 1rem;
            margin-right: 8px;
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 20px;
            flex-wrap: wrap;
        }

        button {
            background: #2d2f36;
            border: none;
            font-size: 1.8rem;
            font-weight: bold;
            font-family: monospace;
            padding: 8px 20px;
            border-radius: 60px;
            color: #f0f0f0;
            cursor: pointer;
            transition: all 0.1s ease;
            box-shadow: 0 5px 0 #121212;
            transform: translateY(-2px);
        }

        button:active {
            transform: translateY(3px);
            box-shadow: 0 1px 0 #121212;
        }

        .restart-btn {
            background: #7a2e2e;
            color: #ffcf9a;
            box-shadow: 0 5px 0 #3e1a1a;
        }

        .status-text {
            text-align: center;
            font-family: 'Segoe UI', 'Courier New', monospace;
            font-weight: bold;
            font-size: 1.2rem;
            background: #00000099;
            padding: 8px 16px;
            border-radius: 40px;
            display: inline-block;
            margin-top: 12px;
            color: #f5e56b;
            letter-spacing: 1px;
        }

        .footer-note {
            text-align: center;
            margin-top: 15px;
            font-size: 0.75rem;
            color: #8bc34a;
            font-family: monospace;
        }

        @media (max-width: 550px) {
            .game-container {
                padding: 12px;
            }
            .score-box, .high-box {
                font-size: 1.2rem;
                padding: 4px 12px;
            }
            button {
                font-size: 1.3rem;
                padding: 5px 14px;
            }
        }
    </style>
</head>
<body>
<div>
    <div class="game-container">
        <div class="game-panel">
            <canvas id="snakeCanvas" width="500" height="500"></canvas>

            <div class="info-bar">
                <div class="score-box"><span>🍎 SCORE</span> <span id="scoreValue">0</span></div>
                <div class="high-box"><span>🏆 BEST</span> <span id="highScoreValue">0</span></div>
            </div>

            <div style="display: flex; justify-content: center;">
                <div class="status-text" id="gameStatusText">▶ PRESS SPACE / ARROWS</div>
            </div>

            <div class="controls">
                <button id="upBtn" aria-label="Up">▲ UP</button>
                <button id="leftBtn" aria-label="Left">◀ LEFT</button>
                <button id="downBtn" aria-label="Down">▼ DOWN</button>
                <button id="rightBtn" aria-label="Right">▶ RIGHT</button>
                <button id="restartBtn" class="restart-btn">🔄 RESTART</button>
            </div>
            <div class="footer-note">
                🐍 eat apples · grow · avoid walls & yourself
            </div>
        </div>
    </div>
</div>

<script>
    (function() {
        // ---------- CONFIG ----------
        const BOARD_SIZE = 20;       // 20x20 grid
        const CELL_SIZE = 25;        // 500px / 20 = 25px
        const canvas = document.getElementById('snakeCanvas');
        const ctx = canvas.getContext('2d');

        // Game state
        let snake = [];          // array of {x, y}
        let food = { x: 12, y: 10 };
        let direction = 'RIGHT';    // current moving direction
        let nextDirection = 'RIGHT';
        let score = 0;
        let highScore = 0;
        let gameLoopInterval = null;
        let isGameRunning = true;     // true = playing, false = game over
        let animationFrame = null;
        
        // speed (ms per move)
        const BASE_SPEED = 140;
        let currentSpeed = BASE_SPEED;
        
        // load highscore from localStorage
        try {
            const saved = localStorage.getItem('snakeHighScoreGit');
            if (saved !== null && !isNaN(parseInt(saved))) {
                highScore = parseInt(saved);
                document.getElementById('highScoreValue').innerText = highScore;
            }
        } catch(e) { /* silent */ }

        // DOM elements
        const scoreSpan = document.getElementById('scoreValue');
        const highScoreSpan = document.getElementById('highScoreValue');
        const statusDiv = document.getElementById('gameStatusText');
        
        // ---------- helper functions ----------
        function updateUI() {
            scoreSpan.innerText = score;
            if (score > highScore) {
                highScore = score;
                highScoreSpan.innerText = highScore;
                // persist best score
                try {
                    localStorage.setItem('snakeHighScoreGit', highScore);
                } catch(e) {}
            }
        }
        
        // reset game fully
        function initGame() {
            // classic snake: length 3, horizontal
            snake = [
                { x: 10, y: 10 },
                { x: 9, y: 10 },
                { x: 8, y: 10 }
            ];
            direction = 'RIGHT';
            nextDirection = 'RIGHT';
            score = 0;
            isGameRunning = true;
            currentSpeed = BASE_SPEED;
            updateUI();
            statusDiv.innerText = '🐍 GAME ACTIVE · PLAY!';
            statusDiv.style.color = '#b9ffb0';
            generateValidFood();  // ensures food not on snake
            clearGameInterval();
            startGameInterval();
            draw(); // immediate visual
        }
        
        // generate food not colliding with snake
        function generateValidFood() {
            const totalCells = BOARD_SIZE * BOARD_SIZE;
            if (snake.length >= totalCells) {
                // you win condition - but in classic snake we just restart? Actually win = game win
                // we'll handle as game win scenario
                gameWin();
                return;
            }
            
            // fast method: collect free cells
            const snakeSet = new Set(snake.map(seg => `${seg.x},${seg.y}`));
            let freeCells = [];
            for (let i = 0; i < BOARD_SIZE; i++) {
                for (let j = 0; j < BOARD_SIZE; j++) {
                    if (!snakeSet.has(`${i},${j}`)) {
                        freeCells.push({ x: i, y: j });
                    }
                }
            }
            if (freeCells.length === 0) {
                gameWin();
                return;
            }
            const randomIndex = Math.floor(Math.random() * freeCells.length);
            food = freeCells[randomIndex];
        }
        
        // game win celebration
        function gameWin() {
            if (!isGameRunning) return;
            isGameRunning = false;
            clearGameInterval();
            statusDiv.innerText = '✨ YOU WIN! PERFECT! ✨  (press RESTART)';
            statusDiv.style.color = '#f9f06e';
            draw();  // show final board
        }
        
        // collision detection (wall or self)
        function isCollision(head, snakeArray) {
            // wall collision
            if (head.x < 0 || head.x >= BOARD_SIZE || head.y < 0 || head.y >= BOARD_SIZE) {
                return true;
            }
            // self collision (skip the first segment? careful: we check head vs body)
            for (let i = 0; i < snakeArray.length; i++) {
                if (snakeArray[i].x === head.x && snakeArray[i].y === head.y) {
                    return true;
                }
            }
            return false;
        }
        
        // core game step
        function gameStep() {
            if (!isGameRunning) return;
            
            // commit queued direction
            direction = nextDirection;
            
            // compute new head
            let newHead = { ...snake[0] };
            switch (direction) {
                case 'RIGHT': newHead.x += 1; break;
                case 'LEFT':  newHead.x -= 1; break;
                case 'UP':    newHead.y -= 1; break;
                case 'DOWN':  newHead.y += 1; break;
                default: break;
            }
            
            // check if food is eaten
            const willEat = (newHead.x === food.x && newHead.y === food.y);
            
            // perform move
            let newSnake = [newHead, ...snake];
            if (!willEat) {
                newSnake.pop();   // remove tail if not eating
            }
            
            // collision detection after move
            if (isCollision(newHead, willEat ? newSnake : newSnake.slice(1))) { 
                // game over
                gameOver();
                return;
            }
            
            // apply new snake
            snake = newSnake;
            
            // handle eating
            if (willEat) {
                score++;
                updateUI();
                // increase speed slightly but keep min 70ms (max speed)
                let newSpeed = Math.max(70, BASE_SPEED - Math.floor(score / 6) * 4);
                if (newSpeed !== currentSpeed) {
                    currentSpeed = newSpeed;
                    clearGameInterval();
                    startGameInterval();
                }
                generateValidFood();  // new food, also checks win condition if board full
                if (!isGameRunning) return; // if gameWin triggered inside generateValidFood
            }
            
            draw(); // re-render after each step
        }
        
        function gameOver() {
            if (!isGameRunning) return;
            isGameRunning = false;
            clearGameInterval();
            statusDiv.innerText = '💀 GAME OVER · press RESTART 💀';
            statusDiv.style.color = '#ffaa88';
            draw();  // show final freeze frame
        }
        
        function clearGameInterval() {
            if (gameLoopInterval) {
                clearInterval(gameLoopInterval);
                gameLoopInterval = null;
            }
        }
        
        function startGameInterval() {
            if (gameLoopInterval) clearGameInterval();
            if (isGameRunning) {
                gameLoopInterval = setInterval(() => gameStep(), currentSpeed);
            }
        }
        
        // restart from UI
        function restartGame() {
            clearGameInterval();
            initGame();
        }
        
        // change direction with validation (prevent 180deg)
        function setDirection(newDir) {
            if (!isGameRunning) return;
            const opposite = {
                'UP': 'DOWN', 'DOWN': 'UP', 'LEFT': 'RIGHT', 'RIGHT': 'LEFT'
            };
            if (opposite[newDir] !== direction) {
                nextDirection = newDir;
            }
        }
        
        // ---------- DRAWING (retro pixel style + smooth animations) ----------
        function draw() {
            if (!canvas || !ctx) return;
            ctx.clearRect(0, 0, 500, 500);
            
            // draw background grid (soft grid lines)
            for (let i = 0; i <= BOARD_SIZE; i++) {
                ctx.beginPath();
                ctx.lineWidth = 0.6;
                ctx.strokeStyle = '#2c5a3e';
                ctx.moveTo(i * CELL_SIZE, 0);
                ctx.lineTo(i * CELL_SIZE, canvas.height);
                ctx.stroke();
                ctx.moveTo(0, i * CELL_SIZE);
                ctx.lineTo(canvas.width, i * CELL_SIZE);
                ctx.stroke();
            }
            
            // draw food (shiny apple)
            ctx.shadowBlur = 0;
            const fx = food.x * CELL_SIZE;
            const fy = food.y * CELL_SIZE;
            ctx.fillStyle = '#ff3b2f';
            ctx.beginPath();
            ctx.arc(fx + CELL_SIZE/2, fy + CELL_SIZE/2, CELL_SIZE/2 - 2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = '#ffd966';
            ctx.beginPath();
            ctx.arc(fx + CELL_SIZE/2 - 3, fy + CELL_SIZE/2 - 4, 3, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = '#6b2e00';
            ctx.beginPath();
            ctx.fillRect(fx + CELL_SIZE/2 - 1, fy + 4, 2, 6);
            ctx.fill();
            // leaf
            ctx.fillStyle = '#7ed957';
            ctx.beginPath();
            ctx.ellipse(fx + CELL_SIZE - 7, fy + 5, 4, 6, 0.5, 0, Math.PI*2);
            ctx.fill();
            
            // draw snake with gradient body
            for (let i = 0; i < snake.length; i++) {
                const seg = snake[i];
                const sx = seg.x * CELL_SIZE;
                const sy = seg.y * CELL_SIZE;
                const padding = i === 0 ? 2 : 3;
                const radius = 6;
                ctx.fillStyle = i === 0 ? '#6fef6a' : '#2ea84c';
                ctx.shadowBlur = 2;
                ctx.shadowColor = '#00000040';
                ctx.beginPath();
                ctx.roundRect(sx + 2, sy + 2, CELL_SIZE - 4, CELL_SIZE - 4, radius);
                ctx.fill();
                // eyes on head
                if (i === 0) {
                    ctx.fillStyle = '#ffffff';
                    ctx.beginPath();
                    ctx.arc(sx + CELL_SIZE - 8, sy + 8, 3, 0, Math.PI*2);
                    ctx.arc(sx + 8, sy + 8, 3, 0, Math.PI*2);
                    ctx.fill();
                    ctx.fillStyle = '#030303';
                    ctx.beginPath();
                    ctx.arc(sx + CELL_SIZE - 7, sy + 7, 1.5, 0, Math.PI*2);
                    ctx.arc(sx + 9, sy + 7, 1.5, 0, Math.PI*2);
                    ctx.fill();
                    ctx.fillStyle = '#fcf4a9';
                    ctx.beginPath();
                    ctx.arc(sx + CELL_SIZE - 8, sy + 6, 1, 0, Math.PI*2);
                    ctx.arc(sx + 8, sy + 6, 1, 0, Math.PI*2);
                    ctx.fill();
                } else {
                    // little pattern
                    ctx.fillStyle = '#d9ffb0';
                    ctx.beginPath();
                    ctx.arc(sx + CELL_SIZE - 7, sy + CELL_SIZE/2, 2, 0, Math.PI*2);
                    ctx.fill();
                }
            }
            ctx.shadowBlur = 0;
            
            // if game over overlay
            if (!isGameRunning && gameLoopInterval === null && snake.length > 0) {
                ctx.globalAlpha = 0.75;
                ctx.fillStyle = '#0a0f0c';
                ctx.fillRect(0, 0, 500, 500);
                ctx.globalAlpha = 1;
                ctx.font = 'bold 22px "Courier New", monospace';
                ctx.fillStyle = '#ffc857';
                ctx.shadowBlur = 0;
                ctx.fillText('⚡ GAME OVER ⚡', 150, 260);
                ctx.font = '14px monospace';
                ctx.fillStyle = '#b3ffcf';
                ctx.fillText('click RESTART', 195, 320);
            } else if (snake.length === BOARD_SIZE * BOARD_SIZE && isGameRunning === false) {
                ctx.globalAlpha = 0.7;
                ctx.fillStyle = '#103a1f';
                ctx.fillRect(0, 0, 500, 500);
                ctx.globalAlpha = 1;
                ctx.font = 'bold 26px monospace';
                ctx.fillStyle = '#FFD966';
                ctx.fillText('🏆 PERFECT WIN!', 130, 270);
            }
        }
        
        // helper for rounded rect
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
        
        // ----- Keyboard & touch controls -----
        function handleKeyDown(e) {
            const key = e.key;
            e.preventDefault();
            switch (key) {
                case 'ArrowUp': setDirection('UP'); break;
                case 'ArrowDown': setDirection('DOWN'); break;
                case 'ArrowLeft': setDirection('LEFT'); break;
                case 'ArrowRight': setDirection('RIGHT'); break;
                case ' ': case 'Space': 
                    e.preventDefault();
                    if (!isGameRunning) restartGame();
                    break;
                default: break;
            }
        }
        
        // Buttons event handlers
        document.getElementById('upBtn').addEventListener('click', () => setDirection('UP'));
        document.getElementById('downBtn').addEventListener('click', () => setDirection('DOWN'));
        document.getElementById('leftBtn').addEventListener('click', () => setDirection('LEFT'));
        document.getElementById('rightBtn').addEventListener('click', () => setDirection('RIGHT'));
        document.getElementById('restartBtn').addEventListener('click', () => restartGame());
        
        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp' || e.key === 'ArrowDown' || e.key === 'ArrowLeft' || e.key === 'ArrowRight' || e.key === ' ' || e.key === 'Space') {
                e.preventDefault();
                handleKeyDown(e);
            }
        });
        
        // prevent touch context menu on canvas
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        canvas.addEventListener('touchstart', (e) => e.preventDefault());
        
        // start fresh game
        function bootstrap() {
            initGame();
            // extra animation loop just for smoothness, but draw already called each step
            // requestAnimationFrame for responsive redraw if needed
            function animFrame() {
                draw();
                requestAnimationFrame(animFrame);
            }
            animFrame(); // keep UI repainted (though steps call draw, but just safe)
        }
        
        bootstrap();
    })();
</script>
</body>
</html>
