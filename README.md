# 7Ei IPTV Dashboard

Browse 11,500+ free IPTV channels from [iptv-org](https://github.com/iptv-org/iptv) directly in your browser.

**Live:** https://arturito7ei.github.io/7ei-iptv-dashboard/

---

## Setup

### 1. Configure API URL

Edit `config.js` to point to your API server:

```javascript
window.API_BASE = 'https://your-tunnel.trycloudflare.com';
```

For local development:
```javascript
window.API_BASE = '';
```

### 2. Enable GitHub Pages

1. Go to **Settings → Pages** in this repo
2. Source: Deploy from a branch → `main` / `(root)`
3. Save

Your site will be live at `https://arturito7ei.github.io/7ei-iptv-dashboard/`

---

## Architecture

```
GitHub Pages          →  Dashboard (static HTML/JS)
Mac Mini + cloudflared →  API server (port 8080)
                            └── /channels, /stats, /m3u/{country}, /api/sync
```

The dashboard runs entirely in the browser. It calls the API on your Mac Mini via the Cloudflare tunnel. **No data is stored on GitHub.**

---

## API Server (Mac Mini)

On the Mac Mini, run:

```bash
python3 skills/iptv-browse/scripts/api.py --port 8080
```

To auto-start on boot:
```bash
launchctl load ~/Library/LaunchAgents/ai.7ei.iptv-api.plist
launchctl load ~/Library/LaunchAgents/ai.7ei.iptv-tunnel.plist
```

---

## Channel Data Sync

From the dashboard: tap the **🔄 Update** button.

Or on the Mac Mini:
```bash
python3 skills/iptv-browse/scripts/sync.py --force
```

Data refreshes automatically weekly.

---

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /` | API info |
| `GET /channels?q=&country=&category=&limit=&offset=` | List/filter channels |
| `GET /channels/random?n=&country=&category=` | Random picks |
| `GET /channels/{name}` | Get one channel |
| `GET /stream/{name}` | Get stream URL |
| `GET /stats` | Stats overview |
| `GET /countries` | All countries + counts |
| `GET /categories` | All categories + counts |
| `GET /m3u/{country}?category=&q=` | Download M3U playlist |
| `GET /api/sync` | Trigger background re-sync |
| `GET /dashboard/` | Web dashboard |

---

## Tech Stack

- Dashboard: Vanilla HTML/CSS/JS, hls.js, dark theme
- API: Python 3 + FastAPI + uvicorn
- Tunnel: cloudflared (Cloudflare Tunnel)
- Data: iptv-org public M3U playlists
