# Auth deep-link infrastructure (website)

Phase 1: trust files + token-safe callback landing. App handlers are a later phase.

## Identifiers

| Platform | Value |
|---|---|
| iOS production app ID | `Z4H3ZLVKLS.com.mdiloreto.ProjectMomentum` |
| iOS Team ID | `Z4H3ZLVKLS` |
| iOS bundle ID | `com.mdiloreto.ProjectMomentum` |
| Android package | `com.wellbearing.app` |

Founder app IDs are **not** included.

## Callbacks

| Role | URL |
|---|---|
| Primary (Universal Link / App Link) | `https://wellbearing.app/auth/callback` |
| Fallback (custom scheme) | `wellbearing://auth/callback` |

Universal Links / Android App Links are primary. The custom scheme is fallback only.

## Files

| Path | Purpose |
|---|---|
| `.well-known/apple-app-site-association` | AASA — no file extension |
| `.well-known/assetlinks.json` | Android Digital Asset Links |
| `auth/callback/index.html` | Token-safe landing if the app does not open |
| `.nojekyll` | GitHub Pages must publish `.well-known` (Jekyll otherwise skips dotfiles) |

## Android SHA-256 (temporary)

Current `assetlinks.json` fingerprint:

`31:29:05:A7:93:CD:98:5A:1A:E6:CF:5C:97:79:50:D6:AD:3D:E2:4A:EE:8E:A9:EE:20:D5:7A:57:B4:37:7B:78`

**TEMPORARY FOR DEBUG VALIDATION ONLY.** This is the local Android debug keystore SHA-256.

**HUMAN REQUIRED:** After Play App Signing is enabled, add or replace with the **Google Play App Signing** SHA-256. Do not invent that value. Debug-only App Links will not verify for Play-distributed builds.

## Token safety

- Do not log, display, persist, or send auth tokens (query or fragment) to analytics, console, `localStorage`, or `sessionStorage`.
- The callback page uses `<meta name="referrer" content="no-referrer">`, no third-party scripts, and strips search/hash from the visible URL via `history.replaceState` without reading token values into logs.
- The “Open Well-Bearing” control uses a **static** `wellbearing://auth/callback` href and does **not** copy query/fragment onto the custom scheme.

## GitHub Pages header limitation

GitHub Pages cannot set custom `Content-Type` or `Cache-Control` for AASA. Apple typically accepts `application/json` or `application/octet-stream`. There is no `_headers` support on this host. If AASA verification fails, confirm the file is served at the exact path with **no HTML wrapper and no redirect**.

## Next phases (not this change)

- **iOS:** `applinks:wellbearing.app` associated domain; auth callback handler (do not treat as widget navigation).
- **Android:** verified `https` intent-filter (`autoVerify`, host `wellbearing.app`, path `/auth/callback`); auth session exchange.
- **Supabase:** allow `https://wellbearing.app/auth/callback` and `wellbearing://auth/callback`.
