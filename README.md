# Vibe Defence — Path Architect

**A tactical tower defence game where you draw the path enemies must follow.**

> Survive 10 waves of increasingly difficult enemies. Place path tiles strategically to guide enemies through your tower coverage zones. Upgrade your towers to devastating power levels. How far can you go?

---

## 🎮 How to Play

### Objective
Draw a path from the **green SPAWN** zone to the **red BASE**. Enemies follow this path while your towers automatically attack them. Protect your base HP — if it reaches zero, game over.

### Controls
| Input | Action |
|-------|--------|
| **Tap/click** on empty cell | Place a path tile |
| **Tap/click** on existing path tile | Remove the tile (refunds budget) |
| **Tap/click** on a tower | Select tower, shows range + upgrade button |
| **Tap same tower** | Deselect |
| **Drag/swipe** | Draw multiple path tiles at once |
| **Tap PLAY** | Start the next wave |

### Game Flow
1. **Planning Phase** — Draw and extend your path, upgrade towers, buy HP
2. **Wave Active** — Enemies spawn and follow the path; you can reroute mid-wave (costs coins)
3. **Wave Complete** — Earn bonus coins, path tiles reset, repeat
4. **Game Over** — HP reaches 0; see your final wave reached
5. **Victory** — Survive all 10 waves; endless mode continues indefinitely

---

## 🏗️ Core Mechanics

### Path Drawing
- Path tiles must form a **continuous chain** from SPAWN to BASE
- First tile must be adjacent to SPAWN (green zone)
- Each subsequent tile must be adjacent to the previous tile
- Last tile must be adjacent to BASE
- Path budget starts at **20 tiles** and accumulates up to **110 max**

### Towers (7 on the map)

| Tower | Color | Range | Fire Rate | Damage | Special |
|-------|-------|-------|-----------|--------|---------|
| **TURRET** | Cyan | 3 | 0.5s | 10 | Fast firing, balanced |
| **CANNON** | Orange | 2 | 2.0s | 40 | Slow but powerful |
| **SNIPER** | Red | 6 | 3.0s | 80 | Long range, devastating |
| **FROST** | Blue | 2.5 | 1.0s | 5 | Slows enemies by 50% |

Towers are **fixed positions** — you cannot move them, only upgrade them.

### Tower Upgrades (Levels 1–99)
Towers can be upgraded infinitely. Tap a tower to select it, then tap the **upgrade button** (↑) that appears next to PLAY.

**Stats per level:**
- **Damage**: ×1.10 per level
- **Fire Rate**: ÷1.06 per level (faster, min 0.15s cap)
- **Range**: ×1.05 per level (max 1.8× base)

**Cost formula:** `30 × 1.2^(level−1)`

| Upgrade | Cost | Cumulative |
|---------|------|------------|
| Lv1 → Lv2 | 30💰 | 30 |
| Lv2 → Lv3 | 36💰 | 66 |
| Lv3 → Lv4 | 43💰 | 109 |
| Lv5 → Lv6 | 62💰 | 223 |
| Lv10 → Lv11 | 129💰 | 622 |
| Lv20 → Lv21 | 796💰 | 4,628 |

**Visual indicators:**
- Lv2: 2 silver stars
- Lv3: 3 gold stars (★★★)
- Lv4+: `Lv7` gold text above tower

### Enemies

| Enemy | HP | Speed | Reward | Notes |
|-------|-----|-------|--------|-------|
| **SCOUT** | 30 | Fast (3) | 5💰 | Small, fast |
| **SOLDIER** | 80 | Medium (2) | 10💰 | Standard enemy |
| **TANK** | 200 | Slow (1) | 25💰 | High HP, tanky |
| **SPEEDSTER** | 20 | Very Fast (5) | 8💰 | Hardest to hit, low HP |
| **BOSS** | 500+ | Very Slow (0.8) | 100💰 | Large, appears in waves 9–10 |

