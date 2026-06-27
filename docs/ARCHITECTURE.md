# Architecture

High-level system design. No secrets, no private keys, no deploy credentials.

---

## Components

```
┌─────────────────────────────────────────────────────────┐
│                     repfi.stream                        │
│                   (Next.js on Vercel)                   │
├─────────────────────────────────────────────────────────┤
│  Pages              │  API Routes           │  Static   │
│  /live              │  /api/auth/*          │  /docs    │
│  /register          │  /api/reward          │  images   │
│  /dashboard         │  /api/withdraw        │           │
│  /pool              │  /api/live/*          │           │
│  /leaderboard       │  /api/leaderboard     │           │
│  /profile           │  /api/athlete/*       │           │
└────────┬────────────┴──────────┬────────────┴───────────┘
         │                       │
    Browser (athlete)        MantleDB
    ├── MediaPipe Pose       (users, balances,
    ├── LiveKit publish       live rooms, payouts)
    └── httpOnly session
         │
    Browser (viewer)
    ├── LiveKit subscribe + audio
    └── MediaPipe on remote video (skeleton)
```

---

## Data flow: verified rep

```
1. Athlete camera → MediaPipe landmarks (client)
2. PushUpTracker → rep evidence (depth, elbow, timing)
3. POST /api/reward (session cookie + evidence JSON)
4. Server: verify session → rate limit → verify evidence → addReward(+1¢)
5. Persist to MantleDB (totalReps++, earnedCents++)
6. Response → update UI balance + leaderboard on next poll
```

---

## Data flow: leaderboard

```
1. GET /api/leaderboard (public, no auth)
2. Server: merge user store + live room map
3. Sort by totalReps DESC → return rows + version
4. Client polls ~1s; sessionStorage cache for instant re-display
```

---

## Data flow: athlete profile

```
1. GET /api/athlete/:nickname (public)
2. Server: user stats + rank + live room id
3. Client shows cached profile immediately; refreshes in background
```

---

## Data flow: withdrawal

```
1. Dashboard → POST /api/withdraw (session cookie)
2. Server: balance ≥ $1.00, pool wallet solvency check
3. Record payout, deduct balance (grow-only counters)
4. Solana transfer via encrypted payout key (server only)
5. On failure → revert balance, mark payout failed
```

---

## What runs where

| Component | Location | Visible to user? |
|-----------|----------|------------------|
| Pose model | Browser (CDN) | Yes (skeleton overlay) |
| Rep counter logic | Browser JS bundle | Yes |
| Rep credit decision | Server API | No |
| Session secret | Server env | **No** |
| User database | MantleDB | **No** |
| Payout signing key | Encrypted in MantleDB | **No** |
| Leaderboard data | Server API | Yes (public) |

---

## Repository split

| Repo | Purpose |
|------|---------|
| `repfi` (private) | Full Next.js app, API routes, deploy config |
| `repfi-public` (public) | Documentation, banner, transparency |

---

## Tech stack

| Layer | Technology |
|-------|------------|
| Framework | Next.js 16, React 19 |
| Docs theme | Nextra |
| Pose AI | MediaPipe Pose via Tasks Vision CDN |
| Live video | LiveKit (WebRTC) |
| Auth | scrypt + HMAC session cookies |
| Storage | MantleDB (remote KV) |
| Chain | Solana (`@solana/web3.js`) |
| Hosting | Vercel |
| Domain | repfi.stream |
| Token | $RepFi (pump.fun) |

---

## Environment variables (server only)

| Variable | Purpose |
|----------|---------|
| `AUTH_SECRET` | Session signing |
| `REPFI_MANTLE_KEY` | MantleDB access |
| `REPFI_MANTLE_NS` | MantleDB namespace |
| `ADMIN_CREDIT_SECRET` | Admin ops + key encryption |
| `LIVEKIT_*` | Live streaming tokens |
| `NEXT_PUBLIC_LIVEKIT_URL` | Client LiveKit endpoint |

None of these are exposed in the client bundle except the public LiveKit URL.
