# RepFi — full project overview (A → Z)

## Mission

RepFi connects **physical effort** to **on-chain payouts**. The goal is simple: prove you did real work on camera, get paid fairly, with no opaque middlemen.

We are not a “move to earn” click farm. We are not an investment product. RepFi is a **performance bounty layer** for fitness tasks, starting with push-ups.

---

## Problem

1. **Creators and athletes** struggle to monetize short-form fitness content beyond ads.
2. **Viewers** want to support effort directly, not only through platforms that take large cuts.
3. **Crypto projects** often promise returns without verifiable work — damaging trust for legitimate apps.

## Solution

RepFi provides:

- **Live rooms** (10 simultaneous athletes)
- **AI pose verification** (MediaPipe Pose in the browser)
- **Instant micro-rewards** ($0.01 / verified rep)
- **Solana withdrawals** to a user-supplied address
- **Public donation pool** funded by community + $RepFi fees

---

## User roles

### Athlete (streamer)
Registers, joins a room, goes live, performs push-ups. Earns per verified rep.

### Viewer
Watches streams, chats, can donate to the global pool.

### Supporter / token holder
Buys or trades $RepFi; trading fees accrue to the pool that pays athletes.

---

## Core flows

### Registration
- Gmail only (`@gmail.com`)
- Password 5–15 chars
- Nickname 4–10 chars
- Solana address (public key only)
- Account is active immediately (no email OTP in current version)

### Live session
1. Athlete selects room 1–10
2. Browser requests camera + microphone
3. WebRTC publishes stream to viewers
4. Pose model runs client-side
5. Valid reps increment balance in real time

### Withdrawal
- Minimum balance: **$1.00** (100 cents)
- Payout sent to registered Solana address
- Requires sufficient pool liquidity

---

## AI verification (push-ups)

The tracker enforces:

- **Plank / push-up position** before counting
- **Hand placement** visible
- **Elbow angle** thresholds for bottom and top of rep
- **Side vs front camera** profiles with different strictness
- **In-form flag** — rep counts only when form is valid

This reduces “half reps” and arm-waving cheats.

---

## Money flow

```
Supporters / $RepFi trading fees
        ↓
  Global donation pool  ←── visible on /pool
        ↓
  Athlete balances (per verified rep)
        ↓
  Solana withdrawal
```

**Rate:** $0.01 per verified rep (configurable in product; documented as 1 cent).

---

## Technology

| Layer | Choice |
|-------|--------|
| Web app | Next.js 16, React 19 |
| Docs | Nextra |
| Pose AI | MediaPipe Pose (client-side) |
| Live video | WebRTC |
| API | Next.js Route Handlers |
| Chain | Solana |
| Token launch | pump.fun ($RepFi) |
| Hosting | Vercel |
| Domain | repfi.stream |

**Note:** Demo mode uses in-memory storage for accounts/pool. Production requires a real database and on-chain settlement hooks (documented in private codebase).

---

## Security principles

- Never collect private keys or seed phrases
- Server-side validation on registration fields
- Pose verification runs locally; server receives rep events after client checks
- Pool withdrawals gated by balance + pool solvency checks

---

## What we are NOT

- Not a hedge fund or staking vault
- Not MLM / referral-required earnings
- Not a wallet custodian (users hold their own keys)
- Not medical advice — consult a doctor before intense exercise

---

## Open vs closed source

| Repository | Visibility | Contents |
|------------|------------|----------|
| `repfistreamdev/repfi` | **Private** | Full application source |
| `repfistreamdev/repfi-public` | **Public** | Docs, images, transparency materials (this repo) |

---

## Links

- Live app: https://repfi.stream
- Documentation: https://repfi.stream/docs
- Pool: https://repfi.stream/pool
- Register: https://repfi.stream/register
