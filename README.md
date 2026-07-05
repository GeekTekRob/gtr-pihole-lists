# gtr-pihole-lists — Pi-hole usage

Quick steps to add these lists to a Pi-hole instance.

1) Get raw file URLs

2) Add blocklists in the Pi-hole web UI
# gtr-pihole-lists — Pi-hole usage

Quick steps to add these lists to a Pi-hole instance, plus a brief catalog of the categories and recommendations for each.

Getting raw URLs
- On GitHub open a file (for example `blacklists/streaming.txt`) and click **Raw** to copy the URL.
- Raw URL pattern: `https://raw.githubusercontent.com/OWNER/REPO/BRANCH/<path>`

Add blocklists (UI)
- Admin → Settings → Blocklists. Paste the raw URL for the categories you want and save. Then Update Gravity.

Add blocklists (CLI)
```bash
sudo sh -c 'echo "https://raw.githubusercontent.com/OWNER/REPO/BRANCH/blacklists/streaming.txt" >> /etc/pihole/adlists.list'
sudo pihole -g
```

Bulk-import whitelists
- Whitelist files in this repo are intended for review before importing. To bulk-import a whitelist remotely:
```bash
curl -sSL "https://raw.githubusercontent.com/OWNER/REPO/BRANCH/whitelists/facebook.txt" \
  | grep -vE '^\s*#|^\s*$' \
  | while IFS= read -r d; do sudo pihole -w "$d"; done
```

Catalog — what each list contains and when to enable it
- Blacklists:
  - `streaming.txt`: Ad and tracking domains used by streaming services (Peacock, Paramount). Enable if you want to reduce streaming ad/tracking traffic — may break some analytics or player features.
  - `ad-network.txt`: Generic ad network domains. Good for broad ad blocking; conservative enabling recommended if you rely on certain publisher functionality.

- Whitelists (short descriptions):
  - `facebook.txt`: Facebook and related CDN/graph domains required for Facebook, Instagram, Messenger features. Enable if users need Facebook services to function on your network.
  - `whatsapp.txt`: WhatsApp domains. Enable for WhatsApp web/notifications to work reliably.
  - `reddit.txt`: Reddit CDN and media hosts. Enable for proper Reddit content loading.
  - `amazon.txt`: Amazon service endpoints (device metrics, SDKs). Enable if you use Amazon services or devices (Fire/Prime/Kindle integrations).
  - `dc-dark-legion.txt`: Domains for KingsGroupGames (developer/game services). Enable for that game's functionality.
  - `streaming.txt` (whitelist): Streaming vendor endpoints (Netflix/Spotify/etc.) — use selectively to avoid interfering with service features.
  - `gaming-console.txt`: Grouped by platform (Xbox, PlayStation, Roblox, etc.) — enable only the platform(s) you need.
  - `mobile-ads.txt`: Mobile ad networks used by apps/games. Disable if you want aggressive mobile ad blocking; enable if apps break.
  - `misc-services.txt`: Misc vendor and telemetry endpoints (analytics, CDNs). Review before enabling.
  - URL shorteners and host-file sources: These entries are included in `whitelists/main.txt` (collapsed) to make them easy to copy; they are safe to allow when you trust shortened links or want blocklist sources reachable.

Conflict handling
- Whitelist entries are authoritative on Pi-hole when added via `pihole -w`. Remove conflicting entries from blacklists in this repo when identified.

Maintaining and deploying
- Use tagged releases or a stable branch in raw URLs for production Pi-holes.
- Prefer per-instance selection: Add only the categories required by each Pi-hole instance via the web UI.
- Use Pi-hole Groups (v5+) to assign different lists to different device groups.

Automation
- I can add a `deploy.sh` script that interactively lists available categories and:
  - adds selected blocklist raw URLs to `/etc/pihole/adlists.list`,
  - runs `pihole -g`, and
  - optionally bulk-imports whitelists.

Security note
- Only use raw URLs from a branch or tag you control. Avoid pointing production Pi-holes at `main` unless you trust ongoing changes.

If you want, I can add the `deploy.sh` to this repo now.

Example raw URLs (replace OWNER/REPO/BRANCH):
- `https://raw.githubusercontent.com/OWNER/REPO/BRANCH/blacklists/streaming.txt`
- `https://raw.githubusercontent.com/OWNER/REPO/BRANCH/blacklists/ad-network.txt`

3) Applying whitelists
- Pi-hole does not import whitelist files as blocklists. To bulk-import whitelist domains, SSH into your Pi-hole and run:
```bash
curl -sSL "https://raw.githubusercontent.com/OWNER/REPO/BRANCH/whitelists/facebook.txt" \
  | grep -vE '^\s*#|^\s*$' \
  | while IFS= read -r d; do sudo pihole -w "$d"; done
```
- Or add individual domains in the Admin UI under Group Management → Domains (or the Whitelist page depending on your Pi-hole version).

4) Best practices
- Use a stable branch or a tagged release in raw URLs to avoid accidental changes.
- Only add categories you want active — blocklists are per-Pi-hole and can be mixed differently across instances.
- For whitelists, prefer `pihole -w` so entries are authoritative over gravity.

5) Automation (optional)
- I can add a `deploy.sh` script that lists available categories and adds chosen raw URLs to `/etc/pihole/adlists.list` and optionally imports whitelists.

If you'd like, I will add a ready-to-run `deploy.sh` to this repo that:
- Lets you choose categories interactively,
- Adds blocklist URLs to Pi-hole,
- Runs `pihole -g`, and
- Optionally bulk-imports whitelist domains.# gtr-pihole-lists
Blocklists and whitelists for pi-hole above and beyond the majority lists for day to day processing.
