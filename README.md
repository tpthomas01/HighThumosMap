# High Thumos Map

This is the static frontend for The High Thumos Brotherhood map. It shows member markers on a Leaflet world map, lets people search the brotherhood list, find nearby members, and link a Discord account to the right marker.

There is no build step and no package manager here. The app is two HTML files plus browser-loaded assets.

## What It Does

- Loads member data from the deployed backend.
- Draws member markers on an interactive Leaflet map.
- Provides a searchable member panel.
- Uses browser geolocation for "Find Brothers Near You".
- Sends users through Discord OAuth so they can claim/link their marker.
- Shows Discord username and avatar data on linked markers.

## Files

- `index.html` is the main app: map, search, nearby lookup, marker popups, and the Discord linking UI.
- `oauth-callback.html` handles the Discord redirect, asks the backend to exchange the auth code, stores the returned Discord user briefly, and sends the user back to the map.

## Run It Locally

Any static file server will work. For example:

```powershell
cd .\HighThumosMap
python -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

The map will still call the deployed backend at `https://discord-map-api.onrender.com`.

Local Discord OAuth testing needs a little more setup. Discord redirect URLs must match exactly, so the Discord app, `index.html`, and the backend config all need to agree on the same callback URL.

## Hardcoded Config

These values are currently embedded directly in the HTML:

- Discord client ID in `index.html`
- Discord callback URL in `index.html`
- Backend API base URL in `index.html` and `oauth-callback.html`
- Frontend home URL used after login in `oauth-callback.html`

If one of these changes, update the full chain at the same time. OAuth is strict about exact URLs.

## Backend Calls

`index.html` uses:

- `GET /data`
- `POST /api/link-discord`

`oauth-callback.html` uses:

- `POST /api/discord/token`
- `POST /api/discord/user`
- `GET /data`

## Deployment Notes

This site is meant for static hosting. GitHub Pages is enough as long as:

- `oauth-callback.html` is deployed at the exact URL registered in Discord.
- The backend allows CORS from the frontend origin.
- Any backend-side redirect or frontend URL config points back to this deployed site.

Browser geolocation only works after the user grants permission, and it behaves best over HTTPS.
