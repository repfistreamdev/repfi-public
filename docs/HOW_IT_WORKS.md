# How RepFi Works

This document explains the full RepFi flow — from account creation to Solana payout — in plain English.

---

## 1. Registration

**URL:** [repfi.stream/register](https://repfi.stream/register)

| Field | Rules |
|-------|-------|
| Email | Gmail only (`@gmail.com`) |
| Password | 5–15 characters |
| Nickname | 4–10 characters, displayed on stream |
| Solana address | Your public payout address (paste only) |

**Important:**
- No wallet browser extension is required.
- We never ask for your seed phrase or private key.
- Your account is active immediately after signup (no email confirmation step in the current version).

After registration you land on the **Dashboard** with your live balance.

---

## 2. Live rooms

RepFi runs **10 simultaneous live rooms**. Each room supports:

- **1 athlete** (streamer performing the task)
- **Unlimited viewers** (watch + chat)

**Current task:** push-ups.

Room state (live / offline, viewer count, rep count) is visible on the [Live](https://repfi.stream/live) page.

---

## 3. Going live

When you start a session:

1. Browser requests **camera** and **microphone** permission.
2. Your video stream is published via **LiveKit** (WebRTC).
3. Viewers see your stream, hear your audio, and can chat in real time.
4. The **pose tracker** analyzes your body in the browser (skeleton overlay).
5. Viewers also see a skeleton overlay — MediaPipe runs on their device on your stream.

If you are not logged in, the site prompts you to [register](https://repfi.stream/register) first.

---

## 4. AI pose verification

RepFi uses **MediaPipe Pose** (client-side) to track **33 body keypoints** and overlay a skeleton on your video.

### What counts as a push-up?

The tracker enforces strict rules:

| Check | Purpose |
|-------|---------|
| Push-up / plank position | Reject standing or random arm movement |
| Hands visible | Prevent off-camera cheating |
| Elbow angle at bottom | Full depth required |
| Elbow extension at top | Full lockout required |
| Body alignment | Reduce hip sag and partial reps |
| Camera angle detection | Side vs front camera use different thresholds |

A rep is credited only when `rep === true` **and** `inForm === true`.

### Form hints

During live sessions the UI shows real-time hints (e.g. “go lower”, “straighten arms”) so athletes can correct form before reps count.

---

## 5. Earnings

| Parameter | Value |
|-----------|-------|
| Rate | **$0.01** per verified rep |
| Credit timing | Instant (live balance updates) |
| Minimum withdrawal | **$1.00** |

Balance is visible on your Dashboard and to viewers during your stream.

---

## 6. Withdrawals

From the Dashboard:

1. Ensure balance ≥ $1.00
2. Request withdrawal
3. Funds are sent to the Solana address on your profile

Withdrawals require **sufficient pool liquidity**. If the global donation pool cannot cover your balance, withdrawal fails with a clear pool error — not a silent failure.

---

## 7. Donation pool

**URL:** [repfi.stream/pool](https://repfi.stream/pool)

The pool funds all athlete payouts. It grows from:

1. **Direct donations** — supporters contribute via the pool page
2. **$RepFi trading fees** — pump.fun creator fee share (when token is live)

The pool total, commission panel, and recent contributions are **public on the website**.

See [TOKENOMICS.md](TOKENOMICS.md) for money flow details.

---

## 8. Viewers & chat

Viewers can:

- Watch any live room (video + **audio**)
- See the **AI skeleton overlay** on the athlete
- Send chat messages
- See rep count and athlete nickname
- Browse the [Leaderboard](https://repfi.stream/leaderboard) and open **athlete profiles**

Viewers do not earn from watching — earnings require performing verified tasks as the room athlete.

---

## 9. Leaderboard & profiles

**Leaderboard:** [repfi.stream/leaderboard](https://repfi.stream/leaderboard)

- All athletes ranked by **lifetime verified push-ups**
- Updates about **every second** during live sessions
- Shows nickname, reps, balance, and live status

**Athlete profile** (click any nickname):

- Unique avatar, rank, reps, balance, earned / withdrawn
- Public **Solana payout address** (copy + Solscan)
- Link to watch live if streaming

See [LEADERBOARD.md](LEADERBOARD.md) in this repo.

---

## 10. Architecture (high level)

```
Browser (athlete)
  ├── Camera / mic
  ├── MediaPipe Pose → rep detection + skeleton
  └── LiveKit → live stream

Browser (viewer)
  ├── LiveKit receive + chat + audio
  └── MediaPipe Pose → skeleton on remote video

Next.js API
  ├── Auth (register, session)
  ├── Live rooms (heartbeat, viewers, LiveKit tokens)
  ├── Rewards (credit balance per verified rep)
  ├── Leaderboard + athlete profiles (public read)
  ├── Pool (donations, on-chain balance)
  └── Withdraw (Solana payout)

MantleDB
  └── Users, balances, live state, encrypted secrets

Solana
  └── Payouts to user-supplied address
```

---

## 11. Production storage

User accounts, balances, rep counts, and live room state persist in **MantleDB**
(across server restarts and deploys). The donation pool balance is read from the
on-chain pool wallet on the [Pool page](https://repfi.stream/pool).

---

## Quick links

- [Leaderboard](https://repfi.stream/leaderboard)
- [LEADERBOARD.md](LEADERBOARD.md)
- [Getting Started](https://repfi.stream/docs/getting-started)
- [Pose Tracking](https://repfi.stream/docs/pose-tracking)
- [Earnings & Withdrawals](https://repfi.stream/docs/earnings)
- [Donation Pool](https://repfi.stream/docs/donation-pool)
- [FAQ](https://repfi.stream/docs/faq)
