# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Bens Demo Disc is a collection of browser-based games with tropical themes. All games are self-contained HTML files with embedded CSS and JavaScript - no build process, dependencies, or external frameworks required.

## Architecture

### File Structure
- `index.html` - Tropical Tetris (main game)
- `asteroids.html` - Tropical Asteroids space shooter
- `mahjong.html` - Tropical Mahjong solitaire
- `minesweeper.html` - Tropical Minesweeper coconut hunt
- `snake.html` - Tropical Snake island slither
- `2048.html` - Tropical 2048 fruit evolution puzzle
- `bubble-shooter.html` - Tropical Bubble Shooter match-3
- `README.md` - Game descriptions and controls

### Shared Design System

All games follow consistent tropical theme conventions:

**Visual Elements:**
- Beach background with animated waves (canvas-based, 5 wave layers with sine wave animation)
- Animated sun with pulsing effect
- Palm trees positioned at canvas edges
- Gradient sky from `#87CEEB` (sky blue) to `#FFE4B5` (peach)
- Sand color: `#F4A460`

**UI Patterns:**
- Primary color: `#FF6B35` (coral orange) for borders, headings, buttons
- Background panels: `#FFE4B5`/`#FFDAB9` (peach/tan gradients)
- Emoji-based game elements (fruits/vegetables as game pieces)
- High score persistence via localStorage
- Comic Sans MS font family

**Background Animation System:**
Each game initializes a shared beach background animation pattern:
```javascript
// Waves array with 5 layers
waves = [{ y, amplitude, frequency, speed, offset }]
// Animated in requestAnimationFrame loop
```

### Game-Specific Architecture

**Tropical Tetris (index.html):**
- 10x16 grid, 30px blocks
- 7 tetromino shapes mapped to fruits/veggies (corn, lemon, watermelon, etc.)
- Power-up system: 💣 Bomb, ⏰ Slow Time, 🌈 Rainbow, ⚡ Zap
- Power-ups spawn randomly (30% chance on line clear), fall independently
- Drop interval decreases with level (1000ms - level*100ms, min 100ms)
- localStorage key: `tropicalTetrisHighScore`

**Tropical Asteroids (asteroids.html):**
- 900x600 canvas with starfield background (100 twinkling stars)
- Ship: surfboard-styled with 🏄 lives indicator
- Asteroids: 16 fruit/veggie types, 3 size levels (breaks into smaller on hit)
- Power-ups: 🛡️ Shield, ⚡ Rapid Fire, ✨ Triple Shot, 💥 Screen Clear, 🕐 Slow Time
- 15% spawn chance when small asteroid destroyed
- Progressive difficulty: 3 + level asteroids per level
- localStorage key: `tropicalAsteroidsHighScore`

**Tropical Mahjong (mahjong.html):**
- 144-tile turtle/pyramid layout (5 layers)
- 18 fruit/veggie tile types (4 of each)
- Classic mahjong rules: match free tiles (uncovered + ≥1 side open)
- Score penalty system: -10 per hint used, -20 per shuffle
- 💡 Hint: flashes matching pair for 3s
- 🔄 Shuffle: randomizes free tiles only
- localStorage key: `tropicalMahjongHighScore`

**Tropical Minesweeper (minesweeper.html):**
- Grid-based with 3 difficulty levels: Easy (8x10/10 mines), Medium (14x18/40 mines), Hard (20x24/99 mines)
- Coconuts 🥥 as mines, beach umbrellas 🏖️ as flags
- Left-click to reveal, right-click to flag
- Safe first click: mines placed after first click, avoiding clicked cell and neighbors
- Auto-reveal recursive flood-fill for zero-adjacent cells
- Timer-based scoring (faster = better)
- Separate high scores per difficulty
- localStorage keys: `tropicalMinesweeperEasy`, `tropicalMinesweeperMedium`, `tropicalMinesweeperHard`

**Tropical Snake (snake.html):**
- 30x30 grid (600x600 canvas), 20px tiles
- 18 fruit/veggie types as food items
- Classic snake mechanics with arrow/WASD controls
- Power-up system: 🐢 Slow Down, ⚡ Speed Boost, 🌟 Shield, 🎯 Magnet, 💎 Double Points
- Power-ups spawn with 25% chance when eating food
- Base speed: 120ms, modified by speed power-ups
- Shield allows passing through walls and self
- Magnet attracts food within radius of 8 tiles
- Score: 10 points per food, 25 per power-up (doubled with 💎)
- Responsive layout: game wrapper fills 95vw × 95vh, canvas scales to fit with aspect-ratio: 1
- localStorage key: `tropicalSnakeHighScore`

