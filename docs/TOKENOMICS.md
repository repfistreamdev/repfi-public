# RepFi Tokenomics

How money enters the system, how it is stored, and how it reaches athletes.

---

## Overview

RepFi has two funding sources for athlete payouts:

1. **Direct donations** to the global pool
2. **$RepFi token trading fees** (pump.fun creator fee share)

Athletes earn **$0.01 per verified rep**. Withdrawals are paid from the pool to the athlete's Solana address.

```
┌─────────────────┐     ┌──────────────────┐
│  Donors /       │     │  $RepFi trading  │
│  supporters     │     │  fees (pump.fun) │
└────────┬────────┘     └────────┬─────────┘
         │                       │
         └───────────┬───────────┘
                     ▼
           ┌─────────────────┐
           │  Global Pool    │  ← public at /pool
           └────────┬────────┘
                    │
         ┌──────────┼──────────┐
         ▼          ▼          ▼
    Athlete A   Athlete B   Athlete C
    balances    balances    balances
         │          │          │
         └──────────┴──────────┘
                    ▼
              Solana payouts
```

---

## $RepFi token

| Property | Value |
|----------|-------|
| Ticker | `$RepFi` |
| Network | Solana |
| Launch platform | pump.fun |
| Required to use RepFi? | **No** |

### Fee routing

When the token is live, trading activity on pump.fun generates **creator fees**. RepFi routes these fees into the donation pool that pays athletes.

The pool page shows:
- Total commission collected
- Amount streaming into the pool (unclaimed)
- Amount already routed (claimed)
- Token market cap (when available)

Before token launch, the commission panel shows **$0** (pre-launch preview mode).

---

## Donation pool

**Public page:** [repfi.stream/pool](https://repfi.stream/pool)

### Direct contributions

Anyone can contribute to the pool:

1. Enter amount (USD demo mode)
2. Optional display name
3. Pool balance updates

In production, contributions require **on-chain transfer confirmation** to the pool wallet before crediting.

### Pool wallet

The pool Solana address is displayed on the pool page. Always verify the address on the official site before sending funds.

---

## Athlete earnings

| Rule | Detail |
|------|--------|
| Rate | $0.01 / verified rep |
| Verification | AI pose tracking (not manual) |
| Minimum withdrawal | $1.00 |
| Payout destination | Solana address from registration |

### Solvency check

Withdrawals fail if `pool balance < athlete balance`. This prevents paying out money that does not exist in the pool.

---

## What we do not promise

- **Token price appreciation** — $RepFi is a community token, not an investment contract
- **Fixed APY or passive income** — earnings require verified physical reps
- **Unlimited liquidity** — payout speed depends on pool funding

---

## Transparency commitments

1. Pool balance visible on the website
2. Commission panel reads on-chain data when token is live
3. Donation feed shows recent contributions
4. Documentation published in this public repository

---

## Risk disclosure

| Risk | Mitigation / note |
|------|-------------------|
| Low pool liquidity | Withdrawals may be delayed; pool must be funded |
| Token volatility | DYOR; crypto markets are unpredictable |
| Demo mode | In-memory store may reset until production DB ships |
| Smart contract / pump.fun dependency | Fees depend on platform availability |

---

## Related docs

- [HOW_IT_WORKS.md](HOW_IT_WORKS.md)
- [WHY_NOT_A_SCAM.md](WHY_NOT_A_SCAM.md)
- [ROADMAP.md](ROADMAP.md)
