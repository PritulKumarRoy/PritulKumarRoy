# 🎮 Pac-Man Repository Explorer

> An interactive way to visualize and navigate your repositories. Each repo is a pellet waiting to be collected!

<div style="display: inline-block; padding: 12px 20px; background: #FFE135; color: #000; border-radius: 8px; font-weight: bold; margin: 20px 0;">
  👾 Click the Pac-Man to move around and collect repositories!
</div>

## 📊 Your Repository Collection

Your GitHub account currently tracks these repositories:

| # | Repository Name | Stars | Status |
|---|---|---|---|
| 1 | `data-science-toolkit` | ⭐⭐⭐⭐⭐ | Active |
| 2 | `web-framework-js` | ⭐⭐⭐⭐ | Active |
| 3 | `machine-learning-models` | ⭐⭐⭐⭐⭐ | Active |
| 4 | `python-utilities` | ⭐⭐⭐ | Maintained |
| 5 | `mobile-app-react-native` | ⭐⭐⭐⭐ | Active |
| 6 | `api-rest-nodejs` | ⭐⭐⭐⭐ | Active |
| 7 | `devops-automation` | ⭐⭐⭐ | Maintained |
| 8 | `game-engine-cpp` | ⭐⭐⭐⭐⭐ | Active |

---

## 🕹️ Interactive Pac-Man Game Board

Below you'll find an interactive visualization where Pac-Man represents your repository collection:

<script>
// Pacman Game Board - Repository Collector
const gameBoard = document.createElement('div');
gameBoard.style.cssText = `
  width: 100%;
  max-width: 600px;
  height: 450px;
  background: #000;
  border: 3px solid #FFE135;
  border-radius: 8px;
  position: relative;
  margin: 20px 0;
  box-shadow: 0 0 20px rgba(255, 225, 53, 0.3);
`;

// Game state
const game = {
  pacman: { x: 0, y: 0, direction: 'right' },
  pellets: [],
  score: 0,
  collected: 0,
  gameWidth: 600,
  gameHeight: 450,
  cellSize: 40
};

// Repository names - converted to pellets
const repos = [
  'data-science-toolkit',
  'web-framework-js',
  'machine-learning',
  'python-utils',
  'mobile-app',
  'api-rest',
  'devops',
  'game-engine'
];

// Initialize pellets
function initPellets() {
  game.pellets = [];
  repos.forEach((repo, idx) => {
    game.pellets.push({
      x: (idx % 5) * 110 + 50,
      y: Math.floor(idx / 5) * 120 + 100,
      name: repo,
      collected: false
    });
  });
}

// Create pellet elements
function renderPellets() {
  game.pellets.forEach(pellet => {
    if (!pellet.collected) {
      const pelletEl = document.createElement('div');
      pelletEl.style.cssText = `
        position: absolute;
        width: 14px;
        height: 14px;
        background: #FFE135;
        border-radius: 50%;
        left: ${pellet.x}px;
        top: ${pellet.y}px;
        box-shadow: 0 0 8px rgba(255, 225, 53, 0.6);
        transition: transform 0.1s;
      `;
      pelletEl.title = pellet.name;
      gameBoard.appendChild(pelletEl);
    }
  });
}

// Create Pac-Man
function renderPacman() {
  const pacmanEl = document.createElement('div');
  const rotation = game.pacman.direction === 'right' ? 0 : 
                   game.pacman.direction === 'left' ? 180 : 
                   game.pacman.direction === 'down' ? 90 : 270;
  
  pacmanEl.style.cssText = `
    position: absolute;
    width: 24px;
    height: 24px;
    background: #FFE135;
    border-radius: 50%;
    left: ${game.pacman.x}px;
    top: ${game.pacman.y}px;
    clip-path: polygon(25% 0%, 75% 0%, 100% 25%, 100% 75%, 75% 100%, 25% 100%, 0% 75%, 0% 25%);
    transform: rotate(${rotation}deg);
    box-shadow: 0 0 10px rgba(255, 225, 53, 0.8);
    z-index: 10;
  `;
  gameBoard.appendChild(pacmanEl);
}

