# 🌊 Wave Radio Relay Server

> Live audio relay — receives DJ audio via WebSocket, streams to 300+ listeners via HTTP

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/addo112/wave-radio-relay)

## Architecture

```
DJ Browser (mic) ──WebSocket──▶ This Server ──HTTP Stream──▶ Listeners
```

## Quick Start

```bash
npm install
npm start
# Server running at http://localhost:3000
```

## Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/stream` | GET | Audio stream (connect with `<audio>` element) |
| `/status` | GET | JSON: isLive, djName, showTitle, listeners count |
| `/health` | GET | Health check for deployment platforms |
| `/ws` | WebSocket | DJ broadcasts audio here |

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `3000` | Server port |
| `DJ_PASSWORD` | `waveradio2024` | Password for DJ authentication |
| `MAX_LISTENERS` | `500` | Maximum concurrent listeners |

## Deploy

### Render.com (Free)
Click the deploy button above, or:
1. Fork this repo
2. Go to [render.com](https://render.com) → New → Blueprint
3. Connect your GitHub repo
4. Deploy — `render.yaml` configures everything

### Railway
```bash
railway login
railway init
railway up
```

### Docker
```bash
docker build -t wave-radio-relay .
docker run -p 3000:3000 -e DJ_PASSWORD=yourpassword wave-radio-relay
```

## How It Works

1. DJ opens the dashboard (`dj.html`) and connects via WebSocket to `/ws`
2. DJ authenticates with `{ type: "auth", token: "password", djName: "DJ Name" }`
3. DJ starts broadcasting — MediaRecorder captures mic audio as WebM/Opus
4. Server receives audio chunks and pipes them to all connected HTTP listeners
5. Listeners connect to `/stream` — server sends init segment + live chunks
6. When DJ stops, all listener connections are cleanly closed