Enemy HP scales **+15% per wave** after wave 1.

### Waves (10 total + endless)

| Wave | Enemy Composition |
|------|-----------------|
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
| 11+ | Endless mode — enemy count and speed scale infinitely |

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

Choose your difficulty from the main menu:

| Difficulty | Enemy HP | Enemy Speed | Starting HP | Starting Coins | Spawn Rate |
|------------|----------|-------------|-------------|----------------|------------|
| **EASY** | 80% | 90% | 120 HP | 150💰 | Slower (1.3×) |
| **NORMAL** | 100% | 100% | 100 HP | 100💰 | Standard (1.0×) |
| **HARD** | 120% | 115% | 80 HP | 80💰 | Faster (0.85×) |

---

## 📱 Technical Details

- **Single HTML file** — no build step, no dependencies, runs anywhere
- **Canvas-based rendering** — smooth 60fps on all screen sizes
- **Touch + mouse support** — unified Pointer Events API
- **Responsive** — adapts to any screen size via CSS flex + canvas scaling
- **Local storage** — high scores (`bestWave`, `bestScore`) persist across sessions
- **Audio** — Web Audio API for placement, shooting, explosions, wave complete

---

## 🗺️ Map Layout

```
12 columns × 16 rows

Row 0  [BLOCKED tiles in a grid pattern...]
Row 1  [                        ]
Row 2  [    T[T]                ]  T = TURRET
Row 3  [T      T]                  C = CANNON
Row 4  [      C  T]              T = TURRET
Row 5  [T          ]              S = SNIPER
Row 6  [              S          F = FROST
Row 7  [S][SPAWN]══════════[BASE]  ═ = Default path
Row 8  [              S          ]
Row 9  [  F    C]              ]
Row 10 [          ]              ]
Row 11 [   T   ]              ]
Row 12 [                            ]
Row 13 [BLOCKED tiles...]
Row 14 [                        ]
Row 15 [                        ]
```

**SPAWN** (col 0, row 7) — enemies enter here
**BASE** (col 11, row 7) — enemies exit here (your base to defend)

---

## 🎯 Tips & Strategy

1. **Don't rush the first wave** — build a long winding path to maximize tower coverage time
2. **Prioritize FROST towers** — slowing enemies = more shots from other towers
3. **Reroute mid-wave** — if enemies are bypassing your best coverage, spend 5💰 to redirect them
4. **Save BOOST for boss waves** — doubled fire rate melts tanks and bosses
5. **Upgrade SNIPER first** — highest base damage, best return on investment
6. **Balance HP and upgrades** — dying with 500💰 unspent is wasteful
7. **Plan for wave 9–10** — bosses are extremely tanky; ensure high-damage coverage

---

## 🏆 Scoring

Your score is your **highest wave reached**. Endless mode continues scaling difficulty forever — how far can you go?

---

## 🔧 Development

To run locally:

```bash
# Serve the directory
python3 -m http.server 3000

# Or with npx
npx serve .
```

Open `http://localhost:3000` in your browser.

For Tailscale remote testing:

```bash
# On the server running the game:
tailscale status  # note your Tailscale IP (e.g. 100.x.x.x)
python3 -m http.server 3000 --bind 0.0.0.0

# On your phone/browser connected to Tailscale:
# Open http://100.x.x.x:3000
```

---

## 📄 License

This game was built for the **Vibe Jam 2026** game jam.

---

## 🎮 Game Controls Quick Reference

| Action | Input |
|--------|-------|
| Place/remove path | Tap/click cell |
| Draw multiple tiles | Swipe/drag |
| Select tower | Tap tower |
| Deselect tower | Tap same tower again |
| Upgrade tower | Select tower → tap ↑ button |
| Buy HP | Tap +HP button |
| Use power-up | Tap ❄ / 💥 / ⚡ buttons |
| Start wave | Tap PLAY |
| Pause | Tap ⏸ button |
| Restart | Select GAME OVER → tap RESTART |