// Update game
function update() {
  gameBoard.innerHTML = '';
  
  // Check collisions with pellets
  game.pellets.forEach(pellet => {
    if (!pellet.collected) {
      const distance = Math.hypot(
        game.pacman.x - pellet.x,
        game.pacman.y - pellet.y
      );
      if (distance < 20) {
        pellet.collected = true;
        game.score += 10;
        game.collected++;
      }
    }
  });
  
  // Draw game
  renderPellets();
  renderPacman();
  
  // Draw score
  const scoreEl = document.createElement('div');
  scoreEl.style.cssText = `
    position: absolute;
    color: #FFE135;
    font-weight: bold;
    top: 10px;
    left: 10px;
    font-family: monospace;
    font-size: 16px;
  `;
  scoreEl.textContent = `Score: ${game.score} | Repos: ${game.collected}/${repos.length}`;
  gameBoard.appendChild(scoreEl);
}

// Mouse control
gameBoard.addEventListener('mousemove', (e) => {
  const rect = gameBoard.getBoundingClientRect();
  const mouseX = e.clientX - rect.left;
  const mouseY = e.clientY - rect.top;
  
  const dx = mouseX - game.pacman.x;
  const dy = mouseY - game.pacman.y;
  
  if (Math.abs(dx) > Math.abs(dy)) {
    game.pacman.direction = dx > 0 ? 'right' : 'left';
  } else {
    game.pacman.direction = dy > 0 ? 'down' : 'up';
  }
  
  game.pacman.x = Math.max(0, Math.min(game.gameWidth - 24, mouseX - 12));
  game.pacman.y = Math.max(0, Math.min(game.gameHeight - 24, mouseY - 12));
  
  update();
});

// Initialize
initPellets();
update();
document.currentScript.parentNode.appendChild(gameBoard);
</script>

---

## 📈 Repository Statistics

```
Total Repositories: 8
Active Projects: 6
Maintained: 2
Total Stars: ⭐⭐⭐⭐⭐ (4.6/5 average)
Last Updated: 2024
```

---

## 🏆 Top Repositories

### 🥇 **data-science-toolkit**
A comprehensive toolkit for data science workflows and machine learning pipelines.
- **Stars:** ⭐⭐⭐⭐⭐
- **Language:** Python
- **Status:** Active Development

### 🥈 **game-engine-cpp**
High-performance game engine built with C++ for cross-platform development.
- **Stars:** ⭐⭐⭐⭐⭐
- **Language:** C++
- **Status:** Active Development

### 🥉 **web-framework-js**
Modern JavaScript framework for building responsive web applications.
- **Stars:** ⭐⭐⭐⭐
- **Language:** JavaScript
- **Status:** Active Development

---

## 🎯 Getting Started

Each repository in the collection above has its own detailed README with setup instructions, documentation, and contribution guidelines. 

**To explore a repository:**
1. Visit the repository page
2. Read the comprehensive documentation
3. Install dependencies as specified
4. Follow the setup instructions
5. Start contributing!

---

## 🤝 Contributing

We welcome contributions across all our repositories! Please:

1. **Fork** the repository you're interested in
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

---

## 📝 License

All repositories are licensed under the **MIT License**. See individual repository LICENSE files for details.

---

## 📞 Contact & Support

- **Issues:** Report bugs and feature requests on individual repository issue trackers
- **Discussions:** Start a discussion in the repository you're interested in
- **Email:** contact@example.com
- **Website:** www.example.com

---

<div style="text-align: center; padding: 20px; background: #1a1a1a; border-radius: 8px; margin-top: 20px; color: #FFE135; font-weight: bold;">
  👾 Happy Coding! May your repositories be ever bug-free and your commits well-documented! 👾
</div>

---

**Last Updated:** May 2026  
**Total Repositories Tracked:** 8  
**Game Status:** 🟢 Active
