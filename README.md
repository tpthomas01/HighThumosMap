# Discord Member Map Frontend

Static frontend for the High Thumos Brotherhood map.

This app renders an interactive world map of members, supports search + nearby discovery, and lets members link their Discord account to their marker via the backend API.

## Files

- `index.html`: main map UI (Leaflet map, member list, search, nearby search, Discord link flow)
- `oauth-callback.html`: Discord OAuth redirect handler (exchanges code via backend, stores user info, redirects back)

## Features

- Interactive Leaflet map with custom marker icons
- Member list/search panel (compass toggle)
- Nearby member search using browser geolocation + selectable radius
- Discord account linking flow (OAuth redirect -> callback -> marker claim modal)
- Marker popups with Discord avatar + username (when linked)
- Auto-refresh of map data from backend (`/data`)

## Architecture (Frontend Side)

- Static hosting (currently GitHub Pages)
- Browser fetches data from backend API (`https://discord-map-api.onrender.com`)
- Discord OAuth starts in `index.html`
- Discord redirects to `oauth-callback.html`
- Callback page sends OAuth code to backend for secure token exchange
- Main page reads temporary `localStorage` values to complete linking UI

## Dependencies

This frontend does not use a build tool or package manager.

It loads external assets directly in the browser:

- Leaflet CSS/JS (CDN)
- Google Fonts
- External images/icons

## Local Development

Because this is a static site, you can serve it locally with any static web server.

Example (Python):

```powershell
cd .\discord-map-frontend
python -m http.server 8000
```

Then open:

- `http://localhost:8000/`

Notes:

- Discord OAuth callback URLs must exactly match what is configured in your Discord app.
- The current frontend is hardcoded for GitHub Pages + the deployed Render backend, so local OAuth testing requires updating those URLs.

## Configuration (Currently Hardcoded)

The following values are embedded directly in the HTML files and must stay aligned with backend + Discord app settings:

- Discord client ID (`index.html`)
- Frontend redirect URI / callback URL (`index.html`)
- Backend API base URL (`index.html`, `oauth-callback.html`)
- Post-login redirect back to frontend home (`oauth-callback.html`)

## Backend API Endpoints Used

From `index.html`:

- `GET /data`
- `POST /api/link-discord`

From `oauth-callback.html`:

- `POST /api/discord/token`
- `POST /api/discord/user`
- `GET /data` (to detect already-linked users)

## Browser Permissions / Storage

- Geolocation permission is required for the "Find Brothers Near You" feature.
- `localStorage` is used to temporarily persist Discord user/link state across the OAuth redirect.

## Deployment Notes

- Designed for static hosting (GitHub Pages works well).
- Ensure `oauth-callback.html` is deployed at the exact path configured in:
  - Discord Developer Portal OAuth redirect URI
  - frontend `index.html`
  - backend `generate_map.py`
- Backend must allow CORS for the frontend origin.

## Recommended Next Improvements (Optional)

- Move hardcoded URLs/client ID into a small config block at the top of each file
- Extract CSS/JS out of `index.html` into separate files for maintainability
- Add basic error UI for failed backend requests (instead of only alerts/console logs)
- Normalize file encoding to UTF-8 if special characters/emojis display incorrectly in some editors
