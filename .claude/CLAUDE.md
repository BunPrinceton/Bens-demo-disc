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

## Development Commands

### Running the Games
```bash
# Open any game directly in browser
open index.html        # Tropical Tetris
open asteroids.html    # Tropical Asteroids
open mahjong.html      # Tropical Mahjong
open minesweeper.html  # Tropical Minesweeper
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
