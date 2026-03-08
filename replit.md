# Swordbattle.io V2 (Gun Edition)

A multiplayer browser-based gun fighting game where players battle each other to collect coins and grow stronger. Originally a sword-fighting game, converted to ranged gun combat.

## Architecture

This is a monorepo with three main components:

### `/client` - React Frontend (Port 5000)
- React + TypeScript, Webpack (CRA-style), Phaser 3 game engine
- Webpack Dev Server on `0.0.0.0:5000`
- `DANGEROUSLY_DISABLE_HOST_CHECK=true` set in `client/.env` for Replit proxy compatibility
- Connects to API on port 8080, game server on port 8008

### `/api` - NestJS REST API (Port 8080)
- NestJS + TypeORM + PostgreSQL
- Handles auth, accounts, cosmetics, game saves, leaderboards
- Database auto-syncs via TypeORM `synchronize: true`
- Config in `api/.env` and `api/src/config.ts`

### `/server` - uWebSockets.js Game Server (Port 8008)
- Real-time multiplayer game logic using uWebSockets.js
- 20 AI bots spawned on startup
- Config in `server/.env` and `server/src/config.js`

## Gun Conversion (from Swords)

The core combat was converted from melee swords to ranged guns:

### Type Renames (numeric values unchanged for protocol compatibility)
- `Types.Input.SwordSwing` (5) → `Types.Input.Shoot` (5)
- `Types.Input.SwordThrow` (6) → `Types.Input.AltFire` (6)
- `Types.Flags.SwordSwing` (7) → `Types.Flags.GunShoot` (7)
- `Types.Flags.SwordThrow` (8) → `Types.Flags.GunAltFire` (8)

### Key Files Modified
- `server/src/game/Types.js` + `client/src/game/Types.ts` — Type definitions
- `server/src/game/entities/Sword.js` — Server weapon logic (input references updated)
- `client/src/game/entities/Sword.ts` — Client projectile rendering (uses bullet texture)
- `client/src/game/entities/Player.ts` — Gun sprite on player, muzzle flash particles, skin loading uses `gunFileName`
- `client/src/game/SoundManager.ts` — GunShoot/GunAltFire sounds
- `client/src/game/scenes/Game.ts` — Preloads gun, bullet, muzzleFlash textures
- `client/src/game/Controls.ts` — Input mapping uses Shoot/AltFire
- `client/src/game/cosmetics.json` — All 454 skins have `gunFileName` field (default: `gun.png`)
- `client/src/game/hud/MobileControls.ts`, `Chat.ts` — Updated input references
- `server/src/game/entities/PlayerBot.js` — Bot AI uses new input names
- `server/src/game/evolutions/IceSpike.js` — `onSwordSwing` → `onGunShoot`

### New Assets
- `client/public/assets/game/player/gun.png` — Default gun sprite
- `client/public/assets/game/mobs/bullet.png` — Bullet projectile
- `client/public/assets/game/mobs/bulletShadow.png` — Bullet shadow
- `client/public/assets/game/particles/muzzleFlash.png` — Muzzle flash particle
- `client/public/assets/sound/GunShoot/GunShoot1.wav` — Gunshot sound

## Workflows

- **Start application** – `cd client && node scripts/start.js` (port 5000, webview)
- **API Backend** – `cd api && node dist/main.js` (port 8080, console)
- **Game Server** – `cd server && node src/index.js` (port 8008, console)

## Environment Variables

| Variable | Value | Purpose |
|----------|-------|---------|
| `PORT` | 5000 | Frontend port |
| `API_PORT` | 8080 | API port |
| `SERVER_PORT` | 8008 | Game server WebSocket port |
| `DATABASE_URL` | auto (Replit) | PostgreSQL connection string |

## Local Config Files

- `api/.env` – Sets `DB_URL`, `API_PORT`, `APP_SECRET`, `SERVER_SECRET`
- `server/.env` – Sets `SERVER_PORT`, `API_ENDPOINT`
- `client/.env` – Sets `PORT`, `HOST`, `REACT_APP_API`, `DANGEROUSLY_DISABLE_HOST_CHECK`

## Build

The API must be built before running:
```bash
cd api && npm run build
```

The client is served directly via webpack dev server (no build step needed for development).

## Database

Uses Replit's built-in PostgreSQL database. TypeORM auto-creates/syncs all tables on API startup. The `DB_URL` in `api/.env` must match the `DATABASE_URL` secret value.
