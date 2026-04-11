# Vibe Defence — Path Architect

**A tactical tower defence game where you draw the path enemies must follow.**

> Survive 10 waves of increasingly difficult enemies. Your towers are fixed — you control the path. Draw it wisely.

**Play now:** `https://bnwraptor.github.io/vibe-defence/`

---

## 🎮 How to Play

### Objective
Draw a path from the **green SPAWN** zone to the **red BASE**. Enemies follow this path through your tower coverage. Protect your base HP — if it reaches zero, game over.

### Controls
- **Tap/click** on empty cell — place a path tile
- **Tap/click** on existing path tile — remove it (refunds budget)
- **Drag/swipe** — draw multiple path tiles at once
- **Tap tower** — select it (shows range + upgrade button)
- **Tap PLAY** — start the wave

### Game Flow
1. **Planning** — draw and extend your path, upgrade towers, buy HP
2. **Wave Active** — enemies follow the path; you can reroute mid-wave (costs coins)
3. **Wave Complete** — earn bonus coins, repeat
4. **Game Over** — HP reaches 0
5. **Victory** — survive all 10 waves (endless mode continues indefinitely)

---

## 🏗️ Core Mechanics

### Path Drawing
- Path must form a **continuous chain** from SPAWN to BASE
- First tile adjacent to SPAWN; last tile adjacent to BASE
- Path budget starts at **20 tiles** and accumulates up to **110 max**
- Mid-wave reroute costs **5💰 per tile changed**

### Towers (7 on the map, positions randomized each game)

| Tower | Color | Range | Fire Rate | Damage | Special |
|-------|-------|-------|-----------|--------|---------|
| **TURRET** | Cyan | 3 | 0.5s | 10 | Fast, balanced |
| **CANNON** | Orange | 2 | 2.0s | 40 | Slow, powerful, splash damage |
| **SNIPER** | Red | 6 | 3.0s | 80 | Long range, armor-piercing |
| **FROST** | Blue | 2.5 | 1.0s | 5 | Slows enemies 50% for 1s |

### Tower Upgrades (Levels 1–99)
Tap a tower to select it, then tap the **↑ upgrade button**.

- **Damage**: ×1.10 per level
- **Fire Rate**: ÷1.06 per level (faster, min 0.15s cap)
- **Range**: ×1.05 per level (max 1.8× base)
- **Cost formula:** `30 × 1.2^(level−1)`

### Enemies

| Enemy | HP (Wave 1) | Speed | Reward | Notes |
|-------|-------------|-------|--------|-------|
| **SCOUT** | 30 | Fast (3) | 5💰 | Small, fast |
| **SOLDIER** | 80 | Medium (2) | 10💰 | Standard |
| **TANK** | 200 | Slow (1) | 25💰 | High HP, tanky |
| **SPEEDSTER** | 20 | Very Fast (5) | 8💰 | Hardest to hit |
| **BOSS** | 500 | Very Slow (0.8) | 100💰 | Large, appears waves 9–10 |
| **MEGABOSS** | 1500 | Very Slow (0.8) | 300💰 | Massive, appears every 5th endless wave |

HP scales **+15% per wave** (25% per wave for bosses). Endless mode adds extra speed scaling.

### Wave Composition

| Wave | Composition |
|------|-------------|
| 1 | 5 Scouts |
| 2 | 8 Scouts, 2 Soldiers |
| 3 | 5 Scouts, 5 Soldiers |
| 4 | 10 Soldiers, 2 Tanks |
| 5 | 8 Soldiers, 3 Tanks, 1 Speedster |
| 6 | 5 Scouts, 5 Soldiers, 5 Tanks, 2 Speedsters |
| 7 | 10 Soldiers, 5 Tanks, 5 Speedsters |
| 8 | 15 Soldiers, 8 Tanks, 8 Speedsters |
| 9 | 5 Scouts, 5 Soldiers, 5 Tanks, 5 Speedsters, 3 BOSSES |
| 10 | 20 Soldiers, 10 Tanks, 10 Speedsters, 5 BOSSES |
| 11+ | Endless — count and speed scale infinitely |

### Dynamic Wave Modifiers
Starting wave 11, each wave randomly applies one or both:
- **⚡ STORM** (15% chance) — all enemies +20% speed, spawn interval −20%
- **🛡️ ARMORED** ((wave−10)% chance) — all enemies gain +10 armor

Both can stack.

### Power-Ups

| Power | Cost | Effect |
|-------|------|--------|
| **❄ FREEZE** | 20💰 | Freezes all enemies for **3 seconds** |
| **💥 NUKE** | 35💰 | Deals **50 damage** to all enemies instantly |
| **⚡ BOOST** | 25💰 | **Doubles** tower fire rate for **5 seconds** |

### Economy

| Item | Cost |
|------|------|
| HP restore (+10 HP) | 15💰 |
| Path tile reroute (mid-wave) | 5💰 |
| Wave completion bonus | 20💰 |

---

## ⚙️ Difficulty Settings

| Difficulty | Enemy HP | Enemy Speed | Starting HP | Starting Coins | Spawn Rate |
|------------|----------|-------------|-------------|----------------|------------|
| **EASY** | 80% | 90% | 120 HP | 150💰 | Slower (1.3×) |
| **NORMAL** | 100% | 100% | 100 HP | 100💰 | Standard (1.0×) |
| **HARD** | 120% | 115% | 80 HP | 80💰 | Faster (0.85×) |

---

## 🎯 Tips & Strategy

1. **Longer path = more damage** — enemies passing through more tower zones means more shots
2. **FROST first** — slowing enemies gives all your other towers more time to fire
3. **SNIPER upgrades pay off most** — highest base damage, armor-piercing
4. **Save BOOST for boss waves** — doubled fire rate melts BOSSES and MEGABOSSES
5. **Reroute mid-wave if needed** — 5💰 to redirect enemies through better coverage is worth it
6. **Balance HP and upgrades** — dying with 500💰 unspent is a waste
7. **Plan for wave 9–10** — BOSSES are extremely tanky; ensure high-damage coverage

---

## 🏆 Scoring

Your score is your **highest wave reached**. Endless mode continues scaling forever — how far can you go?

---

## 📱 Technical Details

- **Single HTML file** — no build step, no dependencies, runs anywhere
- **Canvas-based rendering** — smooth 60fps on all screen sizes
- **Touch + mouse support** — unified Pointer Events API
- **Responsive** — adapts to any screen size via canvas scaling
- **Local storage** — high scores persist across sessions
- **Randomized towers** — tower types AND positions shuffle each new game
- **Audio** — Web Audio API for placement, shooting, explosions, wave complete

---

## 🔧 Development

```bash
# Serve locally
python3 -m http.server 3000

# Open http://localhost:3000
```

---

## 📄 License

Built for **Vibe Jam 2026**.