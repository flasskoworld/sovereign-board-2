# Sovereign Board — SE HQ

A single-file, location-themed interactive chess app. Play against an AI opponent
with five doctrine-based personas, or challenge a friend to a live multiplayer
game with a shareable link.

## What's in this repo

- **`index.html`** — the entire app. One self-contained file: all HTML, CSS, and
  JavaScript live in here. No build step, no dependencies to install. This is
  intentional — it keeps the project trivially deployable on any static host.
- **`firebase-rules.json`** — the Realtime Database security rules this app
  expects. Reference copy, not auto-applied — see Backend setup below.

## Deploying (GitHub Pages)

1. Push this folder to a GitHub repo (public — required for free Pages hosting).
2. In the repo: **Settings → Pages → Branch → `main` → / (root) → Save**.
3. GitHub will publish at `https://<username>.github.io/<repo-name>/` within
   a minute or two. Because the file is named `index.html`, that URL serves
   the app directly — no extra path needed.

Any other static host works too (Netlify, Vercel, Cloudflare Pages, etc.) —
just deploy this folder as-is, no build command required.

## Backend (Firebase Realtime Database)

Multiplayer (challenge links, live moves, presence, chat) runs through Firebase.
The project's config is already embedded directly in `index.html` — search for
`FIREBASE_CONFIG` near the top of the `<script>` block. Firebase web config
values are not secrets; they're safe to ship in client-side code. Access is
controlled entirely by the **database rules**, not by hiding this object.

**Current project:** `sovereignboard-74e2c` (Firebase Spark / free plan)

If you ever stand up a *new* Firebase project instead of reusing this one,
replace the `FIREBASE_CONFIG` values in `index.html` and re-publish these
rules in the Firebase Console under **Realtime Database → Rules**:

```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

This scopes open read/write access to just the `/rooms` path — the only
path this app touches. There's no user authentication system by design;
a challenge link's room ID functions as the access credential, the same
trust model as a Google Docs share link.

## Updating the live site

This is a static file with no build pipeline. To ship a change:

1. Replace `index.html` with the new version.
2. Commit and push (or re-upload via GitHub's web UI).
3. GitHub Pages picks it up automatically within a minute or two.

## Architecture notes (for whoever maintains this next)

- **Chess engine**: hand-written legal move generation (including castling,
  en passant, check/checkmate/stalemate detection) plus a minimax search with
  alpha-beta pruning for the CPU opponent. Search depth varies by location
  persona (1–3 ply) to create informal difficulty tiers.
- **Multiplayer**: Firebase Realtime Database, no auth. Moves, presence, and
  chat all live under `/rooms/{roomId}`.
- **No build tools, no package.json, no framework** — vanilla JS throughout.
  This is a deliberate simplicity choice, not an oversight.

— SE HQ
