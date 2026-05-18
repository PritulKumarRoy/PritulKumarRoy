<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pac-Man Repository Explorer</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;
            background: #ffffff;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .game-container {
            width: 100%;
            max-width: 800px;
        }

        .game-wrapper {
            background: #ffffff;
            border: 1px solid #e1e4e8;
            border-radius: 6px;
            overflow: hidden;
            box-shadow: 0 1px 3px rgba(27, 31, 35, 0.12), 0 8px 24px rgba(66, 82, 110, 0.12);
        }

        canvas {
            display: block;
            width: 100%;
            height: auto;
            background: #000;
            cursor: crosshair;
        }

        .controls {
            display: flex;
            gap: 8px;
            padding: 16px;
            border-top: 1px solid #e1e4e8;
            background: #fafbfc;
            justify-content: center;
            flex-wrap: wrap;
        }

        button {
            padding: 6px 16px;
            background: #28a745;
            color: #fff;
            border: 1px solid #28a745;
            border-radius: 6px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 14px;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;
        }

        button:hover {
            background: #218838;
            border-color: #218838;
        }

        button:active {
            background: #1e7e34;
            border-color: #1e7e34;
        }

        @media (max-width: 768px) {
            canvas {
                max-width: 100%;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div class="game-wrapper">
            <canvas id="gameCanvas" width="800" height="600"></canvas>
            <div class="controls">
                <button id="startBtn">Start Game</button>
                <button id="pauseBtn">Pause</button>
                <button id="resetBtn">Reset</button>
            </div>
        </div>
    </div>

    <script>
        // ============================================
        // PAC-MAN GAME ENGINE - ROBUST IMPLEMENTATION
        // ============================================

        class Vector2 {
            constructor(x = 0, y = 0) {
                this.x = x;
                this.y = y;
            }

            add(other) {
                return new Vector2(this.x + other.x, this.y + other.y);
            }

            distance(other) {
                const dx = this.x - other.x;
                const dy = this.y - other.y;
                return Math.sqrt(dx * dx + dy * dy);
            }

            clone() {
                return new Vector2(this.x, this.y);
            }
        }

        class Pellet {
            constructor(x, y, repoName) {
                this.pos = new Vector2(x, y);
                this.repoName = repoName;
                this.collected = false;
                this.radius = 5;
            }

            draw(ctx) {
                if (this.collected) return;

                ctx.fillStyle = '#FFD700';
                ctx.beginPath();
                ctx.arc(this.pos.x, this.pos.y, this.radius, 0, Math.PI * 2);
                ctx.fill();

                ctx.strokeStyle = 'rgba(255, 215, 0, 0.5)';
                ctx.lineWidth = 1;
                ctx.stroke();
            }

            checkCollision(pacman) {
                return this.pos.distance(pacman.pos) < pacman.radius + this.radius;
            }
        }

        class Ghost {
            constructor(x, y, color) {
                this.pos = new Vector2(x, y);
                this.velocity = new Vector2(0, 0);
                this.color = color;
                this.radius = 12;
                this.speed = 1.5;
                this.direction = Math.floor(Math.random() * 4);
                this.changeDirectionTimer = 0;
                this.changeDirectionInterval = 60;
            }

            update(pacman, canvasWidth, canvasHeight) {
                // Random direction changes
                this.changeDirectionTimer++;
                if (this.changeDirectionTimer > this.changeDirectionInterval) {
                    this.direction = Math.floor(Math.random() * 4);
                    this.changeDirectionTimer = 0;
                    this.changeDirectionInterval = Math.random() * 60 + 40;
                }

                // Simple pathfinding towards Pac-Man
                const dx = pacman.pos.x - this.pos.x;
                const dy = pacman.pos.y - this.pos.y;
                const distance = Math.sqrt(dx * dx + dy * dy);

                if (distance < 150) {
                    this.velocity.x = (dx / distance) * this.speed;
                    this.velocity.y = (dy / distance) * this.speed;
                } else {
                    // Random movement
                    switch (this.direction) {
                        case 0: // Up
                            this.velocity.set(0, -this.speed);
                            break;
                        case 1: // Right
                            this.velocity.set(this.speed, 0);
                            break;
                        case 2: // Down
                            this.velocity.set(0, this.speed);
                            break;
                        case 3: // Left
                            this.velocity.set(-this.speed, 0);
                            break;
                    }
                }

                this.pos = this.pos.add(this.velocity);

                // Boundary wrapping
                if (this.pos.x < 0) this.pos.x = canvasWidth;
                if (this.pos.x > canvasWidth) this.pos.x = 0;
                if (this.pos.y < 0) this.pos.y = canvasHeight;
                if (this.pos.y > canvasHeight) this.pos.y = 0;
            }

            draw(ctx) {
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.arc(this.pos.x, this.pos.y, this.radius, Math.PI, 0);
                ctx.lineTo(this.pos.x + this.radius, this.pos.y + this.radius);
                ctx.lineTo(this.pos.x - this.radius, this.pos.y + this.radius);
                ctx.fill();

                // Eyes
                ctx.fillStyle = '#fff';
                const eyeOffsetX = 6;
                const eyeOffsetY = 4;
                ctx.beginPath();
                ctx.arc(this.pos.x - eyeOffsetX, this.pos.y - eyeOffsetY, 3, 0, Math.PI * 2);
                ctx.fill();
                ctx.beginPath();
                ctx.arc(this.pos.x + eyeOffsetX, this.pos.y - eyeOffsetY, 3, 0, Math.PI * 2);
                ctx.fill();
            }

            checkCollision(pacman) {
                return this.pos.distance(pacman.pos) < this.radius + pacman.radius;
            }

            set(x, y) {
                this.pos.x = x;
                this.pos.y = y;
            }
        }

        Vector2.prototype.set = function(x, y) {
            this.x = x;
            this.y = y;
            return this;
        };

        class PacMan {
            constructor(x, y) {
                this.pos = new Vector2(x, y);
                this.velocity = new Vector2(0, 0);
                this.nextVelocity = new Vector2(0, 0);
                this.radius = 10;
                this.speed = 2;
                this.mouthAngle = 0;
                this.direction = 0; // 0: right, 1: down, 2: left, 3: up
            }

            update(canvasWidth, canvasHeight) {
                // Apply next velocity if possible
                this.velocity = this.nextVelocity.clone();

                this.pos = this.pos.add(this.velocity.clone().set(
                    this.velocity.x * this.speed,
                    this.velocity.y * this.speed
                ));

                // Boundary wrapping
                if (this.pos.x < 0) this.pos.x = canvasWidth;
                if (this.pos.x > canvasWidth) this.pos.x = 0;
                if (this.pos.y < 0) this.pos.y = canvasHeight;
                if (this.pos.y > canvasHeight) this.pos.y = 0;

                // Update mouth animation
                this.mouthAngle = (this.mouthAngle + 0.05) % (Math.PI * 2);
            }

            draw(ctx) {
                ctx.fillStyle = '#FFD700';
                ctx.beginPath();

                const mouthOpen = Math.abs(Math.sin(this.mouthAngle)) * 0.3;
                const startAngle = mouthOpen;
                const endAngle = Math.PI * 2 - mouthOpen;

                ctx.arc(this.pos.x, this.pos.y, this.radius, startAngle, endAngle);
                ctx.lineTo(this.pos.x, this.pos.y);
                ctx.fill();

                ctx.strokeStyle = 'rgba(255, 215, 0, 0.6)';
                ctx.lineWidth = 2;
                ctx.stroke();
            }

            setDirection(vx, vy) {
                this.nextVelocity.set(vx, vy);

                if (vx === 1) this.direction = 0;
                else if (vx === -1) this.direction = 2;
                else if (vy === -1) this.direction = 3;
                else if (vy === 1) this.direction = 1;
            }

            reset(x, y) {
                this.pos.set(x, y);
                this.velocity.set(0, 0);
                this.nextVelocity.set(0, 0);
                this.mouthAngle = 0;
            }
        }

        class PacManGame {
            constructor(canvasId, repositories) {
                this.canvas = document.getElementById(canvasId);
                this.ctx = this.canvas.getContext('2d');
                this.repositories = repositories;
                this.gameState = 'idle'; // idle, playing, paused, gameOver, won
                this.score = 0;
                this.lives = 3;
                this.collectedPellets = 0;

                this.initializeGame();
                this.setupEventListeners();
            }

            initializeGame() {
                this.pacman = new PacMan(
                    this.canvas.width / 2,
                    this.canvas.height / 2
                );

                this.pellets = [];
                this.createPellets();

                this.ghosts = [
                    new Ghost(100, 100, '#FF006E'),
                    new Ghost(700, 100, '#00D9FF'),
                    new Ghost(100, 500, '#39FF14'),
                    new Ghost(700, 500, '#FF6B35')
                ];

                this.frameCount = 0;
                this.lastFrameTime = Date.now();
            }

            createPellets() {
                this.pellets = [];
                const padding = 40;
                const cols = Math.floor((this.canvas.width - padding * 2) / 60);
                const rows = Math.floor((this.canvas.height - padding * 2) / 60);

                for (let i = 0; i < this.repositories.length; i++) {
                    const col = i % cols;
                    const row = Math.floor(i / cols);
                    const x = padding + col * 60 + 30;
                    const y = padding + row * 60 + 30;

                    this.pellets.push(new Pellet(x, y, this.repositories[i].name));
                }
            }

            setupEventListeners() {
                document.addEventListener('keydown', (e) => {
                    switch(e.key.toLowerCase()) {
                        case 'arrowup':
                        case 'w':
                            this.pacman.setDirection(0, -1);
                            e.preventDefault();
                            break;
                        case 'arrowdown':
                        case 's':
                            this.pacman.setDirection(0, 1);
                            e.preventDefault();
                            break;
                        case 'arrowleft':
                        case 'a':
                            this.pacman.setDirection(-1, 0);
                            e.preventDefault();
                            break;
                        case 'arrowright':
                        case 'd':
                            this.pacman.setDirection(1, 0);
                            e.preventDefault();
                            break;
                    }
                });

                this.canvas.addEventListener('mousemove', (e) => {
                    if (this.gameState !== 'playing') return;

                    const rect = this.canvas.getBoundingClientRect();
                    const mouseX = e.clientX - rect.left;
                    const mouseY = e.clientY - rect.top;

                    const dx = mouseX - this.pacman.pos.x;
                    const dy = mouseY - this.pacman.pos.y;

                    if (Math.abs(dx) > Math.abs(dy)) {
                        this.pacman.setDirection(dx > 0 ? 1 : -1, 0);
                    } else {
                        this.pacman.setDirection(0, dy > 0 ? 1 : -1);
                    }
                });
            }

            start() {
                this.gameState = 'playing';
                this.gameLoop();
            }

            pause() {
                if (this.gameState === 'playing') {
                    this.gameState = 'paused';
                } else if (this.gameState === 'paused') {
                    this.gameState = 'playing';
                    this.gameLoop();
                }
            }

            reset() {
                this.score = 0;
                this.lives = 3;
                this.collectedPellets = 0;
                this.initializeGame();
                this.gameState = 'idle';
                this.draw();
            }

            update() {
                if (this.gameState !== 'playing') return;

                this.pacman.update(this.canvas.width, this.canvas.height);

                // Update ghosts
                for (let ghost of this.ghosts) {
                    ghost.update(this.pacman, this.canvas.width, this.canvas.height);

                    // Check ghost collision
                    if (ghost.checkCollision(this.pacman)) {
                        this.lives--;
                        if (this.lives <= 0) {
                            this.gameState = 'gameOver';
                        } else {
                            this.pacman.reset(this.canvas.width / 2, this.canvas.height / 2);
                        }
                    }
                }

                // Check pellet collisions
                for (let pellet of this.pellets) {
                    if (!pellet.collected && pellet.checkCollision(this.pacman)) {
                        pellet.collected = true;
                        this.score += 10;
                        this.collectedPellets++;
                    }
                }

                // Check win condition
                if (this.collectedPellets === this.pellets.length) {
                    this.gameState = 'won';
                }
            }

            draw() {
                // Clear canvas
                this.ctx.fillStyle = '#000';
                this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

                // Draw grid background
                this.ctx.strokeStyle = 'rgba(255, 215, 0, 0.05)';
                this.ctx.lineWidth = 1;
                for (let i = 0; i <= this.canvas.width; i += 40) {
                    this.ctx.beginPath();
                    this.ctx.moveTo(i, 0);
                    this.ctx.lineTo(i, this.canvas.height);
                    this.ctx.stroke();
                }
                for (let i = 0; i <= this.canvas.height; i += 40) {
                    this.ctx.beginPath();
                    this.ctx.moveTo(0, i);
                    this.ctx.lineTo(this.canvas.width, i);
                    this.ctx.stroke();
                }

                // Draw pellets
                for (let pellet of this.pellets) {
                    pellet.draw(this.ctx);
                }

                // Draw ghosts
                for (let ghost of this.ghosts) {
                    ghost.draw(this.ctx);
                }

                // Draw Pac-Man
                this.pacman.draw(this.ctx);

                // Draw game status text
                if (this.gameState === 'paused') {
                    this.ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
                    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

                    this.ctx.fillStyle = '#FFD700';
                    this.ctx.font = 'bold 40px Arial';
                    this.ctx.textAlign = 'center';
                    this.ctx.fillText('PAUSED', this.canvas.width / 2, this.canvas.height / 2);
                }

                if (this.gameState === 'gameOver') {
                    this.ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
                    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

                    this.ctx.fillStyle = '#FF006E';
                    this.ctx.font = 'bold 50px Arial';
                    this.ctx.textAlign = 'center';
                    this.ctx.fillText('GAME OVER', this.canvas.width / 2, this.canvas.height / 2 - 30);

                    this.ctx.fillStyle = '#FFD700';
                    this.ctx.font = 'bold 30px Arial';
                    this.ctx.fillText(`Final Score: ${this.score}`, this.canvas.width / 2, this.canvas.height / 2 + 30);
                }

                if (this.gameState === 'won') {
                    this.ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
                    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

                    this.ctx.fillStyle = '#39FF14';
                    this.ctx.font = 'bold 50px Arial';
                    this.ctx.textAlign = 'center';
                    this.ctx.fillText('YOU WIN!', this.canvas.width / 2, this.canvas.height / 2 - 30);

                    this.ctx.fillStyle = '#FFD700';
                    this.ctx.font = 'bold 30px Arial';
                    this.ctx.fillText(`Final Score: ${this.score}`, this.canvas.width / 2, this.canvas.height / 2 + 30);
                }
            }



            gameLoop() {
                if (this.gameState === 'playing') {
                    this.update();
                    this.draw();
                    requestAnimationFrame(() => this.gameLoop());
                } else if (this.gameState === 'paused') {
                    this.draw();
                } else if (this.gameState === 'gameOver' || this.gameState === 'won') {
                    this.draw();
                }
            }
        }

        // Initialize game
        let game;

        document.addEventListener('DOMContentLoaded', () => {
            const repositoriesData = [
                { name: 'data-science-toolkit' },
                { name: 'web-framework-js' },
                { name: 'machine-learning-models' },
                { name: 'python-utilities' },
                { name: 'mobile-app-react-native' },
                { name: 'api-rest-nodejs' },
                { name: 'devops-automation' },
                { name: 'game-engine-cpp' }
            ];

            game = new PacManGame('gameCanvas', repositoriesData);
            game.draw();

            // Button event listeners
            document.getElementById('startBtn').addEventListener('click', () => {
                game.start();
            });

            document.getElementById('pauseBtn').addEventListener('click', () => {
                game.pause();
            });

            document.getElementById('resetBtn').addEventListener('click', () => {
                game.reset();
            });
        });
    </script>
</body>
</html>
