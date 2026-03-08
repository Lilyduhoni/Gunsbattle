# Swordbattle.io V2

A multiplayer browser-based sword fighting game where players battle each other to collect coins and grow their swords.

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
