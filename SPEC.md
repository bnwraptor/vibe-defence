# Tower Defense: Path Architect — Full Specification

> A tower defense game where players don't place towers — they design the enemy path.

---

## Competition Rules (Reference)

- Anyone can enter with their game
- At least 90% of code has to be written by AI
- Game should be started today or after today — no old games
- Game must be accessible on web without login/signup and free-to-play (own domain or subdomain preferred)
- Multiplayer games preferred but **not required**
- Can use any engine but **Three.js is recommended**
- **NO loading screens and heavy downloads** — must be almost instant (except maybe ask username)
- One entry per person (focus on making one really good game!)

---

---

## 1. Game States

| State | Description |
|-------|-------------|
| `MENU` | Title screen, "Start Game" button, optional "How to Play" |
| `PLANNING` | Player draws path on grid; towers visible but inactive |
| `WAVE_ACTIVE` | Enemies spawning and moving; towers shooting; player cannot edit path |
| `PAUSED` | Overlay pauses game; tap to resume |
| `GAME_OVER` | Base HP reached 0; show score, waves survived, "Restart" button |
| `VICTORY` | All waves cleared; show final score, "Play Again" button |

---

## 2. Grid System

- **Grid size**: 12 columns × 16 rows (portrait on mobile, letterboxed on landscape)
- **Cell size**: Computed dynamically = `min(screenWidth / 12, screenHeight / 16)`
- **Cell types**:
  - `EMPTY` — buildable path tile
  - `BLOCKED` — terrain/obstacle, cannot place path (towers may sit here)
  - `SPAWN` — fixed enemy entry point (left edge, row 8)
  - `BASE` — fixed endpoint (right edge, row 8)
  - `TOWER` — fixed tower position (pre-placed, ~6-8 towers on map)
- **Path tiles** placed by player connect orthogonally (no diagonals)
- Path must form a continuous route from `SPAWN` to `BASE` before wave starts
- **Path budget**: 20 tiles per wave (displayed as `PATH: 12/20`)

---

## 3. Path Drawing Mechanics

- **Tap empty cell** → place path tile (if budget available)
- **Tap placed path tile** → remove it (refunds to budget)
- **Swipe across cells** → place path tiles along swipe trail (respects budget)
- **Path validation** on "Start Wave":
  - Path must be continuous SPAWN → BASE
  - If invalid → flash path red, show tooltip "Path not connected!"
- **Mid-wave reroute** (optional feature):
  - Costs 5 currency to edit path while wave is active
  - Enemies already in play continue on old path; new enemies use updated path

---

## 4. Tower System

### Tower Positions
- 6-8 towers pre-placed on map at game start
- Fixed positions shown as colored squares with range indicator
- Towers cannot be moved or sold

### Tower Types (pick 3-4 for MVP)

| Tower | Shape | Color | Range | Fire Rate | Damage | Cost |
|-------|-------|-------|-------|-----------|--------|------|
| Turret | Square | Cyan | 3 tiles | Fast (0.5s) | 10 | — (pre-placed) |
| Cannon | Square | Orange | 2 tiles | Slow (2s) | 40 | — (pre-placed) |
| Sniper | Square | Red | 6 tiles | Very Slow (3s) | 80 | — (pre-placed) |
| Frost | Square | Blue | 2.5 tiles | Medium (1s) | 5 + 50% slow | — (pre-placed) |

### Tower Behavior
- Each tower targets **nearest enemy within range**
- Projectiles travel visually from tower to enemy (optional: can be instant-hit)
- Range visualized as a faint circle when tapping tower
- Towers only fire during `WAVE_ACTIVE` state

### Projectiles
- Simple shapes: small circles matching tower color
- Speed: 400px/s
- On hit: enemy HP reduced, projectile disappears, small flash effect

---

## 5. Enemy System

### Enemy Types

| Enemy | Shape | Color | Base HP | Speed (tiles/s) | Reward |
|-------|-------|-------|---------|-----------------|--------|
| Scout | Circle (small) | Green | 30 | 3 | 5 |
| Soldier | Circle (medium) | Yellow | 80 | 2 | 10 |
| Tank | Circle (large) | Gray | 200 | 1 | 25 |
| Speedster | Circle (small) | Pink | 20 | 5 | 8 |
| Boss | Circle (large, pulsing) | Purple | 500 | 0.8 | 100 |

### Enemy Behavior
- Spawn at `SPAWN`, follow path tile-to-tile toward `BASE`
- On reaching `BASE`: deal damage equal to `enemy.maxHP * 0.1`, then despawn
- On death: award `reward` currency, despawn, play death flash

### Health Scaling
- Each wave multiplies enemy HP by `1 + (waveNumber * 0.15)`
- e.g., Wave 3 enemies have `HP * 1.45`

---

## 6. Wave System

### Wave Composition

| Wave | Enemies |
|------|---------|
| 1 | 5 Scouts |
| 2 | 8 Scouts, 2 Soldiers |
| 3 | 5 Scouts, 5 Soldiers |
| 4 | 10 Soldiers, 2 Tanks |
| 5 | 8 Soldiers, 3 Tanks, 1 Speedster |
| 6 | 5 of each basic type, 2 Speedsters |
| 7 | 10 Soldiers, 5 Tanks, 5 Speedsters |
| 8 | 15 Soldiers, 8 Tanks, 8 Speedsters |
| 9 | 5 of each type, 3 Bosses |
| 10 (Final) | 20 Soldiers, 10 Tanks, 10 Speedsters, 5 Bosses |