**Tropical 2048 (2048.html):**
- 4x4 grid with 11-stage fruit evolution: 🍇(2) → 🍋(4) → 🍊(8) → 🍎(16) → 🥥(32) → 🍍(64) → 🥑(128) → 🍉(256) → 🥭(512) → 🍈(1024) → 🏆(2048)
- Three-column layout (left: fruit guide & controls, center: game board, right: scores & button)
- Grid layout: CSS Grid with `grid-template-columns: 1fr auto 1fr`
- Fixed tile positioning: 120px tiles, 130px spacing (10px gap), 530px grid size
- Absolute positioning with pixel coordinates for tiles
- Responsive scaling via CSS transform on smaller screens (0.7x at <600px, 0.5x at <400px)
- Stacks to single column layout on screens <1000px wide
- Arrow keys for movement, classic 2048 merge mechanics
- localStorage key: `tropical2048Best`

**Tropical Bubble Shooter (bubble-shooter.html):**
- Hexagonal grid layout for bubbles
- 7 fruit types: 🍇 Grape, 🍋 Lemon, 🍊 Orange, 🍎 Apple, 🥝 Kiwi, 🫐 Blueberry, 🍓 Strawberry
- Mouse aim with trajectory preview line
- Wall bouncing physics for shots
- Match-3+ mechanics: matching bubbles pop, disconnected bubbles fall
- Combo system with multiplier bonuses
- Next bubble preview
- Score boxes positioned on sides (SCORE: top-left, BEST: top-right) for more viewport space
- localStorage key: `tropicalBubbleShooterHighScore`

## Development Commands

### Running the Games
```bash
# Open any game directly in browser
open index.html           # Tropical Tetris
open asteroids.html       # Tropical Asteroids
open mahjong.html         # Tropical Mahjong
open minesweeper.html     # Tropical Minesweeper
open snake.html           # Tropical Snake
open 2048.html            # Tropical 2048
open bubble-shooter.html  # Tropical Bubble Shooter
```

### Testing
No automated tests. Manual testing involves:
- Playing through full game cycles
- Verifying localStorage persistence (check browser DevTools)
- Testing controls across different browsers
- Checking power-up/scoring mechanics

### Git Workflow
This is an active git repository. Recent commit history shows iterative game development:
- Game features added incrementally
- Bug fixes for layout/scoring issues
- Power-up system additions

## Key Implementation Details

### Canvas Animation Pattern
All games use dual canvas setup:
1. **Background canvas** (`#beach-background`) - fullscreen, z-index: 1
2. **Game canvas** - sized per game, z-index: 10+

Animation loop structure:
```javascript
function update(time) {
    drawBeach(time);      // Background
    drawGameElements();    // Game-specific
    requestAnimationFrame(update);
}
```

### Collision Detection
Games use distance-based collision (Euclidean distance):
```javascript
const dx = obj1.x - obj2.x;
const dy = obj1.y - obj2.y;
const distance = Math.sqrt(dx * dx + dy * dy);
if (distance < threshold) { /* collision */ }
```

### High Score Management
Pattern used across all games:
```javascript
let highScore = localStorage.getItem('gameKey') || 0;
// On score update:
if (score > highScore) {
    highScore = score;
    localStorage.setItem('gameKey', highScore);
}
```

### Emoji Rendering
Games render emoji via canvas text:
```javascript
ctx.font = '24px Arial';
ctx.textAlign = 'center';
ctx.textBaseline = 'middle';
ctx.fillText(emoji, x, y);
```

### Responsive Design Patterns

**Viewport-Fitting Layouts:**
- Snake: Game wrapper fills 95vw × 95vh, canvas scales with `aspect-ratio: 1` and responsive max constraints
- 2048: Three-column CSS Grid layout with responsive stacking on <1000px screens

**Canvas Scaling Approaches:**
- **CSS Transform**: 2048 uses `transform: scale()` on grid element (0.7x at <600px, 0.5x at <400px)
- **Responsive Constraints**: Snake uses `max-width: 90vw` and `max-height: 70vh` with `aspect-ratio: 1`
- **Fixed with Visual Scaling**: Asteroids uses CSS `transform: scale()` to decouple visual size from game physics

**Layout Patterns:**
- **Three-Column Grid** (2048): `grid-template-columns: 1fr auto 1fr` with left sidebar, center game, right sidebar
- **Flexbox Wrapper** (Snake): Full viewport wrapper with centered, scaled canvas
- **Fixed Positioning** (Bubble Shooter): Score boxes positioned absolutely at viewport edges

**Responsive Font Sizing:**
Use `clamp()` for fluid typography:
```css
font-size: clamp(1em, 3vw, 1.2em);
```

## Modifying Games

When adding features or fixing bugs:

1. **Maintain tropical theme consistency** - use existing color palette and visual elements
2. **Test localStorage** - verify high scores persist across sessions
3. **Check animation performance** - games target 60fps via requestAnimationFrame
4. **Preserve self-contained structure** - no external dependencies allowed
5. **Update README.md** - document new features/controls/gameplay mechanics

### Adding New Games
Follow this template structure:
- Beach background canvas setup
- Game canvas with appropriate size
- Emoji-based game elements
- High score tracking with unique localStorage key
- Controls display in UI
- Game over screen with "Play Again" button
- Match existing color scheme and UI patterns
