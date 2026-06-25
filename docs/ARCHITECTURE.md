# Architecture

High-level system design. No secrets, no private keys, no deploy credentials.

---

## Components

```
┌─────────────────────────────────────────────────────────┐
│                     repfi.stream                        │
│                   (Next.js on Vercel)                   │
├─────────────────────────────────────────────────────────┤
│  Pages          │  API Routes         │  Static       │
│  /live          │  /api/auth/*        │  /docs        │
│  /register      │  /api/reward        │  /pool        │
│  /dashboard     │  /api/withdraw      │  images       │
│  /pool          │  /api/live/*        │               │
└────────┬────────┴──────────┬──────────┴───────────────┘
         │                   │
    Browser (athlete)    data/repfi.json
    ├── MediaPipe Pose  (users, balances, pool)
    ├── WebRTC stream
    └── httpOnly session cookie
```

---

## Data flow: verified rep

```
1. Athlete camera → MediaPipe landmarks (client)
2. PushUpTracker.process() → rep=true, inForm=true
3. POST /api/reward (session cookie, no body)
4. Server: verify session → rate limit → addReward(+1¢)
5. Persist to data/repfi.json
6. Response → update UI balance
```

---

## Data flow: withdrawal

```
1. Dashboard → POST /api/withdraw (session cookie)
2. Server: balance ≥ $1.00, pool ≥ balance
3. Deduct balance + pool
4. INTEGRATION: Solana transfer to user's public address
   (private key for pool wallet — server env only, never in client)
```

---

## What runs where

| Component | Location | Visible to user? |
|-----------|----------|------------------|
| Pose model | Browser (CDN) | Yes (skeleton overlay) |
| Rep counter logic | Browser JS bundle | Yes (obfuscated in build) |
| Session secret | Server env | **No** |
| User database | Server filesystem | **No** |
| Pool wallet key | Server env / HSM | **No** |
| Reward crediting | Server API | **No** |

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
| Docs theme | Nextra (internal fork in monorepo) |
| Pose AI | MediaPipe Pose via Tasks Vision CDN |
| Live video | WebRTC |
| Auth | scrypt + HMAC session cookies |
| Storage | JSON file (`data/repfi.json`) |
| Hosting | Vercel |
| Domain | repfi.stream |
| Chain | Solana |
| Token | $RepFi (pump.fun) |

---

## Environment variables (server only)

| Variable | Purpose |
|----------|---------|
| `AUTH_SECRET` | Session signing (required in prod) |
| `REPFI_DATA_DIR` | User database directory |
| `PUMP_MINT` | Token mint for commission panel |
| `PUMP_CREATOR_WALLET` | Fee-sharing wallet |

None of these are exposed to the client bundle.