- **Total waves**: 10
- **Spawn interval**: 1 enemy per 0.8 seconds
- **Wave delay**: 3 seconds between waves (during `PLANNING` state)

### Spawning
- Enemies spawn one-by-one with `SPAWN_INTERVAL` delay
- Spawn order follows wave's enemy array
- Enemy type shown briefly as icon before spawning

---

## 7. Economy System

### Currency
- Start with `100` currency
- Earn per kill: see Enemy table above
- Spend on: path reroutes (mid-wave), future features

### Starting Currency
- `100` at wave 1 start
- `+20` bonus at start of each subsequent planning phase

---

## 8. Base & Damage

- **Base HP**: 100
- **Damage per enemy reaching base**: `ceil(enemy.maxHP * 0.1)`
- Displayed as `HP: ████████░░ 80/100` (health bar)
- At 0 HP → `GAME_OVER`

---

## 9. UI Layout

### Mobile Portrait Layout

```
┌──────────────────────┐
│  WAVE 3    💰 145    │  ← Top HUD (48px)
├──────────────────────┤
│                      │
│                      │
│      GAME GRID       │  ← Main play area (flex)
│      (12 × 16)       │
│                      │
│                      │
├──────────────────────┤
│ PATH: ██████░░░░ 14/20│  ← Budget bar (32px)
│  [▶ START WAVE]      │  ← Action button (56px)
└──────────────────────┘
```

### Top HUD
- Left: `WAVE X/10`
- Center: `HP: ██████░░░░`
- Right: `💰 CURRENCY`

### Bottom Bar
- Path budget progress bar
- "START WAVE" button (disabled if path invalid)

### Pause Button
- Top-right corner, small "⏸" icon

---

## 10. Controls

| Action | Input |
|--------|-------|
| Place path tile | Tap empty cell |
| Remove path tile | Tap placed tile |
| Draw path (drag) | Swipe across cells |
| See tower range | Tap tower |
| Pause game | Tap ⏸ button |
| Start wave | Tap START WAVE button |

---

## 11. Visual Style

### Aesthetic
- **Dark background**: `#0a0a0f`
- **Grid lines**: `#1a1a2e` (subtle)
- **Path tiles**: `#2d2d44` with `#4a4a6a` border
- **Towers**: Solid squares with glow matching their color
- **Enemies**: Circles with inner highlight for depth
- **Projectiles**: Small bright circles with motion trail (2-frame trail)
- **Base**: Pulsing red square
- **Spawn**: Pulsing green square

### Font
- System font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- All caps for labels, bold for numbers

### Effects
- Enemy death: white flash + scale-down to 0 over 150ms
- Tower shot: brief muzzle flash (1 frame)
- Path valid: tiles slightly brighter
- Path invalid: tiles flash red
- Enemy slow (frost): blue tint on enemy circle
- Damage to base: screen shake (2px, 200ms)

---

## 12. Audio (Optional / Lightweight)

- **Enemy spawn**: short blip (oscillator, 100ms)
- **Enemy death**: short thud
- **Tower shot**: quiet pop
- **Base damage**: low rumble
- **Wave complete**: ascending chime

All sounds generated via **Web Audio API** — zero external files.

---

## 13. Technical Requirements

### Performance
- Target: **60fps** on mid-range mobile (iPhone SE 2020, Android mid-range)
- Max draw calls per frame: ~50
- No DOM manipulation during gameplay (pure Canvas)
- Object pooling for enemies and projectiles

### Loading
- **Zero external dependencies** — single HTML file
- All assets procedural (rectangles, circles, lines)
- Initial load: < 50KB
- **No loading screen** — game starts immediately

### Hosting
- Single `index.html` file
- Can be served from subdomain or GitHub Pages / Netlify
- **No backend required** — fully client-side

### Compatibility
- Modern browsers: Chrome 90+, Safari 14+, Firefox 88+
- Touch events + mouse events
- Responsive to any screen size

---

## 14. Game Balance Targets

| Metric | Target |
|--------|--------|
| Win rate on wave 5 (casual player) | ~50% |
| Average game length | 10-15 minutes |
| Currency flow | Tight early, looser later |
| Challenge curve | Smooth 1-10 |

---

## 15. MVP Scope (if time-constrained)

Cut in this order:
1. **Cut**: Mid-wave rerouting
2. **Cut**: Frost tower (keep 3 towers)
3. **Cut**: Boss enemies (waves 9-10 use max Tank + Speedster)
4. **Cut**: Screen shake / audio effects
5. **Cut**: Tower range tap-to-show (always show ranges faintly)

---

## 16. File Structure

```
index.html   ← Entire game, single file
```

Internal structure (within file):

```html
<!DOCTYPE html>
<html>
<head>
  <style>/* All CSS inline */</style>
</head>
<body>
  <canvas id="game"></canvas>
  <script>/* All JS inline */</script>
</body>
</html>
```

JS sections (clearly commented):

```javascript
// === CONSTANTS ===
// === STATE ===
// === GRID ===
// === PATH ===
// === TOWERS ===
// === ENEMIES ===
// === PROJECTILES ===
// === WAVE SYSTEM ===
// === RENDERING ===
// === INPUT ===
// === GAME LOOP ===
// === INIT ===
```

---

## Competition Checklist

- [ ] Web-based, no login/signup
- [ ] Free-to-play
- [ ] Accessible via own domain or subdomain
- [ ] 90%+ code written by AI
- [ ] Game started on/after competition date
- [ ] Multiplayer: N/A (single player)
- [ ] No loading screens
- [ ] ~Instant start
- [ ] Mobile-friendly / touch-native
- [ ] Lightweight (runs on any mobile device)
